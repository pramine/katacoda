Now as we have seen how our service in live in acction, let's fiddle a bit with the _readiness probe_

Let's have a quick look on the pods again:

`kubectl get pods`{{execute}}

Do you spot where the result of the latest readiness probe is shown ?

First, let's store the Pod name in a shell variable `$pod` :

`pod=$(kubectl get pods -l app=random-generator -o name | sed -e 's|pod/||')`{{execute}}

As we have seen in the last step, the readiness probe is defined by checking the existance of a trigger file `/opt/random-generator-ready`. This file is automatically generated by our sample application when it's ready to serve.

You can check it:

`kubectl exec $pod -- ls -l /opt/random-generator-ready`{{execute}}

Let's remove the file with

`kubectl exec $pod -- rm /opt/random-generator-ready`{{execute}}

and check what's this makes to our Pod

`kubectl get pods -w`{{execute}}

It will take probably a bit (until the next check is scheduled), but eventually we will end up with 0 ready pods.

What happens when you now access our service ?

`curl -s http://[[HOST_IP]]:31667/ | jq .`{{execute interrupt}}

Please note that the Pod is not restarted, it's just out-of-service and hence you get no response, as we only have a single pod scheduled.

You can re-enable easily the sevice with

`kubectl exec $pod -- touch /opt/random-generator-ready`{{execute}}

An alternative (not shown here) would be to scale up our Pods to create some Pods which are in-service.

In the next step let's check how Pods are restarted by a failing liveness probe.
