# Security
How much security we need? How can we work out what is _enough_ security?
How do we know who a person is, and what we can do? How does this relate to how our servers talk to each other?

## Authentication and Authorization
**Authentication** is the process by which we confirm that a party is who she says she is.

**Authorization** is the mechanism by which we map from a principal to the action we are allowing her to do.

### Common Single Sign-On Implementations
- **SAML**: (soap, old)
- **OpenId**: Connect (rest, new)

When a principal tries to access a resource she is redirected to authenticate with an identity provider. Once the identity provider is satisfied that the principal has been authenticated, it gives information to the service provider, allowing it to decide whether to grant her access to the resource.

You can use an internal identity provider, or use something external your own organization.

### Single Sign-On Gateway
Each service must handle the redirection and handshaking with the identity provider. Obviously this means a lot of duplicated work.

So you can use a gateway to act as a proxy, sitting between your services and the outside world.

The idea is that we can centralize the behaviour for redirecting the user and perform the handshake in only one place.

We still need to solve the problem of how the downstream service receives information about principals. (es: populate headers with this info)

Another problem it can be harder to reason about how a microservice behaves when looking at it in isolation.

The final problem is that you can have a false sense of security. This can lead to a single point of failure.

Be careful: gateway layers tend to take on more and more functionality, which itself can end up being a giant coupling point.

### Fine-Grained Authorization
Avoid fine-grained roles in favour of coarse-grained roles, modeled around how your organization works.

## Service-to-Service Authentication and Authorization
What about programs, or other services, authenticating with each other?

### Allow Everything Inside the Perimeter
This is by far the most common form of inside-perimeter trust in organizations.

Often the implicit trust model is not a conscious decision, but more that people are unaware of the risk in the first place.

### HTTP(S) Basic Authentication
This methos allows for a client to send a username and password in a standard HTTP header.

- must use SSL
- must handle SSL signing certificate
- SSL traffic cannot be catched by reverse proxies
- If we use a SSO solution, e run the risk of duplicating behaviour

We have no idea if this information is coming from a machine we expect, it ould be coming from anyone on our network.

### Use SAML or OpenID Connect
You will need an account for your clients, sometimes referred to as a service account.

A set of credentials for each microservice to handle. This make revoking/changing access easier.

- We need to securely store our credentials.
- Authentication process is fairly tedious.

### HMAC over HTTP
Use a hash-based messaging code (HMAC) to sign the request (used by Oauth and AWS S3 api's)

With HMAC the body of the request along with a private key is hashed, and the resulting hash is sent along with the request.
The server then uses its own copy of the private key and the request body to re-create the hash. If it matches, it allows the request.

- We need to securely store a shared secret on both on server and client.
- Is a pattern, not a standard. There are many ways to implement it. (see JWT)
- This approach ensures only that no third party has manipulated the request. The rest of the data will still be visible to parties snooping on the network.

### API Keys
API keys allow a service to identify who is making a call, and place limits on what they can do.

A commont approach is to use a public and private key pair. Typically, you will manage keys centrally, just as we would manage identities of people centrally.

### The Deputy Problem
The confused deputy problem is a vulnerability which in the context of service-to-service communication refers to a situation where a malicious party can trick a deputy service into making calls to a downstream service on his behalf that he shouldn't be able to.

You could also state on whose behalf it is asking some information. Some authentication schemes allow us to pass in the original principal's credentials downstream. (It's difficult to validating trust for all those deputies)

## Securing Data at Rest
Defense in depth is key.

Often the data was stored in an unencrypted form, or the mechanism used to protect the data had a foundamental flaw.

### Go with the Well Known
Do not try to implement your own encryption!

### It's all about the Keys
Where is your key stored?

- Use separate security appliance to encrypth and decrypth data.
- Use separate key vault that your service can access when it needs a key.

### Pick Your Targets
### Decrypt on Demand
Encrypt data when you first see it. Only decrypth on demand, and ensure data is never stored anywhere.

### Encrypt Backups

## Defense in Depth

### Firewall
Having one or more firewalls is a very sensible precaution to take.

### Logging
Good logging id not about prevention, but can help with detecting and recovering from bad things happening.

### Intrusion Detection (and Prevention) System
IDS can monitor networks or hosts for suspicious behaviour, reporting problems when it sees them. IPS can step in to stop it from happening.

Unlike Firewall IDS and IPS are actively looking inside the perimeter for suspect behaviour.

### Network Segregations
With a monolithic system, we have limits to how we can structure our networks to provide additional protections. With microservices you can put them into different networks segments to further control how services talk to each other.

### Operating System
You can have the most well-defined and protected application-level secutiry in the world, but if you have an old version of a web server running on your machine as root that has an unpatched buffer overflow vulnerability, then your system could still be extremely vulnerable.

## A Worked Example
--

## Be Frugal
Do we really need to store someone's name, age, gender, and date of birth in order to provide her with product offers?

If you don't store it, no one can steal it!
If you don't store it, no one can ask for it either! (es: governmental agency)

## The Human Element
Putting yourself in the mindset of a malicious party is often a good way to reason about the protection you may need.

## The Golden Rule
DON'T WRITE YOUR OWN CRYPTO
DON'T INVENT YOUR OWN SECURITY PROTOCOLS

Reinventing the weel in many cases is often just a waste of time, but when it comes to security it can be outright dangerous.

## Baking Security In
Helping educate developers about security concerns can help reduce them in the first place.

The [Open Web Application Security Project](https://www.owasp.org/index.php/Main_Page) is a good point to start.

## External Verification

Excercises like penetration testing, when done by an outside party, really do mimic real-world attemps.

You will also need to consider how much verification you require before each release.

Generally doing a full penetration test isn't needed for small incremental reeases. What you need depends on your own risk profile.