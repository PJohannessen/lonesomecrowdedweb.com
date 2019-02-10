+++
title = "isitinthe.cloud -  Building a Small Site on Azure Functions"
draft = false
date = "2017-04-16"
slug = "site-on-azure-functions"
+++

## Summary

I recently stumbled upon [Is it on AWS?](https://isitonaws.com/) and [its accompanying backstory](https://aws.amazon.com/blogs/aws/is-it-on-aws-domain-identification-using-aws-lambda/). It sounded like a fascinating way to create a small, focused website. I thought it would be a positive learning experience to create a similar site with Microsoft Azure.

Introducing [IsItInThe.Cloud](https://www.isitinthe.cloud)

This blog will detail the steps taken to create the site; but first, a few notes:

* Credit to [Is it on AWS?](https://isitonaws.com/) for the initial inspiration.
* The site is a relatively quick-and-dirty implementation and nowhere near as robust as its source of inspiration.
* The check is done by a simple IP Address comparison between a site's DNS lookup and [Microsoft's list of Azure Datacenter IP Ranges
](https://www.microsoft.com/en-au/download/details.aspx?id=41653). But there are many reasons they may not match, so it should not be considered highly accurate.
* IPv6 is not currently supported - I wasn't able to locate any published IPv6 ranges for Azure. Please point me in their direction if you happen to know where to find them!
* I don't have a lot of experience with Azure, so I welcome any feedback on any improvements, platform features I haven't used appropriately, etc.
* This is probably not a sensible way to host a website - at least not yet anyway - but you be the judge.
* [The source code is available.](https://github.com/pjohannessen/isitinthe.cloud)

If you're unfamiliar (as I am!) with the idea of Serverless Computing, I'd encourage you to start by reading [this article](https://martinfowler.com/articles/serverless.html) by Mike Roberts or the [Azure Functions documentation](https://azure.microsoft.com/en-au/services/functions/).

## Getting Started

The first step is to create an Azure Function app. In the past this was done through a dedicated [functions portal](https://functions.azure.com/), but has [very recently](https://blogs.msdn.microsoft.com/appserviceteam/2017/04/10/new-integrated-portal-for-azure-functions/) been streamlined with the primary portal. Navigate to [New > Function App](https://portal.azure.com/#create/Microsoft.FunctionApp) to create the app. 

![Create function app](/img/azure-functions-site_1.png)

## Preparing our checklist of IPs

As stated earlier, Microsoft provides a weekly [list of IP ranges](https://www.microsoft.com/en-au/download/details.aspx?id=41653) for its Azure datacenters in an XML document, broken down first by region and then by IP range in [CIDR notation](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing). Not the easiest format to work with. This in itself would be a great opportunity to employ a function to obtain, convert and apply the latest IP ranges each week - but this is not essential for now, so a simple LINQPad script is sufficient.

``` csharp  
void Main()
{
	XDocument document = XDocument.Load("PublicIPs_20170328.xml");
	var regions = document.Root.Elements("Region");
	var ranges = regions.SelectMany(r => r.Elements("IpRange").Select(ipr =>
	{
		var subnet = ipr.Attribute("Subnet").Value;
		IPNetwork ipnetwork = IPNetwork.Parse(subnet);
		string firstUsableUint = IpToInt(ipnetwork.FirstUsable.ToString()).ToString();
		string lastUsableUint = IpToInt(ipnetwork.LastUsable.ToString()).ToString();
		return new
		{
			region = r.Attribute("Name").Value,
			subnet = subnet,
			lower = firstUsableUint,
			upper = lastUsableUint
		};
	}));
	string json = JsonConvert.SerializeObject(ranges);
	System.IO.File.WriteAllText(@"AzurePublicIPs.json", json);
}

uint IpToInt(string ip)
{
	IPAddress address = IPAddress.Parse(ip);
	byte[] bytes = address.GetAddressBytes();
	if (BitConverter.IsLittleEndian)
		Array.Reverse(bytes);
	uint intAddress = BitConverter.ToUInt32(bytes, 0);
	return intAddress;
}
```

I've used a uint to store the lower and upper-bound thresholds for a range, which is enough for these 32-bit IPv4 addresses - but that would need to be improved when and if we are to support 128-bit IPv6 addresses.

Also of note is that there doesn't seem to be any native .NET libraries for working with CIDR notation, so I've included the [IPNetwork2 library](https://github.com/lduchosal/ipnetwork) to assist.

The result is a single JSON file containing a single flat array of ranges like so:

``` json 
{
    "region": "europewest",
    "subnet": "40.112.124.0/24",
    "lower": "678460417",
    "upper": "678460670"
}
```

## Building the function

Back in the portal again, it's time to create the function that will actually serve our website. Starting with the `HttpTrigger-CSharp` template and an `Anonymous authorization` level, I've created a function named `Content`. For an initial request of the webpage, it does roughly the following:

1. Reads a local index.html file;
2. Creates an initial HTTP response;
3. Sets the content of the response to the content of our HTML file; and
4. Returns our response.

On this initial page an end user can specify a single URL, hostname or IP to check. This time our function does the following:

1. Reads a local `index.html` file;
* Creates an initial HTTP response;
* Performs a DNS lookup for the relevant IP address (if a URL or hostname is specified);
* Loads up our list of Azure IP ranges in to memory
* Compares the IP address with the list of Azure IP ranges
    * If it's a match, update the HTML page with the details of match.
    * If it's not a match, update the HTML page accordingly.
    * Otherwise, something went wrong (invalid input, DNS lookup failure, etc) and we set a generic error message.
* Sets the content of the response to our modified HTML file; and
* Returns our response.

``` csharp
using System.Net;
using System.Text;
using Newtonsoft.Json;

public static HttpResponseMessage Run(HttpRequestMessage req, TraceWriter log)
{
    string responsePage = File.ReadAllText(Path.Combine(GetScriptPath(), @"Content\index.html"));
    var response = new HttpResponseMessage(HttpStatusCode.OK);

    string lookup = req.GetQueryNameValuePairs()
        .FirstOrDefault(q => string.Compare(q.Key, "lookup", true) == 0)
        .Value;

    if (!string.IsNullOrEmpty(lookup)) {
        bool azureMatch = false;
        uint uintAddress = 0;
        IPAddress address;
        
        try {
            Uri uri;
            if (!IPAddress.TryParse(lookup, out address)) {
                string lookupValue = lookup;
                if (Uri.TryCreate(lookup, UriKind.Absolute, out uri)) {
                    lookupValue = uri.Host;
                }
                var hostEntry = System.Net.Dns.GetHostEntry(lookupValue);
                address = hostEntry.AddressList[0];
            }
            
            var ipAddress = address.MapToIPv4();
            uintAddress = IpToInt(ipAddress.ToString());
            string ipFilePath = Path.Combine(GetScriptPath(), @"Content\AzurePublicIPs.json");
            var ranges = JsonConvert.DeserializeObject<List<IpRange>>(File.ReadAllText(ipFilePath));
            var matchingRegion = ranges.FirstOrDefault(r => uintAddress >= r.Lower && uintAddress <= r.Upper);
            if (matchingRegion != null) azureMatch = true;
            if (azureMatch) {
            responsePage = responsePage.Replace(
                "<div id=\"placeholder\"></div>",
                $"<div id=\"match\">Yes, it looks like {WebUtility.HtmlEncode(lookup)} ({ipAddress}) is hosted on Azure!<br />Region: {matchingRegion.Region}<br />CIDR: {matchingRegion.Subnet}</div>");
            } else {
            responsePage = responsePage.Replace(
                "<div id=\"placeholder\"></div>",
                $"<div id=\"nomatch\">No, it looks like {WebUtility.HtmlEncode(lookup)} ({ipAddress}) is not hosted on Azure.</div>");
            }
        } catch (Exception e) {
            log.Info(e.Message);
            responsePage = responsePage.Replace(
                "<div id=\"placeholder\"></div>",
                "<div id=\"error\">An error occured. Please ensure a valid URL, hostname or IP was provided.</div>");
        }
    }

    response.Content = new StringContent(responsePage, Encoding.UTF8, "text/html");
    return response;
}

private static string GetScriptPath()
    => Path.Combine(GetEnvironmentVariable("HOME"), @"site\wwwroot");

private static string GetEnvironmentVariable(string name)
    => System.Environment.GetEnvironmentVariable(name, EnvironmentVariableTarget.Process);

static uint IpToInt(string ip)
{
	IPAddress address = IPAddress.Parse(ip);
	byte[] bytes = address.GetAddressBytes();
	if (BitConverter.IsLittleEndian)
		Array.Reverse(bytes);
	uint intAddress = BitConverter.ToUInt32(bytes, 0);
	return intAddress;
}

public class IpRange
{
	public string Region { get; set; }
	public string Subnet { get; set; }
	public uint Lower { get; set; }
	public uint Upper { get; set; }
}
```

## Exposing the function

Now that our function is complete, we need a way of exposing it to the world. Let's start by making it accessible on the default [isitinthecloud.azurewebsites.net](http://isitinthecloud.azurewebsites.net/) we've been provided.

![isitinthecloud.azurewebsites.net/api/Content home page](/img/azure-functions-site_2.png)

![isitinthecloud.azurewebsites.net/api/Content lookup](/img/azure-functions-site_3.png)

By default our function is accessible at `/api/Content`, but what we really want is to serve requests on the root domain. Removing the `/api` component can be done by editing the `host.json` file contained within the `wwwroot` folder of our App Service. I did this by using the App Service Editor.

``` json
{
  "http": {
    "routePrefix": ""
  }
}
```

Getting rid of the `/Content` portion is not as straightforward. By default there is a "Your Function App is up and running" placeholder at the root of our site. It can apparently be disabled with a `AzureWebJobsDisableHomepage` flag in our app settings, but this didn't seem to do this trick; neither did changing the route of the function to accept just an optional, dummy parameter.

To solve this I opted in to the new [Azure Functions Proxies](https://docs.microsoft.com/en-us/azure/azure-functions/functions-proxies) functionality (currently in preview). Creating a function proxy with a `*` route and backend URL `http://isitonazure.azurewebsites.net/Content` allows us to expose our site the way we want. Here is our resulting `proxies.json` and website:

``` json
{
    "proxies": {
        "ContentProxy": {
            "matchCondition": {
                "route": "*"
            },
            "backendUri": "http://isitinthecloud.azurewebsites.net/Content"
        }
    }
}
```

![isitinthecloud.azurewebsites.net home page](/img/azure-functions-site_4.png)

A custom domain is much nicer than the default we're provided. Rather than detailing this process in its entirity, you can do what I did and [follow the documentation](https://docs.microsoft.com/en-us/azure/app-service-web/web-sites-custom-domain-name) provided. Without too much trouble we arrive at:

![isitinthe.cloud home page](/img/azure-functions-site_5.png)


## Applying HTTPS

Our site is now up and running and we could stop here - but it's 2017 and it's never been easier to obtain an SSL certificate. [Let's Encrypt](https://letsencrypt.org/) to the rescue!

While Azure (sadly) doesn't have any native one-click setup for obtaining a Let's Encrypt cert, they [appear to be content with a community-provided solution](https://feedback.azure.com/forums/169385-web-apps/suggestions/6737285-add-support-for-free-ssl-certs-like-those-from-let). Once again, rather than stepping through the process in its entirity I recommend following an existing tutorial. I used [this blog](http://bigfontblog.azurewebsites.net/install-a-letsencrypt-ssl-certificate-into-an-azure-app-service/) by Shaun Luttin (which itself is a set of high-level notes from [Troy Hunt](https://www.troyhunt.com/everything-you-need-to-know-about-loading-a-free-lets-encrypt-certificate-into-an-azure-website/) and [the official documentation](https://github.com/sjkp/letsencrypt-siteextension/wiki/How-to-install)). This worked equally well for our Function App right up until the point of actually obtaining our certificate, at which point we run in to a problem.

Let's Encrypt needs to verify the authenticity of our request by exposing a series of files at the `/.well-known/acme-challenge/` path of our site. But our Function App isn't set up to serve just any arbitrary file. I expect this could be solved with a temporary site to serve the files, but I wanted to try solving it with another function instead. A second function with a route template of `.well-known/acme-challenge/{challengeId}` and the following code does the trick:

``` csharp
using System.Net;
using System.Text;

public static HttpResponseMessage Run(HttpRequestMessage req, string challengeId, TraceWriter log)
{
    string responsePage = File.ReadAllText(Path.Combine(GetScriptPath(), @".well-known\acme-challenge\" + challengeId));
    var response = req.CreateResponse(HttpStatusCode.OK);
    response.Content = new StringContent(responsePage, Encoding.UTF8, "text/html");
    return response;
}

private static string GetScriptPath()
    => Path.Combine(GetEnvironmentVariable("HOME"), @"site\wwwroot");

private static string GetEnvironmentVariable(string name)
    => System.Environment.GetEnvironmentVariable(name, EnvironmentVariableTarget.Process);
```

To make this accessible I had to temporarily disable the earlier proxy function while the certificate was obtained. I also disabled this function immediately afterwards, as in its current form I suspect it's open to [Path Traversal attacks](https://www.owasp.org/index.php/Path_Traversal).

![https://www.isitinthe.cloud home page](/img/azure-functions-site_6.png)

## Conclusion

Our site is now complete! There appears to be a warm-up time before the site is accessible after any period of activity, and I'd love to eliminate or minimise this if possible. Likewise there's an odd `ms-ext-routing` error that pops up from time to time which requires a page refresh to resolve.

I can already think of a number of other changes or improvements that could be made to the site, such as:

* Automating more activities (such as retrieving the latest Azure IP ranges);
* Improving the codebase and introducing some unit tests (this doesn't seem to be straightforward with Azure Functions);
* Profiling and improving the performance of the site (this can lead to direct cost savings!);
* Look at [publishing the code as a class library](https://blogs.msdn.microsoft.com/appserviceteam/2017/03/16/publishing-a-net-class-library-as-a-function-app/) (in theory, this will help with both of the above);
* [Continuous deployment](https://docs.microsoft.com/en-us/azure/azure-functions/functions-continuous-deployment) - given that this restricts the editing of functions in the portal, I'm hesitant to do this until testability is improved;
* IPv6 support for Azure; and
* Support for checking other cloud hosting providers.

Developing this site has certainly helped me understand the potential of an offering like this. I would consider using functions again in future once the workflow behind developing, testing and deploying the functions is a bit more flexible - for instance, I'd love to use Visual Studio Code on any O/S rather than full-blown Visual Studio.

Again, the full source code is [available on my GitHub](https://github.com/pjohannessen/isitinthe.cloud).