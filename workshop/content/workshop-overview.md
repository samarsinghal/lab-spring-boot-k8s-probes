Spring Boot provides a number of features to ensure it works well with Kubernetes. This workshop shows you how to add [liveness and readiness probes](https://kubernetes.io/docs/concepts/workloads/pods/pod-lifecycle/#container-probes) to a Spring Boot application in Kubernetes. The topics we will cover are:

1. Add some endpoints to a Spring Boot application and build and push a Docker image
2. Configure the probes in a few lines of YAML
3. Deploy the image as a container in Kubernetes

We don't cover all the features of Spring and Spring Boot. For more information you can check out the [Spring guides](https://spring.io/guides) or [Spring project homepages](https://spring.io/projects).

We also don't cover all the options available for building container images. There is a [Spring Boot topical guide](https://spring.io/guides/top/spring-boot-docker) covering some of those options in more detail.

When it comes to deploying the application to Kubernetes, there are far too many options to cover them all in one tutorial. We can look at one or two, but the emphasis will be on getting something working as quickly as possible, not on how to deploy to a production cluster.
