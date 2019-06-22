# The Evolutionary Architect

## Evolutionary Vision
You should think like a town planner: you have to try to optimize the layout of microservices infrastructure, like a city, to best suite the needs of "citiziens" users and developers operations.

You do not have to say "build this specific building there", but you have to _zone_ a city.

The system (the city) need to be a good place for everyone who use it. Not only users, but also developers and operations people.
![Evolutionary Architecture](./img/evolutionary_architecture.jpg "Evolutionary Architecture")

## Zoning
Be worried about what happens between boxes and be liberal in what happens inside.

The architect must spend time coding with these teams (in pair programming)

## Approach

### Strategic goals
If you define a company technical vision, you will need to spend more time with the non-technical parts of the organization (or the business).

### Principles
You have to define some principles (fewer than 10 is enough) to align what you are doing to some larger goal.

At [www.12factor.net](http://www.12factor.net)) you can view an example of a set of principles which are structured around the goal of helping you create applications that work well on the Heroku platform.

The more principles you have, the greater the chance that they overlap or contradict each other.

You have to make a distinction between: _principles_ and _contraints_

- _principles_: something you decide to choose
- _contraint_: something very hard to change

### Practices
Our practices are how we ensure our principles are being carried out.

Due to their technical nature, practices will often change more often than principles.

examples:
- coding guidelines
- central logging
- HTTP/REST for the standard integration style

### CombiningPrinciples and Practices
For larger organizations, where the technology and working practices may differ, you may want a different set of practices in different places, as long as they both map to a common set of principles.

### The Required Standard
Find the balance between optimizing for autonomy of the individual microservice without loosing sight of the big picture.

### Monitoring
Coherent cross-service view of the system health.

Monitoring & logging must be in one place and must be standardized across all services.

### Interfaces
We need to have only one or two different style of integration.
- technology
- protocol
- verbs or nouns?
- pagination style
- versioning of endpoints

### Architectureal Safety
We have to ensure that our services shield themselves accordingly from unhealthy or downstream calls.

## Governance Through Code

### Exemplars
Developer like code, code they can explore and run.

You can make some exemplars, so developers can point to them.

Ideally, these should be real-world services you have to get things right, rather than isolated service that are just implemented to be _perfect examples_

### Tailored Service Template
A tailored service template is like a microservice which works pulling together a set of libraries to provide features like health checking,
serving HTTP, or exposing metrics.

_The Good_:
- make easy for the developers to follow most of guidelines with a little work.
- you ensure that teams can get going faster, and also that developers have to go out of their way to make their services badly behaved.

**_The Bad_**:
- Be careful that creating the service templates _does not become the job of a central tool or an architecture team who dictates how things should be done_ (**ALBEIT VIA CODE**)
- more and more work can be placed into a centralized framework until it becomes an **overwhelming monstrosity**.
- It's more difficult when you embrace multiple technologies
- be aware of the perils of shared code [Integration Chapter](./4_Integration.md)

### Exception Handling
In companies where the development teams have a high degree of trust and
autonomy, and there the principles are lightweight (and the need for overt exception handling is greatly reduced if not eliminated).

In more structured organizations in which developers have less freedom, _traking exceptions: may **be vital** to ensure that the rules put in place properly reflect the challenges people are facing.

For example, we might change our practice to say: "Use this DB for most storage, unless you expect large growth in volumes, in which case use Cassandra"
