---
title: Improving my website so changes deploy in 5 seconds ⚡️
summary: I just made big improvements to my blog server, allowing for almost instant deployments. Here is how I did it.
date: 2024-01-03
draft: false
tags:
  - Orangutan
  - Rust
  - Deployment
  - Clever Cloud
---

A few days ago, my blog stopped deploying, for an unknown reason. While trying to understand what was going on, I decided to make a few (big) improvements to [Orangutan], the server I created to handle authorization on my website. I am planning on writing an in-depth design explanation when I'll have implemented all the features I have in mind, but for now you only need to know that it's a [Rust] API which receives requests, verifies authorization and serves back the requested pages (or a [404]).

## How I got to this design {#how-i-got-there}

Before I added this authorization layer, my blog was just a [Hugo]-generated [static site]. When I changed files and pushed them, the website would be regenerated in an instant and made available about a minute later. This little delay happened because [a new virtual machine would have to boot][cc-deployment] and [the reverse proxy][sozu] would have to redirect traffic to it.

Adding support for authorization on my website meant adding some kind of dynamic program which would intercept the requests and decide what to do with them. At first I used [Apache's `.htaccess`][htaccess], but it wasn't a good developer experience and it was hard to maintain while ensuring no data could leak (through [Hugo] [aliases][hugo-aliases] for example), so I decided to write my own server in [Rust]. I obviously searched for an existing tool before starting this journey, but I couldn't find anything close to what I needed.

In [my first design][orangutan-initial-design], I was planning on creating an ever-running server which would read into an [object storage] every time a request came in. This had a lot of benefits, but also a lot of drawbacks. Without a cache, it would instantly react to website updates, but it would also be slow to answer because of the [proxy architecture].

Because it made deployments a lot more complex, I decided to change the design so the website would be generated locally. This meant adding [Orangutan] as a [submodule] and deploying the website as a [Rust application]. Before starting the API, a [pre run hook] would build the website (with the side benefit of ensuring it builds successfully), and then the API could simply serve the local files. This seems like a good plan, but it also caused really long deployments because every content file change would trigger an entire rebuild of the server. [Rust] code is fast to compile, but it still meant a few minutes of deployment and a lot of resources used for nothing, so there was room for improvements.

It worked well for a few months —where I decided to enjoy [my trip to Mexico] instead of indefinitely improving this server— but at some point deployments started failing and I could not post anything anymore. I couldn't understand where the bug came from so I needed to investigate closely, and I decided to use this occasion to implement the new design I had been thinking about.

## The new design {#new-design}

Now, by blog is just a public repository called "[blog-server]" containing [Orangutan] as a [submodule] and some configuration files. This is what [Clever Cloud] deploys, redeploying only when I update [Orangutan] (not on every content change anymore). When [`orangutan-server`] starts, it builds the website a first time (failing the deployment if it doesn't work). Then, a route is connected to a [GitHub push webhook] to rebuild the website when its content files are updated. [`orangutan-server`] pulls the new commit(s), rebuilds the website and can start serving the fresh files right after that.

While this process is going on, a lock blocks all requests, leaving them hanging until new files are available to avoid inconsistent states. For this reason, whenever new content files are pushed, any client accessing a page will have to wait a few seconds (around 5 in bad scenarios) but will get the new version right after. No unncessary build happens which now makes deployments as fast as they could be, as if there was no server in-between.

## What the problem was {#what-the-problem-was}

After completely changing how [Orangutan] worked and adding a lot of debug logs, deployments were still failing with no error log. I couldn't even [`ssh`][ssh] into the VM, which was very annoying. After a while, I had an epiphany: [Hugo] was taking more memory than the VM had, which prevented logs from being emitted and prevented me from opening a [Secure Shell][ssh]. I changed the size of the VM from [pico][cc-scaling] to [nano][cc-scaling] and the deployment worked 🥳 Unfortunately I can't benefit from [Clever Cloud's automatic application scaling][cc-scaling] as the website is generated at run time and I would run into an [out of memory (OOM) exception][OOM] every time it would be built.

One day™ I will make it so the website is generated by a [CI] and saved in an [object storage], which will allow me to scale down the VM to the bare minimum again.

## A few issues I still have to fix {#issues}

- As of today, [Orangutan] is not validating webhook deliveries, so any POST request to the route used by the [GitHub push webhook] causes a rebuild of the website. As it a lock blocks incoming requests while the website is rebuilding, malicious requests to this route would cause a [denial-of-service][DoS]. Fortunately, it's quite easy to [validate GitHub webhook deliveries] so I will probably fix this soon™.
- When I edit pages, the push webhook triggers a rebuild of the website, which starts by deleting the previously generated version and emptying caches. If I inserted something that broke website generation (e.g. a badly formatted [front matter] or a [broken link]), then the server can't deliver pages anymore and can only return [404]. Now that building is done at run time, I can't rely on [Clever Cloud]'s infrastructure to keep at least one working version running. I can fix this by keeping a backup of the website until the new build works and emptying caches only when a new website is about to be used. I will do it soon™.

[blog-server]: <https://github.com/RemiBardon/blog-server> "RemiBardon/blog-server | GitHub"
[broken link]: <https://gohugo.io/content-management/shortcodes/#ref-and-relref> "Shortcodes | Hugo"
[cc-deployment]: <https://developers.clever-cloud.com/doc/best-practices/blue-green/> "Blue/Green Deployments – Clever Cloud Documentation"
[cc-scaling]: <https://developers.clever-cloud.com/doc/administrate/scalability/> "Application scaling – Clever Cloud Documentation"
[CI]: <https://en.wikipedia.org/wiki/Continuous_integration> "Continuous integration | Wikipedia"
[Clever Cloud]: <https://www.clever-cloud.com/> "Home | Clever Cloud"
[DoS]: <https://en.wikipedia.org/wiki/Denial-of-service_attack> "Denial-of-service attack | Wikipedia"
[front matter]: <https://gohugo.io/content-management/front-matter/> "Front matter | Hugo"
[GitHub push webhook]: <https://docs.github.com/en/webhooks/about-webhooks> "About webhooks | GitHub Docs"
[htaccess]: <https://httpd.apache.org/docs/current/howto/htaccess.html> "Apache HTTP Server Tutorial: .htaccess files - Apache HTTP Server Version 2.4"
[Hugo]: <https://gohugo.io/> "The world’s fastest framework for building websites | Hugo"
[hugo-aliases]: <https://gohugo.io/content-management/urls/#aliases> "URL management | Hugo"
[my trip to Mexico]: <https://blog.remibardon.name/series/mexique-2023/> "Mexique 2023"
[object storage]: <https://en.wikipedia.org/wiki/Object_storage> "Object storage | Wikipedia"
[OOM]: <https://en.wikipedia.org/wiki/Out_of_memory> "Out of memory | Wikipedia"
[Orangutan]: <https://github.com/remibardon/orangutan> "RemiBardon/Orangutan: Lightweight authorization layer for static sites"
[orangutan-initial-design]: <https://github.com/RemiBardon/Orangutan/tree/main/design/v1> "Orangutan/design/v1 at main · RemiBardon/Orangutan"
[`orangutan-server`]: <https://github.com/RemiBardon/Orangutan/tree/main/src/orangutan-server> "Orangutan/src/orangutan-server at main · RemiBardon/Orangutan"
[pre run hook]: <https://developers.clever-cloud.com/doc/develop/build-hooks/#pre-run> "Deployment Hooks – Clever Cloud Documentation"
[proxy architecture]: <https://refactoring.guru/design-patterns/proxy> "Proxy | Refactoring and Design Patterns"
[Rust]: <https://www.rust-lang.org/> "Rust Programming Language"
[Rust application]: <https://developers.clever-cloud.com/doc/applications/rust/> "Rust – Clever Cloud Documentation"
[sozu]: <https://www.sozu.io/> "SŌZU - HTTP Reverse Proxy in Rust for Immutable Infrastructures"
[ssh]: <https://en.wikipedia.org/wiki/Secure_Shell> "Secure Shell | Wikipedia"
[static site]: <https://en.wikipedia.org/wiki/Static_web_page> "Static web page | Wikipedia"
[submodule]: <https://git-scm.com/book/en/v2/Git-Tools-Submodules> "Git - Submodules"
[validate GitHub webhook deliveries]: <https://docs.github.com/en/webhooks/using-webhooks/validating-webhook-deliveries> "Validating webhook deliveries | GitHub Docs"
[404]: <https://httpstatuses.io/404> "404 Not Found — httpstatuses.io"
