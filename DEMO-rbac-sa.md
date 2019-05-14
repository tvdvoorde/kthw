```
kubectl run curl --image=maiwj/curl --restart=Never -- sleep 3600
kubectl exec curl -it -- /bin/sh
```

```
TOKEN=invalidtoken
curl https://kubernetes/api --header "Authorization: Bearer $TOKEN" --insecure
TOKEN=$(cat /var/run/secrets/kubernetes.io/serviceaccount/token)
curl https://kubernetes/api --header "Authorization: Bearer $TOKEN" --insecure
curl https://kubernetes/api/v1/namespaces/default/pods --header "Authorization: Bearer $TOKEN" --insecure
```

```
kubectl create serviceaccount podreader
kubectl create role pod-reader --verb=get --verb=list --verb=watch --resource=pods
kubectl create rolebinding podr-view --role=pod-reader --serviceaccount=default:podreader 
kubectl run curl2 --image=maiwj/curl --serviceaccount=podreader --restart=Never -- sleep 3600 
kubectl exec curl2 -it -- /bin/sh
```

```
TOKEN=$(cat /var/run/secrets/kubernetes.io/serviceaccount/token)
curl https://kubernetes/api/v1/namespaces/default/pods --header "Authorization: Bearer $TOKEN" --insecure
```
