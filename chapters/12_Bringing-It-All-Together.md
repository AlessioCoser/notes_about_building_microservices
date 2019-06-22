# Bringing It All Together
- Model Around Business Concept
  - use bounded context
- Adopt Culture of Automation
  - Automated testing
  - immutable servers
- Hide Internal Implementation Details
  - hide service's database
  - keep technology agnostic APIs (REST)
- Decentralize All the Things
  - teams own their services
  - Align teams to the organizations (Conway law)
  - embrace shared governance
- Independently Deployable
  - coexist versioned endpoints
  - one-service-per-host
  - use consumer driven contracts to catch breaking changes before they happen
- Isolate Failure
  - don't treat remote calls like local calls
  - Understand when and how use bulkheads and circuit breaker to limit the fallout
  - know whether scrificing availability or consistency
- Highly Observable
  - use semantic monitoring
  - aggregate your logs, stats

## When Shouldn't You Use Microservices?
The less well you understand a domain, the harder it will be for you to find proper bounded context for your service.

## Parting Words
Learn to embrace the concept of evolutionary architecture, where your system bends and flexes and changes over time as you learn new things.

Thinks _not of big-bang rewrites_, but instead _of a series of changes over time as you learn new things_.

Change is inevitable. Embrace it.
