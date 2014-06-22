Don't awaken the PaaS giants
===

A few years ago, application servers were all the rage.
I want to give you a warning about how the architecture concepts of the app server is yet again rising in the current PaaS ecosystem.


A small step for man, a big step for MegaCorp®
----

The app server made your development process *easier*. The app server would provide a pre-configured runtime environment, all set up with clustering, load balancing and failover mechanisms.
Database drivers and connection pools were provided, as were authentication services and user management, caching services and message queues. What else, you name it! For the *ops* guys, the app server provided centralized monitoring, configuration and administration.

All of these services would be deeply integrated in the platform, for ease of use. Ah, what glory! The app server was the silver bullet we needed!


The pendulum swings
---

However, as most programmers [wisely figured out](lenke til tech radar?), a heavy-weight app server placed restrictions on your application that made development slow down.
Deploys were painstakingly slow, your classloader had a will of its own, memory consumption went through the roof, and the upgrade process was a dreaded nightmare.
And the worst part: The vendor lock-in was real. The strengths of the app server eventually led to it's downfall.

As an alternative to the ever-popular enterprise-required app server, developers started thinking minimalistically.
 - Instead of deploying your app to a managed container, you would embed a light-weight web server.
 - For authentication you could choose any SSO solution, like [OpenAM](http://forgerock.com/products/open-identity-stack/openam/).
 - Caching was provided by Terracotta [Ehcache](http://ehcache.org/) or a standalone [Memcached](http://memcached.org/) server.
 - You would use [ActiveMQ](http://activemq.apache.org/), [RabbitMQ](https://www.rabbitmq.com/) or similar as your message queue.
 - For your monitoring needs you would use a combination of tools:
    - [Nagios](http://www.nagios.org/) or [Munin](http://munin-monitoring.org/) for the servers.
    - Database monitoring would be done with the tools your database shipped with.
    - For your application, you would create light-weight custom monitoring, perhaps based on [Dropwizard](http://dropwizard.codahale.com/).
 - The database(s) could be chosen based on which product was best fit for the job; goodbye vendor lock-in!

In this kind of ecosystem it's far easier to develop slim, lightweight [twelve-factor](http://12factor.net/) apps.
The long-term result is increased development speed, reduced maintenance costs, and a team of happy developers.


Meanwhile, in ops town
---

Over time, ops people figured out that provisioning servers &mdash; a tedious and error prone task &mdash; should be fully automated.
With the rise of virtualization support at the hardware and software level, sharing servers could finally be done efficiently.
Tools like [Vagrant](http://www.vagrantup.com/) and [Puppet](http://puppetlabs.com/) let you automatically set up your server from scratch, just the way it's supposed to be.

From there, it's a small step to create a self-serve API for provisioning a *whole new* server. An on-demand server! Infrastructure as a service!
Within the enterprise, the servers are fairly homogenously configured. The block devices and file systems, networking configurations and firewall rules, they can all be set up with a sensible default configuration.
This is all well. The ops guys save time on provisioning new servers for the greedy developers. The developers get their servers quickly, and the servers *just work*. Nice!


Revenge of the developers
---

What happened next? Developers got jealous. They turned it up to 11. They made the *platform* as a service.  
The PaaS is a miraculous invention. It provides you with a a ton of functionality, including:
 - An execution runtime environment
 - Automated scaling
 - A database
 - A message queue
 - A load balancer
 - A log aggregator
 - A monitoring dashboard
 - etc.

*Wait, what? Exactly.* The pendulum has swung back.
The app server provided tight integrations with every resource. We are seeing its renaissance in the PaaS ecosystem.  
It should not be surprising that most PaaS solutions are backed by software giants. Giants that still have not accepted the downfall of the app server. Giants that profit heavily from vendor lock-in, and expensive support licenses.

In all fairness, a PaaS solution hurts long-term productivity and flexibility less than an app server. Many PaaS solutions offer *fairly* easy integration with most third-party components not endorsed by the platform. However, you will still hear faint murmurs of the words *"That's not supported yet"*.

The demand for PaaS solutions brings a plethora of nice innovation and improvements to the general computing ecosystem: Most notably making microcontainers on the Linux platform production ready, and enabling automatic horizontal scaling.
My instinctive reaction is to appreciate all the nice focus on automation and process isolation, but be selective about what I'll be using in a major project.
Your simple CRUD apps will probably do just fine on a PaaS.
Though, as soon as you start discovering new technical challenges or business requirements, your applications might outgrow the platform.
And that is the time at which you start to experience a deja-vu of having to fight the platform in which the application runs.

As a developer I like to understand, and be able to control, everything that happens in my runtime environment. I like to build things from simple abstractions.
If I want log aggregation, I'll just set up [logstash](http://logstash.net/). If I need a load balancer, I'll set up [HAProxy](http://haproxy.1wt.eu/). It's *not that hard*, and the flexibility it provides is well worth the investment of time.
If I want auto-scaling or governance or process isolation, I will try to find a product that does this, but nothing else.

Tread with caution. The giants are rising yet again.
