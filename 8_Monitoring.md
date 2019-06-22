# Monitoring

### Single Service, Multiple Servers
With a load balancer on top of the servers.
- Aggregate the logs in order to see what they are across all hosts
- For some information like response time probably you can track at the load balancer itself.
- Tracking the behaviour of Load Balancer

### Multiple Services, Multiple Servers

Answer is collection and central aggregation. (es: using logstash)

### Metric Tracking Across Multiple Services
- We want to pick the system that makes it very easy to collect metrics from new hosts or new instances.
- We want to be able to look at a metric aggregated for the whole system (es: average CPU load)

So we will need to be able to associate metadata with the metric to allow us to infer this structure.

[Graphite exposes very simple API and allows you to send metrics in real-time]

### Service Metrics
Your services should expose basic metrics themselves. (like response time and error rate)

### Syntetic Monitoring
Our monitoring system could be programmed to act a bit like our users, and could report back if something goes wrong.

#### Semantic Monitoring
Run the end2end service or end2en system tests on an ongoing basis, as a way of monitoring our system.

Be carefull about data requirements, adapt to different live data or different source data.

Es: Have a fake user in production with a known set of data.

#### Correlation IDs (page 162)
Generate a GUID on a first call to the system. This GUID is then passed along to all subsequent calls and can be put into your logs in a structured way.

A problem with Correlation ID is that you often don't know you need one until after you already have a problem that could be diagnosed only if you had the ID at the beginning.

Suggestion: consider putting them in as soon as you can.

#### The cascade
Use libraries to implement a circuit breaker around network calls to help you handle cascading failures in a more elegant fashion. (es: Hystrix)

#### Consider the Audience
What people want to see and react to RIGHT NOW is different than what they need when drilling down. So, for the type of person who will be lloking at this data, consider:
- What they neet to know right now
- What they might want later
- How they like to consume data