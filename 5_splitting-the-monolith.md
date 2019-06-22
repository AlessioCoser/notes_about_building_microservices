# Splitting the Monolith

## It's all about Seams
highly cohesive
loosely coupled

A Seam is a portion of code that can be treated in isolation and worked on without impacting the rest of codebase.
We want to Identify seams that can become service boundaries.
Bounded contexts make excellent seams: they represent a cohesive and yet losely coupled boundaries in an organization.

## Start Breaking Apart
Is a process which could take more than a day or than a week and has to be done bit by bit, day by day
1. Start Identifying the boundaries in our code.
2. create packages representing these context and move the existing code into them with refactoring (you must have tests)
   a. The remaining code will often identify bounded context we might have missed

## The reason to Split the Monolith
Use an incremental approach -> it also will limit the impact of getting something wrong.

It's best to think about where you are going to get the most benefit from some part of your code-base being separated.

## Consider some driver:

### Pace of Change
If we split a part as a service now, we could change that service faster, as it is a separate autonomous unit.

### Team Structure
Each team should have a full ownership of one or more services.

### Security
Spliting out services related to sensitive information we can provide additional protection.

### Technology
If we split by technology or algorithms, it would be easy to consider building an alternative implementation that we could test against.

### Tangled Dependencies
Consider how a seam is entangled with the rest of the system.
We want to pull out the seam that is least depended on if we can.


### The Database
We need to find seams in our database too so we can split them out cleanly.

## Getting to Grips with the Problem
1. See which part of the system read/write to the database.
   - Use a Repository layer
   - Group the code into packages representing bounded contexts.
   - Split repository layer, each part should represent a bounded context's database access.

   This help us understand what parts of the database are used by what bts of code (what bounded context)

### Example: Breaking Foreign Key Relationships
BEFORE:
```txt
----------|         |----------|
| Catalog |         | Fincance |
|----------         |-----------
     |                     |
----------|               / \      -----------|
|C. Table |______________/   \_____| F. Table |
|----------                        |-----------
```
AFTER:
```txt
----------|   api   |----------|
| Catalog |---------| Fincance |
|----------         |-----------
     |                    |
----------|         -----------|
|C. Table |         | F. Table |
|----------         |-----------
```

### Example: Static Shared Data
You can have country_code table that belongs to 3 different services
1. duplicate table for each of our services
   - potential consistency challenge
   - you have to live edit the table on the database
2. Treat this shared, static data as code. (es: property-file or enumeration)
  - potential consistency challange
  + easier to push out changes to configuration files than table of database
3. push static data into separate service of it's own rights.
  - only if it's necessary! (volume, complexity, rules associated)

### Example: Shared Data
We have stored all information in a fairly generic customer record table.
Both services are writing and reading to the same table.

This is a domain concept that isn't modeled in the code, and is in fact implicity modeled in the database.

We need to make the current abstract concept of the customer concrete. We may end up with a distinct customer service.

### Example: Shared Tables
Split the table in two different tables which express the real behaviour (boundary)

## Refactoring Databases
### Staging the break
### Transactional Boundaries
#### Try again later
#### Abort the entire operation
#### Distributed transaction

## Reporting
### The reporting Database

## Data Retrieval via Service Calls
es:
- reporting system request data to a service.
- service returns an HTTP 202 response (accepted but not processed).
- reporting system polls the resource waiting until it retrieve a 201 Created status.
- a. fetch the data trough HTTP
- b. get path from http and fetch data from a shared location.

!!! There are other potentially simpler solutions that can scale more effectively.

### Data Pumps
INVERT: rather than have the reporting system pull the data, we could instead have the data pushed to the reporting system.

An alternative option is to have a standalone program that directly accesses the database of the service that is the source of data, and pumps it into a reporting database.

Sharing database is bad -> This approach, IF IMPLEMENTED PROPERLY is a notable exception, where the downsides ofthe coupling are more than mitigated by making the reporting easier. !! SAME TEAM HAS TO BE OWNER OF BOTH SERVICE AND PUMP !!

### Alternative Destinations
-> S3

### Event Data Pump
The service emits an event when a given entity is created, updated or deleted. Then we can write our own event subscriber that pumps data into the reporting database.
+ less coupling
+ faster reporting
+ insertion more efficient
+ can be managed by an another team
- may not scale as well as data pump for larger volumes of data that has benefit of operating directly at the database level.

### Cost of Change
Do baby steps! Try to make mistakes where the impact will be lowest.
What calls get made?
Do you start seing odd circular references?
Do you see two services that are overly chatty, which might indicate they should be one thing?

Great tecnique is Class-responsibility-Collaboration (CRC) and can be applied to services too.
For each service list its responsibilities in terms of capabilities it provides, with the collaborators specified in the diagram.
As you work through more use cases, you start to get a sense as to whether all of this hangs together properly.





