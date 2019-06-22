# Deployment

## Continuous Integration

### Are you really doing it?
- Do you check in to mainline once per day?
- Do you have a suite of tests to validate your changes?
- When the build is broken, is it the #1 priority of the team to fix it?

## Mapping Continuous Integration to Microservices
The preferred approach is to have a single CI build per microservice, to allow us to quickly make and validate a change prior to deployment into production. Each microservice has its own source code repository mapped to its own CI build.

Alignment to team ownership is more clear too. If you own the service, you own the respository and the build.

making changes across repositories can be more difficult in this world.

## Build pipelines and Continuous Delivery
Different stages on Pipeline in order to have the fastest feedback.

Single artifact to test and deploy. Example:
  - Build artifact -> test built artifact -> deploy tested artifact

Continuous Delivery is the approach whereby we get constant feedback on the production readiness of each and every check-in, and furthermore treat each and every check-in as a release candidate.

Some stages may be manual.

One pipeline per microservice.

### Inevitable Exceptions
When a team is starting out with a new project, expecially a greenfield one, it is quite likely that there will be a large amount of changes in terms of working out where the service boundaries stand.
This is a good reason, in fact, for keeping your initial services on the larger side until your understanding of the domain stabilizes.
During this period, having all services in a single build to reduce the cost of cross-service changes may make sense.

Releasing all the services as a bundle needs to be a transitionary step.
As service API stabilze, start moving them out into their own builds.

If you are unable to get stability in service boundaries in order to properly separate them, merge them back in a more monolithic service and give yourself time to get grips with the domain.

## Platform-Specific Artifacts
Artifacts like Java JAR, NPM package, Ruby GEM may need to be executed (for example) by an embedded HTTP process.
This configuration change by the project by the OS etc.

There are different types of artifacts that might be even easier to work with.

### Operating System Artifacts
We just use thetools native to the OS to install the package. (like .RPM, .DEB, etc...)
But:
- It is hard to create packages like these.
- You are coupled with the OS

### Custom Images
Using an atomated configuration management like Puppet, Chef, Ansible.
Create a virtual machine that bakes in some of the common dependencies we use. All virtualization platforms allow you to build your own images.

Deploy our software by spinning up an instance of this custom image and all e have to do is install the latest version of our software. (Packer)
+ You don't spend time installing dependency each time
+ Significant time savings.
- Building images takes long time
- must support other ways of deploying services to ensure the developers don't have to wait half an hour just to create a binary deployment.
- Resulting image can be very large (look at container technologies)

#### Images as Artifacts
Go further: bake our service into the image itself, and adopt the model of our service artifact being an image.

#### Immutable Servers
Configuration drift -> the code in source code control no longer reflects the configuration of the running host.

To avoid this, we can ensure that no changes are even made to a running server.
Any change, no matter how small, has to go through a build pipeline in order to create a new machine. (es: disable SSH)
We need to ensure that any data we care about that is stores on the box is stored elsewhere (like mounting a volume, external DB, etc...)

## Environments
Tou will want to ensure that your environments are more and more production-like to catch any problems associated with these environmental differences sooner.
This will be a constant balance. Sometimes the time and cost to reproduce and run on every deployment production-like environments can be prohibitive, so you have to make some compromises.

## Service Configuration

Ideally, there should be a small amount of service's configurations and limited to those feature that change from one environment to another.

The more your configuration changes foundamental service behaviour, and the more that configuration varies from one environment to another, the more you will find problems only in certain environments, which is painful in the extreme.

A good approach is to create one single artifact and manage configuration separately.
- property file in each env
- different parameters passed in to an install process)
- ...
- dedicated system for providing configuration. (cap. 11)

## Service to Host Mapping

### Multiple services per Host
- Monitoring more difficult
- When one service is under a significant load, it can end up reducing the resource available to other running services.
- Deployment more complex (using something like puppet) you can't be sure that an update does not affects other services or dependencies.
- Can inhibit autonomy of teams. If services for different teams are installed on the same host, who gets to configure the host?

#### Application Containers
Like Java Servlet Container (JBoss, Tomcat?)

- Can not choose different technologies through services.
- Limit also the options you have in terms of automation and system management.
- Proper lifecycle management on top of JVM can be problematic.
- Analyze resource and thread management is also much more complex. (multiple application share same process)
- Many of them are commercial and so have a cost implication.

**Prefer looking at self-contained deployable microservices as artifacts.**

### Single service per Host

#### Platform as a Service
- Trade of in term of what you can fix under the hood of a PaaS.

#### Automation
Automation is how we can make sure that our developers still remain productive. Giving them the ability to self-service-provision individual services or group of services is the way to making developers' lives easier.
