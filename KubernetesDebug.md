# Helpful Kubernetes Debug Tips

Run an interactive pod in the cluster:
```shell
kubectl run -it busybox --image=busybox --restart=Never -n develocity --rm=true -- sh
```

Run the Minio Admin client in the cluster:
```shell
kubectl run -it mc --image=minio/mc --restart=Never -n develocity --rm=true --command -- /bin/sh
```

Read a single secret value:
```shell
kubectl -n develocity get secret gradle-default-system-password-secret --template={{.data.password}} | base64 --decode
kubectl -n develocity get secret gradle-ingress-ssl-secret -o=jsonpath={.data.tls.key}
```

Read all secret values:
```shell
kubectl get secret gradle-ingress-ssl-secret -n develocity -o json | jq '.data | map_values(@base64d)'
```

Get superuser credentials for Minio
```shell
kubectl get secret gradle-embedded-object-storage-superuser-credentials -n develocity -o json | jq '.data | map_values(@base64d)'
```
