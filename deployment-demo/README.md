1. Create a namespace for deployments.
``` 
kubectl create ns deployment-demo 
```

``` 
kubectl config set-context --current --namespace=deployment-demo 
```
2. Create the deployment 

```
kubectl apply -f deployment.yaml
```
3. Verify the deployment using `kubectl get deployment`

4. Create the config maps and PV's

``` 
kubectl apply -f configmap.yml
```
```
kubectl apply -f pv.yml
```
5. Check the pods and its naming convention (as we know it will assign some random hash to it)
``` 
kubectl get pods
```
6. Check for the PV and PVC status.

``` 
kubectl get pv 
kubectl get pvc
```
7. We already knew that all every pod in deployments share the same PV, we can verify that using

```
kubectl get po -o json --all-namespaces | jq -j '.items[] | "\(.metadata.namespace), \(.metadata.name), \(.spec.volumes[].persistentVolumeClaim.claimName)\n"' | grep -v null

```
8. Let's try to scale the deployment replicas from 2 to 4. 
```
kubectl scale deployment/redis-cluster --replicas=4
```
```
kubectl scale deployment/redis-cluster --replicas=2
```

9. So the problems we see now are:
 - No persistant name or DNS name attached to pods.
 - All the Pods share same PV
 - Ungraceful scaling ( it is all random )

Let't take on these problems and see how StatefulSets solves these.

