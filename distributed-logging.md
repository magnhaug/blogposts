Logging in distributed systems
===============================

The [microservices architecture](http://martinfowler.com/articles/microservices.html) is gaining traction, and many systems fan out into sets of applications distributed on multiple servers. If you don't keep your logging in check, it gets harder and more tedious to compile a full trace of your complete systems behavior. In fact, one of the arguments against a distributed system is that it is harder to debug. I intend to show you that with a little effort spent on logging and infrastructure, we can bust that myth. Get ready to level up your logging game!

#### Starting grounds: The basics of logging
Everything we will cover here assumes you already have a grip on standard application logging, with a basic setup. Your logs end up in files on disk. You use a standard logging framework like [slf4j](http://www.slf4j.org/) and [logback](http://logback.qos.ch/). You know [what to log](http://arctecgroup.net/pdf/howtoapplogging.pdf) and [how to log it](http://gojko.net/2006/12/09/logging-anti-patterns/). Timestamps, thread names and class names are a part of your logger pattern.

#### Level 1: Aggregate your logs
If you do not have one already, you need to set up a log aggregator. This is absolutely necessary to do to reap *any* benefits from your logs in a distributed system. Luckily it is pretty easy to get going with a basic setup.
A personal favorite is the [ELK stack](http://www.elasticsearch.org/overview/). This gives you everything you need: ElasticSearch to store the logs, Logstash to collect and manage them, and Kibana to view the logs and pull our reports. The ELK stack is widely used and works well. If you have the budget to pay for a license, [Splunk](http://www.splunk.com/) is a worthy alternative.
Other free alternatives include [Apache Flume](https://cwiki.apache.org/confluence/display/FLUME/Home) that stores data in HDFS, or [rsyslog](http://www.rsyslog.com/) in conjunction with the datastore of your choice.
When you have a log aggregator running with a nice UI on top to browse your logs, it's much easier to correlate and filter down logs. This means you can now enable a lot more log information in all your applications! Tthose who do not need that specific information can filter it out query-time by applying a simple log-level filter.

#### Level 2: Trace the flow
When debugging a complex issue in a multi-app environment, you need to be able to trace the control flow across applications. To make this a lot easier, follow these simple rules.
 - Log the `username` of the user initiating the first request. Log the ´system-name´ if the initiator is a system, like a scheduled service.
 - Log the `user-agent` of the application calling your application.
 - Log a `traceId` throughout the call chain, to be able to quickly search across all applications for every log trace for a given user action.
 - Log a `spanId` in your request scope in every application, to be able to distinguish separate requests to the same app.

These rules implies a couple of important aspects. Firstly, you have to propagate the `username`, `user-agent` and `traceId` whenever your control flow jumps from one application to the next. If your applications communicate using ´REST´ you can transmit these bits of information as HTTP header parameters.
The `traceId` and `spanId` can be a randomly generated short string or number. The `traceId` will be generated when you hit the first application, and just re-used in all cascading requests.
A new `spanId` will be generated every time a request hits an application.
To make sure every bit of metainformation is logged at every log-statement in your application, a good solution is to include it in your log pattern, fetching the values from [MDC](http://logback.qos.ch/manual/mdc.html).

#### Level 3: Explicitly log control flow events
Log a line as soon as a request enters your application, and as soon as you transfer the control flow to another component. This typically means you log a "hello" and a "goodbye" message at the entry- and exit-points for your REST or SOAP endpoints. Also log a "calling" message whenever you initiate a request to another service, and a "recieved" message when the other service responds. This will enable you to identify which external service calls are the performance drag in your system, with a quick glance at the timestamps of your logs. You can also correlate the timestamps from the calling service and the callee, to identify network latency issues.

#### Level 4: Log metrics
With a library like [Metrics](http://metrics.codahale.com/) you will get even easier-to-read output about your applications runtime performance. Since it is ridiculously easy to get started with, there's almost no reason not to. If it looks like a daunting task to apply meters and timers to every application, start with the services that has the highest risk of running into performance problems or being a bottleneck. Log everything to your log files, or to another specialized aggregator if you intend to use your metrics for more fancy applications. A good set of historic/logged metrics is paramount to retroactively debugging a performance problem in your production environment. Investing a little time here makes it possible to quickly pinpoint bottlenecks, and also to set up alarms whenever a certain service suffers from a low throughput or a high latency.

#### Level 5: Consider a structured log format
*Consider* logging in a [structured log format](http://gregoryszorc.com/blog/category/logging/). This makes it a lot easier to parse your logs for answering complex queries through your log aggregator. There are good arguments as to why using a structured log format might be a [waste of time](http://carolina.mff.cuni.cz/~trmac/blog/2011/structured-logging/). My gut feeling here tells me that it's a cheap investment in a *new* system, but I'd be reluctant to spend too much time re-working my old log statements. Migrating a system to a structured log format is probably worth the effort if you find yourself repeatedly constructing complex queries in order to parse log messages and to correlate certain data points. Keep in mind that humans will still also read your logs. Ideally you want your logs to be both easily read and easily parsed.

#### Level 6: Consider JIT logging
*Consider* [Just-in-time logging](https://pragprog.com/magazines/2011-12/justintime-logging). This pattern is a bit dated, but it will be helpful if you are struggling with logging I/O or log size quotas that you cannot work around. Given that you already implemented the previously mentioned measures like log aggregation and a trace/span id for all requests, most of the listed benefits of JIT logging have already been reaped. 

#### Level 7: Consider Dapper
*Consider* using an implementation of [Google Dapper](http://research.google.com/pubs/pub36356.html) like i.e. [Twitter Zipkin](http://twitter.github.io/zipkin/). This is basically a toolset that helps you do all of the above, through a few well defined interfaces and some utility aggregator daemons. Zipkin leverages adaptive sampling, failover and other techniques required to scale *really well* (far better than most normal-sized projects need). It also gives you some pretty [nifty graphs](https://blog.twitter.com/2013/observability-at-twitter), a powerful query language and good monitoring capabilities. However, for most purposes a log aggregator like the ELK stack and a basic trace-id/metrics logging setup is good enough.
