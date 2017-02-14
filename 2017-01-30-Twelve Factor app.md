# Practising The Twelve-Factor App
* [The Twelve-Factor App](https://12factor.net/)
* ["12 Fractured App", practising by example](https://medium.com/@kelseyhightower/12-fractured-apps-1080c73d481c#.b4ayjxx9r)
* [How to Build 12 Factor Microservices on Docker - Part 1](https://www.packtpub.com/books/content/how-to-build-12-factor-design-microservices-on-docker-part-1)
* [How to Build 12 Factor Microservices on Docker - Part 2](https://www.packtpub.com/books/content/how-to-build-12-factor-design-microservices-on-docker-part-2)
* [Simple flask-db application that was 12 factorized, docker-ized, and kuber-ized](https://github.com/alainchiasson/12factor-app)

----
# The Twelve Factors (summarized from [The Twelve-Factor App site](https://12factor.net/))

This is a summary to quickly understand and learn the 12 factors:
- 1. One codebase tracked in revision control, many deploys.
- 2. Explicitly declare and isolate dependencies
- 3. Store config in the environment
- 4. Treat backing services as attached resources
- 5. Strictly separate build and run stages
- 6. Execute the app as one or more stateless processes
- 7. Export services via port binding
- 8. Scale out via the process model
- 9. Maximize robustness with fast startup and graceful shutdown
- 10. Keep development, staging, and production as similar as possible
- 11. Treat logs as event streams
- 12. Run admin/management tasks as one-off processes


## I. Codebase
#### One codebase tracked in revision control, many deploys
A codebase is any set of repos who share a root commit.

There is only one codebase per app, but there will be many deploys of the app.
A deploy is a running instance of the app.

There is always a one-to-one correlation between the codebase and the app

## II. Dependencies
#### Explicitly declare and isolate dependencies
A twelve-factor app never relies on implicit existence of system-wide packages.

It declares all dependencies, completely and exactly, via a dependency declaration manifest.

Twelve-factor apps also do not rely on the implicit existence of any system tools.

If the app needs to shell out to a system tool, that tool should be vendored into the app.


## III. Config
#### Store config in the environment
An app’s *config* is everything that is likely to vary between deploys.

Store config as constants in the code is a violation of twelve-factor.

Twelve-factor requires **strict separation of config from code**. 

**The twelve-factor app stores config in environment variables** (often shortened to env vars or env).

Env vars are easy to change between deploys without changing any code.

## IV. Backing services
#### Treat backing services as attached resources
A *backing service* is any service the app consumes over the network as part of its normal operation.

These services can be local or provided by third-parties.

**The code for a twelve-factor app makes no distinction between local and third party services.**


To the app, both are attached resources, accessed via a URL or other locator/credentials stored in the config.

A twelve-factor app should be able to swap out a local service with an equivalent external one without any changes to the app’s code.

Resources can be attached and detached to deploys at will without any code changes.

## V. Build, release, run
#### Strictly separate build and run stages

**The twelve-factor app uses strict separation between the build, release, and run stages.**
##### - Build:
Converts a code repo into an executable bundle.

Builds are initiated by the app’s developers whenever new code is deployed.

Build stage can be complex, since errors are always in the foreground for a developer who is driving the deploy.

##### - Release: 
Combines *build* with the deploy’s current config and is ready for immediate execution.

Every release should always have a unique release ID (timestamp, incrementing number...).

A release cannot be mutated once it is created.

##### - Run:
Runs the app in the execution environment.

The run stage should be kept to as few moving parts as possible.


## VI. Processes
#### Execute the app as one or more stateless processes
**Twelve-factor processes are stateless and share-nothing.**

Any data that needs to persist must be stored in a stateful backing service, typically a database.

The memory space or filesystem of the process can be used as a brief, single-transaction cache. For example, downloading a large file, operating on it, and storing the results of the operation in the database.

The twelve-factor app never assumes that anything cached in memory or on disk will be available on a future request or job.

## VII. Port binding
#### Export services via port binding
**The twelve-factor app is completely self-contained** and does not rely on runtime injection of a webserver into the execution environment to create a web-facing service.

The web app **exports HTTP as a service by binding to a port**, and listening to requests coming in on that port.

## VIII. Concurrency
#### Scale out via the process model
I have not understood a word here :-(


## IX. Disposability
#### Maximize robustness with fast startup and graceful shutdown
**The twelve-factor app’s processes are disposable, meaning they can be started or stopped at a moment’s notice.**

Processes **shut down gracefully when they receive a SIGTERM** signal from the process manager.

Processes should also be **robust against sudden death**, in the case of a failure in the underlying hardware.

A recommended approach is use of a robust queueing backend, such as *Beanstalkd*, that returns jobs to the queue when clients disconnect or time out. 


## X. Dev/prod parity
#### Keep development, staging, and production as similar as possible
**The twelve-factor app is designed for continuous deployment by keeping the gap between development and production small.**

` `|**Traditional app**|**Twelve-factor app**
---|---|---|
**Time between deploys**|Weeks|Hours
**Code authors vs code deployers**|Different people|Same people
**Dev vs production environments**|Divergent|As similar as possible

**The twelve-factor developer resists the urge to use different backing services between development and production.**

Adapters to different backing services are still useful, because they make porting to new backing services relatively painless.

But **all deploys of the app** (developer environments, staging, production) should be using the **same type and version** of each of the backing services.

## XI. Logs
#### Treat logs as event streams
**A twelve-factor app never concerns itself with routing or storage of its output stream.**

It should not attempt to write to or manage logfiles. Instead, each running process writes its event stream, unbuffered, to **stdout**.

In staging or production deploys, each process’ stream will be captured by the execution environment, collated together with all other streams from the app, and routed to one or more final destinations (i.e. log indexing and analysis system) for viewing and long-term archival.

## XII. Admin processes
#### Run admin/management tasks as one-off processes
One-off admin processes should be run in an identical environment as the regular long-running processes of the app.

Admin code must ship with application code to avoid synchronization issues.

The same dependency isolation techniques should be used on all process types.

For example, a Python program using Virtualenv should use the vendored bin/python for running both the Tornado webserver and any manage.py admin processes.
