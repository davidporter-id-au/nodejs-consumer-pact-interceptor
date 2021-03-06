### Node Consumer Pact interceptor

This is a pure nodejs implementation of the PACT system of mocking and testing
system integration points. It is presently in an **Alpha** state and you
should expect some bugs. Be sure to read the caveats below.

##### Quickstart

See the `examples/passing-example` for the fastest way to get started.

##### Workflow:

(This assumes understanding of
    - [Consumer driven contracts](http://martinfowler.com/articles/consumerDrivenContracts.html)
    - [The PACT v2 specification](https://github.com/pact-foundation/pact-specification/tree/version-2))

1. Create pact JSON spec
2. Feed this to the interceptor (As below examples)
3. Send requests as expected to the provider, they will be intercepted as outgoing HTTP requests
4. Throw any failures received by the interceptor, else verify the response is as expected
5. Publish pact to broker

##### Why?

There is a [JS DSL](https://github.com/DiUS/pact-consumer-js-dsl) for creating pacts which
is already compatible with the Pact v2 Spec. However, this requires the use of a ruby
server with which to create assertions with.

We found that it was difficult to induct new developers into using this system and the
incidental complexity barrier (often compounted by CI servers and docker-containers)
was such that this became a real pain-point.

##### How does it work

The interceptor is just wrapping the excellent [MITM library](https://www.npmjs.com/package/mitm)
which is catching outgoing HTTP requests at the NodeJS core level and allowing
responses to be injected.

From a high-level, the interceptor waits for requests the URL it's watching and, once
it receives them, it will try verify this against the PACT specification. If it fails
to do so, it will return an assertion error, if there is no assertion failures it
will respond as per the PACT specification.

##### Caveats:

- As yet this **does not fully implement Pact V2**. At the time of writing I have been unable
 to fully meet the requirements of nested type-matching.
- This is **Based around a single interaction per pact-file**. This was a first-cut
design-decsion and will be revisited if it appears necessary.
- Outgoing HTTP or socket requests which are not part of the pact test are going to be blocked.
This is unfortunate and less than ideal. I have not yet found a way to use MITM's API to allow
HTTP requests on a per-url basis.
