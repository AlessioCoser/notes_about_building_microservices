# Microservices at Scale

## Failure is Everywhere
We know that the network is unreliable.

If we can handle the failure of a service gracefully, then it follows that we can also do in-place upgrades of a service, as a planned outage is much easier to deal with than an unplanned one.

We can also spend less of our time trying to stop the inevitable, and more of our time dealing with it gracefully.

Baking in the assumption that everything can and will fail leads you to think differently about you to solve problems.

## How Much is Too Much?
Knowing how much failure you can tolerate, ow how fast your system needs to be, is driven by the users of your system.

When it comes to considering if and how to scale out your system to better handle load or failure, start by trying to understand the following requirements:
- _Response time/latency_
- _Availability_
- _Durability of data_

## Degrading Functionality
We need to understand the impact of each outage, and work out how to properly degrade functionality. For example: if we are developing and e-commerce, and the shopping cart service is unavailable, we're probably in a lot of trouble, but we could still show the web page with the listing. Perhaps we just hide the shopping cart or replace it with an icon sayng "Be Back Soon!".

The right thing to do in any situation is often not a technical decision.

## Architectural Safety Measures
These are few patterns that we can make use of to ensure that if something does go wrong, it doesn't cause a nasty ripple-out effects.

## The Antifragile Organization
Some companies organize _game days_, where failure is simulated by systems being switched off and having the various teams react. For example:
- Google has simple tests to mimic server failure, and as part of its annual DiRT (Disaster Recovery) exercises is has simulated large-scale disaster such as earthquakes.
- Netflix has a more aggressive approach, by writing programs that cause failure and running them in production on a daily basis.

The company has to understand the importance of learning from the failure when it occurs, and adopt a blameless culture when mistakes do happen. Developers are further empowered to be part of this learning and evolving process, as each developer is also responsible for managing his or her production services.

Things will fail. The fact that your system is now spread across multiple machines (which can and will fail) across a network (which will be unreliable) can actually make your system more vulnerable, not less.

### Timeouts (page 211)
Timeouts are something it is easy to overlook, but in a downstream system they are important to get right.
Put timeouts on all out-of-process calls, and pick a default timeout for everything. Log when timeouts occur, look at what happens, and change them accordingly.

### Circuit Breakers
After a certain number of requests to the downstream resource have failed, the circuit breaker is blown. All further requests fail fast while the circuit breaker is in its blown state. After a period of time, the client sends a few requests through to see if the downstream service has recovered, and if it gets enough healthy responses it resets the circuit breaker.

While the circuit breaker is blown you have some options:
- Queue up the requests and retry them later on. (specially when the work is done on an async job)
- Fail fast and propagate error up the call chain (better when we are doing sync calls through the chain)

### Bulkheads
Is a way to isolate yourself from failure. (Same as bulkheads of the ships that protect the rest of the ship from a leak)
- Using different connection pools for each downstream connection.
- Spearation of concern can also be a way to implement bulkheads. (By teasing apart functionality into separate microservices, we reduce the chance of an outage in one area affecting another.)

Sometimes rejecting a request is the best way to stop an important system from becoming overwelmed and being a bottleneck for multiple upstream services.

See Netflix Histrix library

## Idempotency
Ex: some of the HTTP verbs such as GET and POST, are defined to be idempotent, but they rely on your service handling these calls in an idempotent manner.

## Scaling
We scale our system for 2 reasons:
- help deal with failure (having more instances will help)
- performance either in terms of handling more load, reducing latency or both

### Go Bigger
Increase CPU and improve I/O (vertical scaling), but can be expensive and sometimes out software itself cannot do much with the extra resources.

### Splitting Workloads
Having a single microservice per host is certainly preferable to a multiservice-per-host model.

With independent microservices that communicate over the network, it should be an easy tast to then move them onto their own hosts to improve throughput and scaling. This can also increase the resiliency of the system.

### Spreading Your Risk
Don't put all your eggs in one basket: don't have multiple services on one _host_ (or same physical box) (see AWS Availability Zones).

### Load Balancing
To have a resilient service you want to avoid single point of failure. The load balancers distribute calls sent to them to one or more instances based on some algorithm, remove instances when they are no longer healthy, and hopefully add them back in when they are.

### Worker-Based Systems
A worker-based system consists in a collection of instances all work on some shared backlog of work. This could be a number of Hadoop processes, or perhaps a number of listeners to a shared queue of work.

Good for async jobs.

You can spin up additional instances on demand to match the lod coming in.
The impact of a worker failing is easy to deal with. Work will take longer, but nothing get lost.

### Starting Again
You should design architecture for ~10x growth, but plan to rewrite before ~100x.

