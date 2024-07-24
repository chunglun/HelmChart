# HelmChart

## Install kubernetes dashboard
- Add kubernetes-dashboard repository
```
helm repo add kubernetes-dashboard https://kubernetes.github.io/dashboard/
```
- Deploy a Helm Release named "kubernetes-dashboard" using the kubernetes-dashboard chart
```
helm upgrade --install kubernetes-dashboard kubernetes-dashboard/kubernetes-dashboard --create-namespace --namespace kubernetes-dashboard
```

- To access Dashboard
```
kubectl -n kubernetes-dashboard port-forward svc/kubernetes-dashboard-kong-proxy 8443:443
```

Dashboard will be available at:
  https://localhost:8443

## Create a user for kube dashboard

```
apiVersion: v1
kind: ServiceAccount
metadata:
  name: admin-user
  namespace: kubernetes-dashboard
```

```
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRoleBinding
metadata:
  name: admin-user
roleRef:
  apiGroup: rbac.authorization.k8s.io
  kind: ClusterRole
  name: cluster-admin
subjects:
- kind: ServiceAccount
  name: admin-user
  namespace: kubernetes-dashboard
```

- Getting a Bearer Token for ServiceAccount
```
kubectl -n kubernetes-dashboard create token admin-user
```
```
eyJhbGciOiJSUzI1NiIsImtpZCI6InFNZ1RZaV8wcjVxQ21QMnYyNnZqNGhQcG9uZXlJTUFOWnZaWkpQXzNOUEkifQ.eyJhdWQiOlsiaHR0cHM6Ly9rdWJlcm5ldGVzLmRlZmF1bHQuc3ZjLmNsdXN0ZXIubG9jYWwiXSwiZXhwIjoxNzIxODIzODUzLCJpYXQiOjE3MjE4MjAyNTMsImlzcyI6Imh0dHBzOi8va3ViZXJuZXRlcy5kZWZhdWx0LnN2Yy5jbHVzdGVyLmxvY2FsIiwia3ViZXJuZXRlcy5pbyI6eyJuYW1lc3BhY2UiOiJrdWJlcm5ldGVzLWRhc2hib2FyZCIsInNlcnZpY2VhY2NvdW50Ijp7Im5hbWUiOiJhZG1pbi11c2VyIiwidWlkIjoiNzM1N2E0OTMtMmRhMS00YmU2LWI2NmItN2E4ZmYwNDM2ZDkyIn19LCJuYmYiOjE3MjE4MjAyNTMsInN1YiI6InN5c3RlbTpzZXJ2aWNlYWNjb3VudDprdWJlcm5ldGVzLWRhc2hib2FyZDphZG1pbi11c2VyIn0.KUswoK3_4d9ovZtxMkp2e0OPzeI7Oa7DVdvvo8wPKEAHiFRVPi925x1zGHR5sMMvOjC8bZiN_gVIsK15uwM_RvLKzXbXe1z5-roo57xMez0SuwMo-i9JnBuaZ_1E-dxAG-QbQVbRr6DQV5136C7UVCWkb9nRJK0O5A7V857ewvCvCc6TCWMswdy_2W1GcCWtlC3FPURY0Z9Qp0VqZttJSZyCi1gUPPQhBgjRju1pHJimquDlzpTAlZ-svZ2DhnQ-j4XJx2rvC0QJYVgAQMp4H7DggdSDKT5Q3jISCzpOq5rNdhTMQJo1jVXCwnVe4lQD9w7aZgTgEEA3vhd1k4eCaw
```

- Getting a long-lived Bearer Token for ServiceAccount
```
apiVersion: v1
kind: Secret
metadata:
  name: admin-user
  namespace: kubernetes-dashboard
  annotations:
    kubernetes.io/service-account.name: "admin-user"   
type: kubernetes.io/service-account-token  
```
```
kubectl get secret admin-user -n kubernetes-dashboard -o jsonpath={".data.token"} | base64 -d
```

## Reference
[kube dashboard](https://kubernetes.io/docs/tasks/access-application-cluster/web-ui-dashboard/)