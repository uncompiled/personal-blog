---
date:  2016-07-03T00:00:00-04:00
title: "TodoMVC: An Evolution to Progressive Web App"
---

There's a lot of buzz around [Progressive Web Apps](https://developers.google.com/web/progressive-web-apps/)
lately and there's a good reason: _performance_.  Offline functionality is a big
selling point of [Service Workers](https://www.w3.org/TR/service-workers/),
but the same features that enable offline browsing (cache, fetch, background sync)
also enable consistent and reliable performance. In an era of abundant LTE and wifi,
network performance might not appear to be the highest priority, but real world
mobile networks are neither consistent nor reliable.

Service workers provide a programmable proxy that bridges the network
reliability gap and allows us to create reliable user experiences, even on flaky
mobile connections. Much like XHR and AJAX created a revolutionary change in the
way web applications were developed, service workers and progressive web apps
signal another pending change in web development.

### Revolutionary Ideas with Evolutionary Changes

[Initial examples of progressive web apps](https://pwa.rocks/), such as
[Airhorner](https://airhorner.com/), have typically taken the form of a 
standalone webapp.  This is a good demonstration of how PWAs can act like native
apps, but one thing that needs to be stressed is that PWAs are just web pages,
so we can take _any_ web site and turn it into a PWA.

To demonstrate this process, let's take the archetypical [TodoMVC](http://todomvc.com/)
and turn it into an offline-capable webapp.

### Step 0: Get the Source for the Website

We'll use the [React](https://facebook.github.io/react/) version as a starting 
point since React is currently quite popular. The React version of the TodoMVC
app can be found on [GitHub](https://github.com/tastejs/todomvc/tree/master/examples/react).
After making a local copy, you can test the webapp by running your favorite
HTTP server, such as `python -m SimpleHTTPServer` or [simplehttp2server](https://github.com/GoogleChrome/simplehttp2server).

**If you'd like to follow along, I've created a sample repo [here](https://github.com/uncompiled/todopwa/tree/0-unmodified/app).**

### Step 1: Create a Web App Manifest

[Web App Manifest](https://www.w3.org/TR/appmanifest/) is a feature that can 
be used with any website right now.  The Web App Manifest is a JSON file that
includes metadata about the application, such as the application's name and
launch screen icons, but it also allows the application to be scoped to a URL.

#### manifest.json:

{{< highlight json >}}
{
  "dir": "ltr",
  "lang": "en",
  "name": "React • TodoMVC",
  "short_name": "TodoMVC",
  "description": "Todo List Webapp",
  "scope": "/todo-pwa",
  "start_url": "/todo-pwa",
  "display": "fullscreen",
  "theme_color": "transparent",
  "orientation": "any",
  "background_color": "transparent",
  "related_applications": [],
  "prefer_related_applications": false,
  "icons": [
    {
      "src": "img/96x96.png",
      "sizes": "96x96",
      "type": "image/png"
    },
    {
      "src": "img/144x144.png",
      "sizes": "144x144",
      "type": "image/png"
    },
    {
      "src": "img/192x192.png",
      "sizes": "192x192",
      "type": "image/png"
    },
    {
      "src": "img/512x512.png",
      "sizes": "512x512",
      "type": "image/png"
    }
  ]
}
{{< /highlight >}}


Let's step through each of the manifest entries:

- **dir**: specifies the directionality of the text,
  such as left-to-right ("ltr") or right-to-left ("rtl").
- **lang**: specifies the primary language,
  such as English ("en") or Spanish ("es").
- **name**: specifies the full name of the application.
- **short_name**: specifies a short version of the application name,
  which can be displayed on the home screen.
- **description**: specifies the purpose of the application.
- **scope**: specifies the application context for the app.
  For most web sites, this is the root context ("/"), but this should
  be modified to your specific use case.
- **start_url**: specifies the URL that you want to load if the
  application is launched from the home screen. This can be different
  from the scope.
- **display**: specifies the display mode to use when the application
  is launched from the homescreen, such as "fullscreen", "standalone",
  "minimal-ui", or "browser".
- **orientation**: specifies the default orientation for the application,
  such as "landscape", "portrait", or "any".
- **theme_color**: specifies a default theme color for the application
- **background_color**: specifies a default background color when the 
  application is loading from the homescreen.
- **icons**: specifies a list of image icons that can be displayed
  on the user's homescreen.
- **related_applications**: specifies related native applications that
  may be installed on the user's device.
- **prefer_related_applications**: specifies a hint that a related native
  application should be preferred to the webapp.

**TOOL TIP**: _[webmanife.st](https://webmanife.st/) can help you automatically
generate your web app manifest.  It is a web-based interface for the
[manifestation](https://www.npmjs.com/package/manifestation) npm module._

#### Link to the manifest from your HTML:

Now that we have a manifest, we need to add it to the application HTML so the
browser can find it. If you named your manifest file `manifest.json`, 
add the following link tag to your application HTML's `<head>` tag:

{{< highlight html >}}
<link rel="manifest" href="manifest.json">
{{< /highlight >}}

This tells the browser to look for a file named `manifest.json`, which will
contain the web app manifest.

To optionally support older browsers, you can add some extra metadata to specify
the application name and associated icons.

{{< highlight html >}}
<!-- Fallback application metadata for legacy browsers -->
<meta name="application-name" content="TodoMVC">
<link rel="icon" sizes="96x96 144x144 192x192" href="img/192x192.png">
<link rel="icon" sizes="512x512" href="img/512x512.png">
{{< /highlight >}}

**The updates for the web app manifest are reflected [here](https://github.com/uncompiled/todopwa/tree/1-web-app-manifest/app).**

### Step 2: Configure the Service Worker

The next step is to introduce the [service worker](https://www.w3.org/TR/service-workers/) caching and fetch handling.
Before we jump into that, let's go over some Service Worker implementation
details:

- **HTTPS is a hard requirement.** The service worker registration
  method will fail without HTTPS. Service workers provide the ability to intercept
  and modify network requests on the client and without HTTPS, the application would
  be vulnerable to [Man-in-the-Middle](https://en.wikipedia.org/wiki/Man-in-the-middle_attack) attacks.
- **Same-origin rules apply to service worker registration.**  This means that 
  service workers can only be installed for the domain where the application is
  served. For example, `www.example.com` is different from `login.example.com`.
- **Service worker scope is limited to the installation context**. If you want
  the service worker to be installed for the entire domain, use the root ("/")
  context. If you place the service worker in a `js` sub-folder, it won't apply
  to URLs further up in the URL hierarchy. 

Implementing a service worker can be daunting, but thankfully, there is a
growing number of tools and resources to make this easier. One tool that I've
found incredibly useful is [sw-precache](https://github.com/GoogleChrome/sw-precache),
which is a node module that automatically generates a service worker that 
precaches resources. During development, it can be burdensome to manually
invalidate cached resources every time you make changes. By integrating
sw-precache into the build process, the service worker will automatically be
updated as a part of your build pipeline.

#### Generating the service-worker.js with sw-precache

sw-precache can be used within a Gulp task, but for simplicity, let's use the
command line interface. To install sw-precache, run `npm install -g sw-precache`.

Within the TodoMVC application folder, create a file named `sw-precache-config.json`:

{{< highlight json >}}
{
  "staticFileGlobs": [
    "img/**.png",
    "js/**.*",
    "node_modules/**/**.*",
    "index.html"
  ]
}
{{< /highlight >}}

**staticFileGlobs** is an array of string patterns that match resources you
want to automatically precache when the service worker is installed.

Run `sw-precache --config=sw-precache-config.json --verbose` to generate
`service-worker.js`.

#### Registering the Service Worker

Now that we have generated the service worker, we need to register the service
worker within the web site. Technically, this only needs to be done from the
entry point to the site. For a single page app, this is straightforward.
For an enterprise site with potentially hundreds of thousands (or millions) of
URLs, you may only need to register the service worker from the root ("/").
However, if your site contains multiple web applications installed at different
contexts, such as "/mail" and "/calendar", then you may want to register separate
service workers for each application.

In our TodoMVC example, open `index.html` and include the following `<script>`:

{{< highlight html >}}
<script>
if ('serviceWorker' in navigator) {
  navigator.serviceWorker.register('service-worker.js').then(function(reg) {
    reg.onupdatefound = function() {
      var installingWorker = reg.installing;
      installingWorker.onstatechange = function() {
        switch (installingWorker.state) {
          case 'installed':
            if (navigator.serviceWorker.controller) {
              console.log('New or updated content is available!');
            } else {
              console.log('Service worker is installed!');
            }
            break;
          case 'redundant':
            console.error('The installing service worker became redundant.');
            break;
        }
      };
    };
  }).catch(function(e) {
    console.error('Error during service worker registration:', e);
  });
}
</script>
{{< /highlight >}}

The important part is the `navigator.serviceWorker.register('service-worker.js')`
bit. This calls the browser's service worker registration method to
install the service worker, which is wrapped up in feature detection code,
so that older browsers don't explode. 

**The updated source code with the service worker can be found [here](https://github.com/uncompiled/todopwa/tree/2-service-worker/app).**

If we deploy the latest changes, we should see "Service worker is installed!" 
in the JS Console.  If we disable our network connection or switch to airplane
mode, the website should still work.

That's it! We've taken the TodoMVC app and made it work offline.

**Check it out on [GitHub Pages](https://uncompiled.github.io/todopwa/)!**

The next step would be to persist the tasks to a remote database, which could be
done using [background sync](https://developers.google.com/web/updates/2015/12/background-sync?hl=en).
However, I'll leave that topic for another post.