Danger: do not build for massive scale from the beginning. This can be disastrous: at the beginning we don't know exactly what we want to build, nor do we know if it will be successful.
Do less, validate the solution, gather feedback: at the beginning you don't need to create a huge architecture.

The need to change our system to deal with scale isn’t a sign of failure. It is a sign of success.

## Scaling Databases (page 222)
Availability of Service and Durability of Data are different things

### Scaling for Reads
ex: `Caching data`, use `read replicas`

Read replicas are often done to ensure that a copy of our data is kept safe, but we can also use it to distribute our reads.
With this technique reads may sometimes see `stale` data until the replication has completed.
This setup is called `Eventually consistent`.

### Scaling for Writes
One approach is to use `sharding`. You have multiple database nodes. You take a piece of data to be written, apply some hashing function to the key of the data, and based on the result of the function learn where to send data.

The complexity of sharding for writes comes from handling queries.
What about queries that span the data in multiple nodes?
You need to query each individual shard and join in memory, or have and alternate read store where both data-set are available.

Sharding for writes may not improve resiliency. (es: Cassandra handles the replication of data too)

### CQRS (page. 225)
Command Query Responsibility Segregation pattern
Part of the system deals with commands, which capture requests to modify state, while another part of the system deals with queries.
Commands:
  - come in requesting changes in state
  - are validates
  - they will be applied to the model
  - should contain info about their intent
  - can be processed sync or async

Internal models are completely separate from commands and queries.

## Caching
More often than not, cachingis about eliminating needless round-trips to databases or other services to serve results faster.

### Client-Side, Proxy, and Server-Side Caching
Client-Side:
  - client decides when (and if) cache the result or it needs to retrieve a fresh copy.
  - Reduce netowrk calls drastically
  - When need a change in how we cache stuff, we have to modify all the client services (it can be difficult)
  - Invalidation of stale data can also be trickier,
Proxy Caching:
  - placed between client and server.
  - for examplereverse proxy, CDN (Content Delivery Network)
  - a very simple way to add caching to an existing system
  - introduce additional network hops (but very rare)
Server-Side:
  - server handles handles caching responsibility
  - make useof a system like Redis or Memcache or something else
  - opaque to the clients
  - easier to invalidate data, track and optimize cache hits
  - when you have multiple clientsis the fastest way to improve performance

Use a mix of three approaches, but for some distributed system you may not need caching at all.

You need to know what load you need to handle, how fresh your data
needs to be, and what your system can do right now.

### HTTP Caching
cache-control directive in response to client (as header) this tell if client should cche te resource at al and, if so, how long they should cache it.

Are standards and there are a lot of preexisting software that handles the caching for us: Reverse proxies like Squid or Varnish can sit transparently on the network between client and server. (take a look at "REST In Practice" Book: http://bit.ly/rest-practice)

### Caching for Writes
Write to a local cache and, at some later point, the data will be flushed to a downstream source. (write-behind cache)
For example it is useful when a downstream service is unavailable, and we can queue up the writes and send them through when it is available again.

### Keep It Simple
Be careful about caching in too many places! Use it only if needed!

## CAP Theorem
It is impossible for a distributed system to provide more than two out of the following three:
  - _Consistency_: Every read receives the most recent write or an error
  - _Availability_: Every request receives a (non-error) response – without the guarantee that it contains the most recent write
  - _Partition tolerance_: The system continues to operate despite an arbitrary number of messages being dropped (or delayed) by the network between nodes

You have to find out a trade off between these three traits!

### Sacrificing Consistency
An _AP system_ is eventually consistent. It looses consistency to keep partition tolerance and availability.
- example: 2 load-balanced istances which call 2 different DB. These databases are synced by a multiprimary replication to share data between them. (at some point DB1 is not in sync with DB2, so you can have for a limited time a possibility of stale data)

### Sacrificing Availability
A _CP system_ looses availability to keep consistency and partition tolerance.
- example: if database nodes can't talk to each other, they cannot coordinate to ensure consistency. We are unable to guarantee consistency, so our only option is to refuse to respond to the request.

### Sacrificing Partition Tolerance
A _CA system_ don't exists in distributed system, it can't run over a network, it needs to be a single process operating locally.

### It's Not All or Nothing
Our system can be AP between some nodes and CP between some others.

### And the real World
Yet so much of we build is just a reflection of the real world, and we don't get control that.
We have to recognize that no matter how consistent our system might be in and of themselves, they cannot know everything that happens, especially when we are keeping records of the real world.

_This is one of the reasons why AP systems end up being the right call in many situations._

Consistency across multiple nodes is really hard. Distributed systems have to expect failure. So, if you really need it don't try to invent it yourself. Pick a data store od a lock service that offers these characteristics.
