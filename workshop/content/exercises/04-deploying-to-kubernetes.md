We now need a deployment resource for Kubernetes to describe how to deploy the container image. Start out by creating a file `deployment.yaml` containing the following deployment description.

```editor:append-lines-to-file
file: ~/exercises/demo/deployment.yaml
text: |
  apiVersion: apps/v1
  kind: Deployment
  metadata:
    name: demo
    labels:
      app: demo
  spec:
    replicas: 1
    selector:
      matchLabels:
        app: demo
    template:
      metadata:
        labels:
          app: demo
      spec:
        containers:
        - name: demo
          image: docker.io/samarsinghal/probe:latest
```

The deployment will use the `default` service account, which already has the image pull secret for the image registry associated with it, so all we have to do is provide the name of our container image.

We now need to add the configuration for the liveness and readiness probes. These need to be added to the definition of the container within the pod template.

```editor:insert-value-into-yaml
file: ~/exercises/demo/deployment.yaml
path: spec.template.spec.containers[0]
value:
  livenessProbe:
    httpGet:
      path: /actuator/health/liveness
      port: 8080
    initialDelaySeconds: 10
    periodSeconds: 3
  readinessProbe:
    httpGet:
      path: /actuator/health/readiness
      port: 8080
    initialDelaySeconds: 60
    periodSeconds: 10
```

The URL paths `/actuator/health/liveness` and `/actuator/health/readiness` are the endpoints for the respective probes exposed by the actuator module when the application is being run in Kubernetes. You can find out more details on using these Kubernetes probes in the Spring Boot [documentation](https://docs.spring.io/spring-boot/docs/current/reference/htmlsingle/#production-ready-kubernetes-probes).

Create the deployment by running:

```execute
kubectl apply -f deployment.yaml
```

The output should be:

```
deployment.apps/demo created
```

To monitor the deployment, run:

```execute
kubectl rollout status deployment/demo
```

Let's have a look at the pods:

```execute-2
kubectl get pods
```

This pod is not running yet, since the readiness probe initialDelaySeconds is set to 60 sec. As expected, the readiness probe is used by Kubernetes to check if this app is ready.


To test that the endpoints for the liveness and readiness probes are working, we need to be able to connect to the application. Normally you would have created a service for the application, as well as exposed it outside of the cluster using an ingress or via a load balancer. Since we haven't done that, we will set up port forwarding to expose the application to the local environment.

```execute
kubectl port-forward deployment/demo 8080:8080
```

To check that the readiness endpoint is working, run:

```execute-2
curl localhost:8080/actuator/health/readiness
```

To check that the liveness endpoint is working, run:

```execute-2
curl localhost:8080/actuator/health/liveness
```

In both cases they should respond with:

```
{"status":"UP"}
```


Note: If all instances of an application are unready, a Kubernetes Service with type=ClusterIP or NodePort will not accept any incoming connections. There is no HTTP error response (503 etc.) since there is no connection. A Service with type=LoadBalancer might or might not accept connections, depending on the provider. A Service that has an explicit Ingress will also respond in a way that depends on the implementation - the ingress service itself will have to decide how to handle the "connection refused" from downstream. HTTP 503 is quite likely in the case of both load balancer and ingress.