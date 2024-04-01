# K8s scaling with keda

This project is an example of how to use keda to scale deployments in kubernetes, I will show you how to install, configure and give one case of how to use keda for scaling.


## Required:

* k8s Cluster (with metrics server installed)
* helm


## Let's deploy the example app

The first thing that we need to deploy the example-app is create a namespace:

<pre>
kubectl create ns app-scaling
</pre>
After namespace app created, we need deploy the app, for this action run the commad bellow:
<pre>
kubectl apply -f k8s/app-scaling/
</pre>
Let's test if the app is working:
<pre>
kubectl -n app-scaling port-forward svc/app-scaling-svc 8080:8080
</pre>
    
It's working:

![](./img/app-port-forward.png)



## What is keda ?

KEDA is a Kubernetes-based Event Driven Autoscaler. With KEDA, you can drive the scaling of any container in Kubernetes based on the number of events needing to be processed.

### Install keda
In this case I'm using helm to install [keda](https://keda.sh/), but if you want to know more about install, see the official docs.

Adding repo:
<pre>
helm repo add kedacore https://kedacore.github.io/charts
</pre>

Updating the repo:
<pre>
helm repo update
</pre>

Installing:
<pre>
helm install keda kedacore/keda --namespace keda --create-namespace
</pre>

## Let's scale the example app using cpu metrics

<pre>
kubectl apply -f k8s/keda/cpu/
</pre>

Now we can see the HPA was created:
<pre>
kubectl -n app-scaling get hpa
</pre>
output:

![](./img/hpa.png)


## Now let's stress the app to see scale working

In this example I'm using locust to stress the app, you can see the app consuming 0% of CPU below:

![](./img/hpa-start.png)

Let's set a 200 users to access the app:

![](./img/locust.png)

After a few minutes we can see the scale working and 2 replicas up:

![](./img/stress-locust.png)


