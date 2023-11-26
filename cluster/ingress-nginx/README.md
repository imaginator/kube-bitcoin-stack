Install with 

```bash
helm upgrade --install ingress-nginx ingress-nginx \
  --repo https://kubernetes.github.io/ingress-nginx \
  --namespace ingress-nginx --create-namespace
```

## adding additional TCP and UDP services

[reference](https://minikube.sigs.k8s.io/docs/tutorials/nginx_tcp_udp_ingress/)


add the configmaps:
```
kubectl apply -f ~/Documents/src/openlsp/cluster/nginx-ingress/nginx-tcp-udp-services.yaml
```

check that the configmaps are created:
```
kubectl get configmap -n ingress-nginx
```


edit the ingress-nginx-controller deployment to include the following args:
```
kubectl edit deployment.apps/ingress-nginx-controller -n ingress-nginx
```

ensure it looks something like:

```yaml
spec:
  template:
    spec:
      containers:
      - args:
        - /nginx-ingress-controller
        - --tcp-services-configmap=ingress-nginx/tcp-services
        - --udp-services-configmap=ingress-nginx/udp-services
```

Finally:

```
kubectl patch deployment ingress-nginx-controller --patch "$(cat ingress-nginx-controller-patch.yaml)" -n ingress-nginx
```

