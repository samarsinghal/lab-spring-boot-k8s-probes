Kubernetes implements three types of probes for evaluating the health of your application. These are liveness, readiness and startup probes.

    A startup probe is used to determine whether your application has started up correctly. 

    A readiness probe is used to determine whether your application is in a state where it is able to accept inbound traffic such as HTTP requests. 

    The liveness probe is used to test whether your application is still running.

In the case of a startup or liveness probe failing, Kubernetes will terminate the instance of your application and restart it.

The success or failure of a readiness probe controls whether the IP address of your application pod is included in the list of endpoints associated with a service. Where an application had been running fine, the failure of a readiness probe results in the application instance being taken out of the load balancer rotation for the service, with no more traffic being sent to that instance until the readiness probe once again passes.

The liveness probe is distinct from the readiness probe and will continue to be checked regardless of the state of the readiness probe.

Whether you need any of these probes will depend on your application and how tolerant of failures, or an overloading state, you want to make your application.

Spring Boot includes out-of-the box support for endpoints that can be used in conjunction with the Kubernetes liveness and readiness probes. The endpoints can be enabled by adding the [Spring Boot actuator module](https://docs.spring.io/spring-boot/docs/current/reference/htmlsingle/#production-ready) to your application.
