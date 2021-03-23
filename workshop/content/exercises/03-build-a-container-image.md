We are ready now to build the application. we want to deploy the application to Kubernetes, we need to create a container image which bundles up the application.

To create the container image, we are going to use the Maven `spring-boot:build-image` command. This will build us a container image without needing to worry about a `Dockerfile`.

```execute
./mvnw spring-boot:build-image -Dspring-boot.build-image.imageName={{ registry_host }}/springguides/demo
```

To test that the container image works run:

```execute
docker run -p 8080:8080 {{ registry_host }}/springguides/demo
```

You should see the startup messages for the Spring Boot application.

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

If the actuator endpoints have been successfully added to the application, you should be able to make a request against the URL path of `/actuator/health`.

```execute-2
curl localhost:8080/actuator/health
```

The response should be:

```
{"status":"UP"}
```

> NOTE: You will not be able to exercise the `/actuator/health/readiness` and `/actuator/health/liveness` endpoints at this point, as they are only enabled when the application is run inside of Kubernetes.

Once you are happy the main health endpoint is working kill the container:

```terminal:interrupt
```
