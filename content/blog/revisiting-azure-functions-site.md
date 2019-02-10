+++
title = "Revisiting a Site Built On Azure Functions"
draft = false
date = "2018-01-03"
slug = "revisiting-a-site-on-azure-functions"
+++

## Summary

Last year I blogged about [Building a Small Site on Azure Functions](https://www.lonesomecrowdedweb.com/blog/site-on-azure-functions/). It was an interesting experiment, though not a serious solution I'd recommend to anyone based on that experience. Still, the site has been running away in the background serving the _very_ occasional visitor since then at the cost of just a few cents per month.

At some point in the last month or two (some form of monitoring would have helped here) the site stopped working. Based on a quick search I _think_ this might be due to do some breaking changes in Azure Function Proxies (a preview technology) and its handling of catch-all routes, but I'm not entirely sure.

So I'm using this as an opportunity to do a few things:

* Get the site functioning again
* Upgrade to the latest Azure goodies (runtime, etc)
* Leverage the latest tooling that's available
* Attempt to solve some of those lingering performance issues (e.g. slow start time when accessing the site for the first time after a period of inactivity)

## Leveraging Visual Studio

The first step was to create a new Visual Studio solution to bundle all the different parts that I had previously managed directly through the Portal. At the time of writing I'm using Visual Studio 2017 (`v15.5.2`) with the Azure Functions and Web Jobs Tools (`v15.0.31201.0`).

When creating a new Azure Functions project I'm presented with two options:

* Azure Functions v1 (.NET Framework)
* Azure Functions v2 (.NET Core)

The former is Generally Available (GA) while the latter is still in Preview. I wanted to give v2 a try despite its status to save me from upgrading later and to experience the latest and greatest offering. I went down this path for awhile until I got stuck and actually stopped to read the [Azure Functions runtime 2.0 known issues](https://github.com/Azure/azure-webjobs-sdk-script/wiki/Azure-Functions-runtime-2.0-known-issues). The important one for us is that proxy functions are not yet supported in v2 - so we have to stick with v1 for now.

Pulling in my existing files and converting my `csx` files to `cs` files was straightforward and left me with the following:

![isitinthecloud Visual Studio solution](/img/revisiting-azure-functions-site_1.png)

The `letsencrypt` function has been disabled with an attribute and the content files (such as `index.html`) are set to copy to the output directory. As before the [source code is available](https://github.com/PJohannessen/isitinthe.cloud).

## Configuring and deploying

I started by having a look through the Portal to see whether there were any noteworthy settings we might want to tinker with. There were a few, including:

* Setting edit mode to Read Only (forcing me to deploy changes through my IDE or source control)
* Enabling HTTPS Only for my custom host name

Visual Studio's "right click publish" works great here for this hobby project and allows me to deploy everything in one hit to my existing Function App.

## Testing

Now that everything has been updated and deployed we can see if the site is accessible again. Unfortunately, it is not - while hitting the function directly works, trying to access it through the root domain does not.

With some more googling I was able to find a [similar issue report](https://github.com/Azure/Azure-Functions/issues/572). Their solution was to explicitly name the wildcard portion of their match condition, so I made a similar change to my `proxies.json` from:

``` json
"ContentProxy": {
    "matchCondition": {
    "route": "*"
    },
    "backendUri": "https://isitinthecloud.azurewebsites.net/api/Content"
}
```

To:

``` json
"ContentProxy": {
    "matchCondition": {
    "route": "{*route}"
    },
    "backendUri": "https://isitinthecloud.azurewebsites.net/api/Content"
}
```

And success! The site is functioning again. So our theory from a quick initial search was correct.

## Performance

The site is functioning again, we're on the latest runtime/version that meets our needs and we've built and deployed our solution using the latest tooling - all that's left is to see how quickly the site spins up. This proved to be difficult to measure as the poor startup time I've noticed is intermittent and not directly related to the execution time of the function itself, which is generally taking no more than 250 ms to execute. [I'm not the only one who has noticed this](https://stackoverflow.com/questions/45448040/why-do-azure-functions-take-an-excessive-time-to-wake-up). This behaviour of Function Apps on a consumption plan makes sense, but I'm not interested in the costs associated with a full app service plan. I've seen a few suggestions such as [warming it up beforehand](https://blog.kloud.com.au/2017/11/04/azure-functions-cold-start-workaround/) (not suitable for our needs) or adding an additional function with a 4 minute timer to keep it warm. Such a function would likely incur an additional (though quite possibly very small) cost.

To try and solve the measuring problem I've set up a monitor in [Uptime Robot](https://uptimerobot.com), a simple but useful tool I've used in the past to alert me to website outages. I've increased it from the default 5 minute check to a 30 minute check, both to avoid hitting the function too much and to ensure the function app has time to "fall asleep". The site is accessed infrequently enough that it is unlikely to be woken up from any other source except our monitor.

After 24 hours the results on the monitor were as follows:

![isitinthecloud uptime monitor without timer function](/img/revisiting-azure-functions-site_2.png)

Roughly seven and a half seconds when accessed cold with several instances of it already warm. This should act as a suitable baseline for determining whether adding an additional timer functional helps keep our Function App warm. I've gone ahead and created such a function with the aforementioned 4 minute timer:

``` csharp
public static class Ping
{
    [FunctionName("Ping")]
    public static void Run([TimerTrigger("0 */4 * * * *")]TimerInfo myTimer, TraceWriter log)
    {
        log.Info($"C# Timer trigger function executed at: {DateTime.Now}");
    }
}
```

After a few days of this, the monitor reports the following:

![isitinthecloud uptime monitor with timer function](/img/revisiting-azure-functions-site_3.png)

As you can see, our average respond time has dropped to half a second, suggesting that the additional function has succeeded in improving the performance of our site.

After a few days of this I haven't noticed any measurable impact to the cost of my app, but the spend for this isn't clear at the best of times, so trial at your own risk.