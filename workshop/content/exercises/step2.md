
{% include "code-server/package.liquid" %}

Quickly create a deployment manifest for the application on Kubernetes:

```execute
kubectl create deployment demo --image={{ REGISTRY_HOST }}/springguides/demo --dry-run -o=yaml > deployment.yaml \
&& echo --- >> deployment.yaml \
&& kubectl create service clusterip demo --tcp=8080:8080 --dry-run -o=yaml >> deployment.yaml
```

We need to add an image pull secret, so that Kubernetes can pull our image from the local registry. So add this to the deployment spec:

<pre class="pastable" data-file="/home/eduk8s/exercises/demo/deployment.yaml" data-yaml-path="spec.template.spec">
imagePullSecrets:
  - name: registry-credentials
</pre>

Let's add some configuration to the deployment for probes, as would be typical for an app using Spring Boot actuators. Add this YAML snippet to the container spec:

<pre class="pastable" data-file="/home/eduk8s/exercises/demo/deployment.yaml" data-yaml-path="spec.template.spec.containers[0]">
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
  initialDelaySeconds: 20
  periodSeconds: 10
</pre>

Push the app to the cluster:

```execute
kubectl apply -f deployment.yaml
```

```
deployment.apps/demo created
service/demo created
```

> NOTE: You can also use the Kubernetes extension in the IDE to deploy YAML files, to inspect the cluster, forward ports, etc.

Check that the application is running:

```execute
kubectl get all
```

```
NAME                             READY     STATUS      RESTARTS   AGE
pod/demo-658b7f4997-qfw9l        1/1       Running     0          146m

NAME                 TYPE        CLUSTER-IP      EXTERNAL-IP   PORT(S)    AGE
service/kubernetes   ClusterIP   10.43.0.1       <none>        443/TCP    2d18h
service/demo         ClusterIP   10.43.138.213   <none>        8080/TCP   21h

NAME                   READY     UP-TO-DATE   AVAILABLE   AGE
deployment.apps/demo   1/1       1            1           21h

NAME                              DESIRED   CURRENT   READY     AGE
replicaset.apps/demo-658b7f4997   1         1         1         21h
d
```

> TIP: Keep doing `kubectl get all` until the demo pod shows its status as "Running".

Now you need to be able to connect to the application, which you have exposed as a Service in Kubernetes. One way to do that, which works great at development time, is to create an SSH tunnel:

```execute
kubectl port-forward svc/demo 8080:8080
```

then you can verify that the app [is running](//{{ session_namespace }}-application.{{ ingress_domain }}/actuator/health):

```execute-2
curl localhost:8080/actuator/health
```