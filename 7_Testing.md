# Testing
Brian Marick's testing quadrant
```txt
                                  Business Facing
              _______________________________________________________
              |    Acceptance testing    |    Exploratory testing   |
              |   (build right thing?)   |    (How can I break?)    |
              |       - automated -      |         - Manual -       |
  Support     |__________________________|__________________________|  Critique
programming   |       Unit testing       |     Property testing     |  product
              |     (built it right?)    | (scalab., perf., secur.) |
              |       - automated -      |         - tool -         |
              -------------------------------------------------------
                                  Technology facing
```
## Types of Test

## Test Scope

## Those Tricky End-to-End Tests
Handle and-2-end tests across services.

Uses normal pipelines for each service. Then at the end of the pipelines the end-2-end tests should be triggered. (show figure page. 139)

Some downsides to handle:

### Flaky and Brittle Tests

The more moving parts, the more brittle out tests may be, and less determinstic they are. So a test that does not pass now, maybe could pass later; it's a flaky test.

When we detect flaky tests, it is essential that we do our best to remove them.

### Who write these test?

Avoid all teams to have granted access at adding tests without undestanding of the health of the whole suite. (explosion test cases, no real obvious ownership leads to ignoring the tests)

Avoid building a dedicated team responsible to write these tests.(team becomes increasingly distant from the tests for its code, Cycle time increase, as service owners end up waiting for the test team).

The best balance I have found is to treat the end-to-end tests as a shared codebase, but with joint ownership.

### The great Pile-up
The larget the scope of a deployment and the higher the risk of a release, the more likely we are to break something.

A key driver to ensuring we can release our software frequently is based on the idea that we release small changes as soon as they are ready.

### The Metaversion
Why not use a version number for the whole system?

By versioning together changes made to multiple services, we cede one of the main advantage of microservices: the ability to deploy one service independently of other service.

### Consumer-Driven Test to the Rescue

**Consumer Driven Contract (CDC)**

We define the expectations of a consumer on a service (or producer)
The expectations are captured in code form as tests, which are run against the producer.

CDC should be run as part of CI builds of the producer, ensuring that it never deployed if it breaks one of these contracts.

These tests should be run only against a single producer in isolation, so can be faster and more reliable than the end-to-end tests they might replace.

With CDC we can identify a breaking change prior to our software going into production without having to use a potentially expensive end-to-end test.

### It's About Conversations
CDC requires good communication and trust between the consumer and producing service.

When you are consuming a service provided with a third party, you may not have the frequency of communication (or trust) to make CDC work.

In these situations, you may have to do larger-scoped integration tests just around the _untrusted_ component.

### So Should Tou Use End-to-End Tests?

You do not necessarily throw these tests away. You may end up using many of those end-to-end journey tests to monitor the production system using a technique called _semantic monitoring_.

## Testing After Production

### Separating Deployment from Release
If we can deploy our software, and test it in place prior to directing production loads against it, we can detect issued specific to a given environment.

Another example: blue/green deployment.

Two copies of our software deployed at a time, but only one is receiving real requests.

1. You have to be able to direct production traffic to dofferent hosts (DNS, load-balancing, etc.)
2. You have to be able to provision enough hosts to have both versions running at once.

Using blue/green deployment allows you to reduce the risk of deployment, as well as gives you the chance to revert should you encounter a problem.

By keeping the old version running while we perform our release we greatly reduce the downtime associated with releasing our software.

### Canary Releasing
with this approach, we are verifying our newly deployed software by directing amounts of production traffic against the system to see if it _performs as expected_ (functional or not functional, es: check it is responding in 500ms, the error rate between new and old service is proportional)

This approach differs from blue/green deployment in that you can expect versions to cohexists for longer, and you will often vary the amount of traffic.

### Mean Time to Repair Over Mean Time Between Failures?
Sometimes expendingthe same effort into getting better at remediation of a release can be significantly more beneficial than adding more automated functional tests.

Techniques to reduce the time to recovery can be as simple as very fast rollbacks coupled with good monitoring like blue/green deployments.

For different organizations the trade-off between MTBF and MTTR will vary.

## Cross-Functional Testing
They include aspects like the acceptable latency of a web page, the number of users a system should support, how accessible your UI should be to people with disabilities, and so on.

### Performance Tests
When you have a call chain of multiple synchronous calls, if any part of the chain start acting slowly, everything is affected, potentially leading to a significant impact.

Make sure you also look at theresult!
You really need to have targets. This way you can make the build go _red_ or _green_ based on the results being a clear cal to action.