1. Create a namespace for deployments.
``` 
kubectl create ns statefulset-demo 
```

``` 
kubectl config set-context --current --namespace=statefulset-demo 
```
2. Know about the storage class of your cluster. we will be using this in our volumeclaimtemplates. If not mentioned, the controller will take the default storageclass.

```
kubectl get storageclass
```
3. Create the statefulset

```
kubectl apply -f statefulset.yml
```
4. Because we are using configmaps for the config data, we need to create configmap too.
5. And inorder for the discovery we need to create a headless service too. 

```
kubectl apply -f configmap.yml
```
```
kubectl apply -f service.yml
```
6. Check the Pods created by StatefulSets and its naming order. ( statefulsetname - [ordinal number])
` kubectl get pods`
7. Check the PV created and which pod associated with which PV using. 
```
kubectl get po -o json --all-namespaces | jq -j '.items[] | "\(.metadata.namespace), \(.metadata.name), \(.spec.volumes[].persistentVolumeClaim.claimName)\n"' | grep -v null
```
8. Scaling the pods in Statefulsets. In statefulsets pods are created one by one. 

```
kubectl scale sts/redis-cluster --replicas=5
```
9. Let's scale down the replicas and see what happens to the PV created. (and scaling down is done from highest ordinal numbered pod to lowest)

```
kubectl scale sts/redis-cluster --replicas=3
```
Check the status of the PV. (It will still retain the PV even pod is deleted)

```
kubectl get pvc
```

<!-- 10. Clustering in Redis 

```
kubectl exec -it redis-cluster-0 -- redis-cli --cluster create --cluster-replicas 1 $(kubectl get pods -l app=redis-cluster -o jsonpath='{range.items[*]}{.status.podIP}:6379 ')
```
```
kubectl exec -it redis-cluster-0 -- redis-cli cluster info
``` -->

Remember we had some issues with Deployments, 
- No sticky identity of pods, which is solved by the StatefulSets
- each Pod has saperate PV, even if database crashes the other PV has same data ( if clustering is enabled and it is needed for the Stateful applications )
- Graceful scaling and scaling down is done in StatefulSets



