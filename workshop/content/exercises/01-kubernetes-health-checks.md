Kubernetes implements three types of probes for evaluating the health of your application. These are liveness, readiness and startup probes.

A startup probe is used to determine whether your application has started up correctly. A readiness probe is used to determine whether your application is in a state where it is able to accept inbound traffic such as HTTP requests. The liveness probe is used to test whether your application is still running.

In the case of a startup or liveness probe failing, Kubernetes will terminate your application and restart it.

The success or failure of a readiness probe controls whether the IP address of your application pod is included in the list of endpoints associated with a service. Where an application had been running fine, the failure of a readiness probe results in the application being taken out of the load balancer rotation for the service, with no more traffic being sent to it until the readiness probe once again passes.

The liveness probe is distinct from the readiness probe and will continue to be checked regardless of the state of the readiness probe.

Whether you need any of these probes will depend on your application and how tolerant of failures, or an overloading state, you want to make your application.

Spring Boot includes out-of-the box support for endpoints that can be used in conjunction with the Kubernetes liveness and readiness probes. The endpoints can be enabled by adding the [Spring Boot actuator module](https://docs.spring.io/spring-boot/docs/current/reference/htmlsingle/#production-ready) to your application.

{% comment %}
If you are using Spring Boot’s “actuator” support then these states are exposed as health endpoint groups and they map to `/actuator/health/liveness` (liveness) and `/actuator/health/readiness` (readiness). These endpoints are provided out of the box by the [Spring Boot Actuators](https://docs.spring.io/spring-boot/docs/current/reference/htmlsingle/#production-ready-endpoints) feature, but only exposed by default in a Kubernetes environment. You can read more about Kubernetes probes in Spring Boot [in the user guide](https://docs.spring.io/spring-boot/docs/current/reference/htmlsingle/#production-ready-kubernetes-probes).

> NOTE: The `/health` and `/info` endpoints are actually the *only* actuators enabled and exposed over HTTP by default. You don't have to worry about the others until you want to use them. You can use `/info` as a kind of informal liveness probe (works in older versions too).
{% endcomment %}
