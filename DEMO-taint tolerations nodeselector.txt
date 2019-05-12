# Taints & Tolerations & Nodeselector demp

## Requirements

Three node cluster with node names 'node01', 'node02' and 'node03'

## Instructions

```
cat > nginx.yaml <<EOF
apiVersion: apps/v1
kind: Deployment
metadata:
  labels:
    run: nginx
  name: nginx
spec:
  replicas: 5
  selector:
    matchLabels:
      run: nginx
  template:
    metadata:
      labels:
        run: nginx
    spec:
      containers:
      - image: nginx
        name: nginx
      nodeSelector:
        conf: fastcpu
      tolerations: 
        - key: "pref"
          operator: "Equal" 
          value: "cpu"
          effect: "NoSchedule"
EOF


cat > httpd.yaml<<EOF
apiVersion: apps/v1
kind: Deployment
metadata:
  labels:
    run: httpd
  name: httpd
spec:
  replicas: 5
  selector:
    matchLabels:
      run: httpd
  template:
    metadata:
      labels:
        run: httpd
    spec:
      containers:
      - image: httpd
        name: httpd
      nodeSelector:
        conf: highmem
      tolerations: 
        - key: "pref"
          operator: "Equal" 
          value: "mem"
          effect: "NoSchedule"
EOF
```

## Label nodes

```
kubectl label node node01 conf=fastcpu
kubectl label node node02 conf=highmem
```

## Taint Nodes

```
kubectl taint node node01 pref=cpu:PreferNoSchedule
kubectl taint node node02 pref=mem:PreferNoSchedule
```

## Create deployments

```
kubectl create -f nginx.yaml
kubectl create -f httpd.yaml
kubectl run busybox --image=busybox --replicas=5 -- sleep 3600
```

## Observe

```
kubectl get pods
```

## Clean up

```
kubectl delete -f nginx.yaml
kubectl delete -f httpd.yaml
kubectl delete deployment busybox
```





