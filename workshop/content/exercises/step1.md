Kubernetes has 3 types of probe. The most important are liveness and readiness. Not all apps need probes, and not all apps need readiness if they have a liveness probe, and vice versa. Spring Boot includes out-of-the box support for the commonly used “liveness” and “readiness” availability states. If you are using Spring Boot’s “actuator” support then these states are exposed as health endpoint groups and they map to `/actuator/health/liveness` (liveness) and `/actuator/health/readiness` (readiness). These endpoints are provided out of the box by the [Spring Boot Actuators](https://docs.spring.io/spring-boot/docs/current/reference/htmlsingle/#production-ready-endpoints) feature, but only exposed by default in a Kubernetes environment. You can read more about Kubernetes probes in Spring Boot [in the user guide](https://docs.spring.io/spring-boot/docs/current/reference/htmlsingle/#production-ready-kubernetes-probes).

> NOTE: The `/health` and `/info` endpoints are actually the *only* actuators enabled and exposed over HTTP by default. You don't have to worry about the others until you want to use them. You can use `/info` as a kind of informal liveness probe (works in older versions too).

If a readiness probe fails then the app is taken out of the load balancer rotation, and no more traffic is sent to it, until it passes again. It can continue to pass liveness checks the whole time it is out of rotation.

Click here to open the pom.xml in the editor.

```editor:open-file
file: exercises/demo/pom.xml
line: 21
```

Then add the actuator dependency:

```editor:insert-lines-before-line
file: exercises/demo/pom.xml
text: |+2
          <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-actuator</artifactId>
          </dependency>

line: 21
```

In the terminal, pop into the `demo` director and build the app:

```execute
cd demo && ./mvnw package -U
```

Then create a docker container:

```execute
./mvnw spring-boot:build-image -Dspring-boot.build-image.imageName={{ registry_host }}/springguides/demo
```

You can test that the container is working:

```execute
docker run -p 8080:8080 {{ registry_host }}/springguides/demo
```

Sample output:

```
  .   ____          _            __ _ _
 /\\ / ___'_ __ _ _(_)_ __  __ _ \ \ \ \
( ( )\___ | '_ | '_| | '_ \/ _` | \ \ \ \
 \\/  ___)| |_)| | | | | || (_| |  ) ) ) )
  '  |____| .__|_| |_|_| |_\__, | / / / /
 =========|_|==============|___/=/_/_/_/
 :: Spring Boot ::        (v2.2.1.RELEASE)

2019-11-27 10:13:54.838  INFO 1 --- [           main] com.example.demo.DemoApplication         : Starting DemoApplication on 051fa7c2fe52with PID 1 (/app started by root in /)
2019-11-27 10:13:54.842  INFO 1 --- [           main] com.example.demo.DemoApplication         : No active profile set, falling back to default profiles: default
2019-11-27 10:14:00.070  INFO 1 --- [           main] trationDelegate$BeanPostProcessorChecker : Bean 'org.springframework.transaction.annotation.ProxyTransactionManagementConfiguration' of type [org.springframework.transaction.annotation.ProxyTransactionManagementConfiguration] is not eligible for getting processed by all BeanPostProcessors (for example: not eligible for auto-proxying)
2019-11-27 10:14:01.093  INFO 1 --- [           main] o.s.b.a.e.web.EndpointLinksResolver      : Exposing 2 endpoint(s) beneath base path'/actuator'
2019-11-27 10:14:01.809  INFO 1 --- [           main] o.s.b.web.embedded.netty.NettyWebServer  : Netty started on port(s): 8080
2019-11-27 10:14:01.816  INFO 1 --- [           main] com.example.demo.DemoApplication         : Started DemoApplication in 1.793 seconds(JVM running for 2.061)
```

If the Actuator endpoints are there you can test with

```execute-2
curl localhost:8080/actuator/health
```

```
{"status":"UP"}
```

Once you are sure it is working you can kill the container:

```execute
<ctrl-c>
```

And push it to the local registry:

```execute
docker push {{ registry_host }}/springguides/demo
```

> NOTE: There is a Docker (v2) registry running on `{{ registry_host }}` port 80, just to make the tutorial work smoothly, and so you don't have to authenticate to Dockerhub. If you prefer to use Dockerhub just remove `{{ registry_host }}` from the container labels (or insert another registry host instead).
