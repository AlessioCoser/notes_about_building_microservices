# Conway's Law and System Design
Any organization that designs a system will inevitably produce a design whose structure is a copy of the organization's communication structure.

If you have 4 groups working on a compiler, you will get a 4-pass compiler.

## Loose and Tightly Coupled Organizations
For tightly coupled orgs, think commercial product firms that are typically colocated with strongly aligned visions and goals, while loosely coupled orgs are well represented by distributed open source communities.

The study: the more loosely coupled orgs actually created more modular, less coupled systems, whereas the more tightly focused orgs's software was less modularized.

## Netflix ad Amazon

Amazon understand that teams owns the whole lifecycle of the system they manage and also knows that small teams can work faster than large teams. => two-pizza teams => AWS

Netflix learned from this example and it structured itself around small, independent teams. => Architecture optimized for speed of change.

## Adapting to Communication Pathways
We want to ensure our services are decomposed such that the pace of change inside a service is much higher than the pace of change between services.

When the cost of coordinating change increases, or people find ways to reduce the coordination costs, or they stop making changes.

A suggestion could be that use geographical boundaries: assign ownership of a service to a single, colocated team who can keep the cost of change low.

## Service Ownership
Team owning a service is responsible for making changes to that service. It should feel free to restructure the code however it wants, as long as that change doeasn't break consuming services.

Having one team responsible for deploying and maintaining th application means it has an incentive to create services that are easy to deploy.

This model pushes the decisions to the people best able to make them, giving the team both increased power and autonomy, but also making it accoutnable for it's work.

## Bounded Contexts and Team Structures
We have to look to draw our service boundaries around bounded contexts. It therefore follows that we would like our teams aligned alon bounded contexts too.

## Case Study: RealEstate.com.au
Those organizations that are adaptive enough to change not only their system architecture, but also their organizational structure can yeld huge benefits in terms of improved autnomy of teams and faster time to market for new features and functionality.

## People
_No matter how it looks at first, it's always a people problem_ -- Gerry Weinberg, The second Law of Consulting
In a microservice environment a developer has to be more aware of the implications of things like calls across boundaries, or the implications of failure.

Pushing power into development teams to increase autonomy can be _fraught_ (causing or affected by great anxiety or stress).

You may find contractual barriers to having your developers carry support pagers for the systems they support. (NDR: Remove the blame practice)

You have to understand staff's appetite to change, don't push them too fast! Each organization has it's own set of dynamics around this topic.

Without people on board, any change you might want to make could be doomed from the start.