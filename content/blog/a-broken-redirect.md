+++
title = "A Broken Redirect - Why My Certificate Wouldn't Renew"
draft = false
date = "2017-02-21"
slug = "broken-redirect"
+++

## Summary

I was recently trying to renew the Let's Encrypt certificate for this site, but doing so failed with the following error (and an accompanying 404 Not Found):

``` plaintext
Cert is due for renewal, auto-renewing...
Renewing an existing certificate
Performing the following challenges:
http-01 challenge for lonesomecrowdedweb.com
http-01 challenge for www.lonesomecrowdedweb.com
Waiting for verification...
Cleaning up challenges
Attempting to renew cert from /etc/letsencrypt/renewal/lonesomecrowdedweb.com.conf produced an unexpected error: Failed authorization procedure. www.lonesomecrowdedweb.com (http-01): urn:acme:error:unauthorized :: The client lacks sufficient authorization

Domain: www.lonesomecrowdedweb.com
Type:   unauthorized
Detail: Invalid response from http://www.lonesomecrowdedweb.com/.well-known/acme-challenge/ ...
```

This was because my redirects in NGINX were not configured appropriately, and I didn't notice due to my use of [HTTP Strict-Transport-Security](https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/Strict-Transport-Security). Correcting the bindings allowed me to renew the certificate successfully.


## Background

In December 2016 I attended [DDD Brisbane](http://dddbrisbane.com/). One talk that I particularly enjoyed was [Joseph Cooney](https://jcooney.net) and [Leon Bambrick's](http://secretgeek.net) talk _Asp.net core on LINUX. It's the future. Let's dive right in!_

As a predominantly .NET developer I spend most of my time in a Windows environment with a lot of GUIs; but the speed and ease with which they were able to provision a functional, affordable environment was very enticing. Knowing that a personal blog/website was long overdue, I thought that was a good place to begin and something that could withstand the mistakes I was likely to make along the way.

I haven't spent much time developing in non-Windows environments before. Fortunately, Digital Ocean (the hosting provider featured in the earlier presentation) offers a number of easy tutorials on [setting up Ubuntu](https://www.digitalocean.com/community/tutorials/initial-server-setup-with-ubuntu-16-04), [installing NGINX](https://www.digitalocean.com/community/tutorials/how-to-install-nginx-on-ubuntu-16-04) and [securing it with Let's Encrypt](https://www.digitalocean.com/community/tutorials/how-to-secure-nginx-with-let-s-encrypt-on-ubuntu-16-04). Add on [a few extra changes from Leon](https://til.secretgeek.net/linux/nginx.html) and within a few short hours I had this very Hugo-powered site up and running with an A+ rating from the Qualys SSL Server Test.

![lonesomecrowdedweb.com A+ Qualsys SSL Server Test rating](/img/lonesomecrowdedweb-ssl-report.jpg)

Hitting the site over HTTP in Chrome would redirect me to HTTPS. The site has laid dormant and forgotten ever since, waiting for me to write some content. A few months later I would find the perfect opportunity.


## The Problem

A few weeks ago I received a reminder to renew my Let's Expiry certificate, but attempting to do so resulted in the aforementioned error. After googling for some quick tips, I started by clumsily navigating my way through the server and verified that the target `.well-known` folder was available.

``` plaintext
patrick@ubuntu-512mb-sgp1-01:/var/www/html/lonesomecrowdedweb.com$ ls -a
.   404.html  blog  .DS_Store  .gitkeep  index.html  sitemap.xml
..  about     css   fixed      img       index.xml   .well-known
```

No issues there. I took a closer look at the URL it was attempting to hit and realised it was using HTTP. [Another search](https://github.com/certbot/certbot/issues/1343) confirmed that it should be following 301 or 302 redirects, which I thought I had in place. Back to Chrome and:

``` plaintext
Request URL:http://www.lonesomecrowdedweb.com/
Request Method:GET
Status Code:307 Internal Redirect
```

No 301 or 302, but a 307 instead - not something I expected to encounter. [Some more research](https://www.seroundtable.com/google-307-http-strict-transport-security-19357.html) and it was starting to make sense. I had also enabled HSTS - a helpful mechanism for ensuring that browsers connect only using HTTPS - and Chrome was enforcing that by way of a 307 Internal Redirect. I confirmed this by visiting `chrome://net-internals/#hsts` and querying my domain:

``` plaintext
static_sts_domain: 
static_upgrade_mode: UNKNOWN
static_sts_include_subdomains: 
static_sts_observed: 
static_pkp_domain: 
static_pkp_include_subdomains: 
static_pkp_observed: 
static_spki_hashes: 
dynamic_sts_domain: lonesomecrowdedweb.com
dynamic_upgrade_mode: STRICT
dynamic_sts_include_subdomains: false
dynamic_sts_observed: 1487119538.699455
dynamic_pkp_domain: 
dynamic_pkp_include_subdomains: 
dynamic_pkp_observed: 
dynamic_spki_hashes: 
```

Deleting the domain didn't seem to be enough, as Chrome continued to enforce this policy. HSTS relies on having accessed the website previously, which I had done so in this browser many times. As such it was easiest just to try another browser - and it was a good thing I did:

``` plaintext
Welcome to nginx!

If you see this page, the nginx web server is successfully installed and working. Further configuration is required.

For online documentation and support please refer to nginx.org.
Commercial support is available at nginx.com.

Thank you for using nginx.
```

A dummy NGINX page in place of my site. Definitely not right - so I reviewed my NGINX configuration and found:

``` nginx
server {
        listen 80;
        server_name lonesomecrowdedweb.com www.lonesomcrowdedweb.com;
        return 301 https://$host$request_uri;
}
```

Can you spot the problem? It's subtle...

> www.lonesom**e**crowdedweb.com;

A simple typo! But why the generic NGINX page? Well, when setting up the configuration initially I used a sepearte server block for this domain and left the existing `default` block in place with the `default_server` option enabled. This was unnecessary (and confusing), so I disabled the default site, corrected the redirect and attempted another certificate renewal:

``` text
Cert is due for renewal, auto-renewing...
Renewing an existing certificate
Performing the following challenges:
http-01 challenge for lonesomecrowdedweb.com
http-01 challenge for www.lonesomecrowdedweb.com
Waiting for verification...
Cleaning up challenges
...
Congratulations, all renewals succeeded.
```

Much better! One last NGINX restart and my renewal completed successfully.


## Conclusion

A pretty simple mistake on my part and one that could have easily been avoided if I'd tested the redirects to begin with in a different browser, or on another machine, or with any real kind of scientific method - but an educational mistake all the same.