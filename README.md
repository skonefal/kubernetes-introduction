# First steps

1. Install docker https://docs.docker.com/engine/install/
2. Get Minikube https://minikube.sigs.k8s.io/docs/start/

```bash
$ curl -LO https://storage.googleapis.com/minikube/releases/latest/minikube-linux-amd64
$ sudo install minikube-linux-amd64 /usr/local/bin/minikube
$ minikube start
```
Configure Minikube's kubectl for easier usage:
```bash
$ ln -s $(which minikube) /usr/local/bin/kubectl
```

# Get around the cluster

kubectl documentation: https://jamesdefabia.github.io/docs/user-guide/kubectl/kubectl/

## Get some information

Get all pods in the cluster
```bash
ubuntu@perspektywy:~$ kubectl get pods -A
NAMESPACE     NAME                               READY   STATUS    RESTARTS   AGE
kube-system   coredns-74ff55c5b-p4925            1/1     Running   0          12h
kube-system   etcd-minikube                      1/1     Running   0          12h
kube-system   kube-apiserver-minikube            1/1     Running   0          12h
kube-system   kube-controller-manager-minikube   1/1     Running   0          12h
kube-system   kube-proxy-7c29r                   1/1     Running   0          12h
kube-system   kube-scheduler-minikube            1/1     Running   0          12h
kube-system   storage-provisioner                1/1     Running   0          12h
ubuntu@perspektywy:~$
```

Get all nodes in the cluster
```bash
ubuntu@perspektywy:~$ kubectl get nodes
NAME       STATUS   ROLES                  AGE   VERSION
minikube   Ready    control-plane,master   12h   v1.20.2
ubuntu@perspektywy:~$ 
```

Get more details about pods
```bash
ubuntu@perspektywy:~$ kubectl get pods -A -o wide
NAMESPACE     NAME                               READY   STATUS    RESTARTS   AGE   IP             NODE       NOMINATED NODE   READINESS GATES
kube-system   coredns-74ff55c5b-p4925            1/1     Running   0          12h   172.17.0.2     minikube   <none>           <none>
kube-system   etcd-minikube                      1/1     Running   0          12h   192.168.49.2   minikube   <none>           <none>
kube-system   kube-apiserver-minikube            1/1     Running   0          12h   192.168.49.2   minikube   <none>           <none>
kube-system   kube-controller-manager-minikube   1/1     Running   0          12h   192.168.49.2   minikube   <none>           <none>
kube-system   kube-proxy-7c29r                   1/1     Running   0          12h   192.168.49.2   minikube   <none>           <none>
kube-system   kube-scheduler-minikube            1/1     Running   0          12h   192.168.49.2   minikube   <none>           <none>
kube-system   storage-provisioner                1/1     Running   0          12h   192.168.49.2   minikube   <none>           <none>
ubuntu@perspektywy:~$
```

## Deploy something!

Deploy nginx container
```bash
ubuntu@perspektywy:~$ kubectl run nginx --image=nginx                                                                                                                                                                                                         
pod/nginx created                                                                                                                                                                                                                                             
ubuntu@perspektywy:~$ kubectl describe pod nginx
(...)
ubuntu@perspektywy:~$ kubectl get pods -o wide                                                                                  
NAME    READY   STATUS    RESTARTS   AGE     IP           NODE          NOMINATED NODE   READINESS GATES
nginx   1/1     Running   0          2m13s   172.17.0.3   perspektywy   <none>           <none>
ubuntu@perspektywy:~$ curl 172.17.0.3:80
<!DOCTYPE html>
<html>
<head>
<title>Welcome to nginx!</title>
<style>
    body {
        width: 35em;
        margin: 0 auto;
        font-family: Tahoma, Verdana, Arial, sans-serif;
    }
</style>
</head>
<body>
<h1>Welcome to nginx!</h1>
<p>If you see this page, the nginx web server is successfully installed and
working. Further configuration is required.</p>

<p>For online documentation and support please refer to
<a href="http://nginx.org/">nginx.org</a>.<br/>
Commercial support is available at
<a href="http://nginx.com/">nginx.com</a>.</p>

<p><em>Thank you for using nginx.</em></p>
</body>
</html>
ubuntu@perspektywy:~$
```

Deploy Kubernetes Dashboard
```bash
kubectl apply -f https://raw.githubusercontent.com/kubernetes/dashboard/v2.2.0/aio/deploy/recommended.yaml
kubectl apply -f manifests/kubernetes-dashboard-rbac.yml
```
How to get token for dashboard login?
```bash
ubuntu@perspektywy:~$ kubectl get secrets -n kubernetes-dashboard
NAME                               TYPE                                  DATA   AGE
default-token-7ll9s                kubernetes.io/service-account-token   3      76m
kubernetes-dashboard-certs         Opaque                                0      76m
kubernetes-dashboard-csrf          Opaque                                1      76m
kubernetes-dashboard-key-holder    Opaque                                2      76m
kubernetes-dashboard-token-8fln6   kubernetes.io/service-account-token   3      76m
ubuntu@perspektywy:~$ kubectl describe secret kubernetes-dashboard-token-8fln6 -n kubernetes-dashboard
Name:         kubernetes-dashboard-token-8fln6
Namespace:    kubernetes-dashboard
Labels:       <none>
Annotations:  kubernetes.io/service-account.name: kubernetes-dashboard
              kubernetes.io/service-account.uid: e625d129-45a9-4b1d-a36b-c1f73abe55f5

Type:  kubernetes.io/service-account-token

Data
====
token:      eyJhbGciOiJSUzI1NiIsImtpZCI6Im9wTUw3QlltM0hzT2NSbXlDc0dMVEpOV0puRUJCdjdBNTlJQVl4bUlITlkifQ.eyJpc3MiOiJrdWJlcm5ldGVzL3NlcnZpY2VhY2NvdW50Iiwia3ViZXJuZXRlcy5pby9zZXJ2aWNlYWNjb3VudC9uYW1lc3BhY2UiOiJrdWJlcm5ldGVzLWRhc2hib2FyZCIsImt1YmVybmV0ZXMuaW8vc2VydmljZWFjY291bnQvc2VjcmV0Lm5hbWUiOiJrdWJlcm5ldGVzLWRhc2hib2FyZC10b2tlbi04ZmxuNiIsImt1YmVybmV0ZXMuaW8vc2VydmljZWFjY291bnQvc2VydmljZS1hY2NvdW50Lm5hbWUiOiJrdWJlcm5ldGVzLWRhc2hib2FyZCIsImt1YmVybmV0ZXMuaW8vc2VydmljZWFjY291bnQvc2VydmljZS1hY2NvdW50LnVpZCI6ImU2MjVkMTI5LTQ1YTktNGIxZC1hMzZiLWMxZjczYWJlNTVmNSIsInN1YiI6InN5c3RlbTpzZXJ2aWNlYWNjb3VudDprdWJlcm5ldGVzLWRhc2hib2FyZDprdWJlcm5ldGVzLWRhc2hib2FyZCJ9.oyRYn-hQD5Ak6Y9CfrsNx1-5LLXDoKgddJcuyP9iCzlzhrYHyChnwq4k5OD7PsIzHnAFJc1fGa7ZjaqFCvPP9MDVN4ZLJbo0grgzQBfJmpQq_WYIfJyTywDKC_hUKS--o5vG994ksP5Kv2F5u3d-IE0KUeKbZNEWVoKNWhlcbYCTobdANBd3z2fDRISHNPnVWEumFGac09_so8YwtqUwWS5RkLlWh_OHNIcCdkFvJVgx9A8tn0xvNOQ_tS5WSZiFoEcAfjKFcQ9642489Q3bbj19L2NES4GmLz9frUOewX_0EJnzghIQxREQn9joL2CqkgXzOPOHkrD_MKSYLTpjow
ca.crt:     1111 bytes
namespace:  20 bytes
ubuntu@perspektywy:~$
```

Insert the token on the login screen.


## Deploy from the manifest file

```bash
kubectl apply -f manifests/nginx-deployment.yml
```
Check if it works and try to delete one pod  :
```bash
ubuntu@perspektywy:~/manifests$ kubectl get deployments
NAME       READY   UP-TO-DATE   AVAILABLE   AGE
my-nginx   2/2     2            2           9m26s
ubuntu@perspektywy:~/manifests$ kubectl  get pods
NAME                        READY   STATUS    RESTARTS   AGE
my-nginx-5b56ccd65f-8tchs   1/1     Running   0          8m26s
my-nginx-5b56ccd65f-xw8gf   1/1     Running   0          9m29s
ubuntu@perspektywy:~/manifests$ kubectl  delete pod my-nginx-5b56ccd65f-wmpfx
pod "my-nginx-5b56ccd65f-wmpfx" deleted
ubuntu@perspektywy:~/manifests$ kubectl get pods 
NAME                        READY   STATUS    RESTARTS   AGE
my-nginx-5b56ccd65f-8tchs   1/1     Running   0          50s
my-nginx-5b56ccd65f-xw8gf   1/1     Running   0          113s
ubuntu@perspektywy:~/manifests$ 
```

### Check how service works

```bash
kubectl apply -f manifests/nginx-service.yml
```

```bash
ubuntu@perspektywy:~/manifests$ kubectl get svc
NAME         TYPE        CLUSTER-IP    EXTERNAL-IP   PORT(S)   AGE
kubernetes   ClusterIP   10.96.0.1     <none>        443/TCP   95m
my-nginx     ClusterIP   10.99.195.6   <none>        80/TCP    29s
```
```bash
ubuntu@perspektywy:~/manifests$ curl 10.99.195.6:80
<title>Welcome to nginx!</title>
```

Get inside a container and curl the service name
```bash
ubuntu@perspektywy:~/manifests$ kubectl  exec -ti my-nginx-5b56ccd65f-8tchs -- bash
root@my-nginx-5b56ccd65f-8tchs:/# curl my-nginx:80
<title>Welcome to nginx!</title>
root@my-nginx-5b56ccd65f-8tchs:/#
```

## Execute some updates ...

```bash
vim manifests/nginx-deployment.yml # and do some changes... ;)
```

### Rollback deployment

```
ubuntu@perspektywy:~/manifests$ kubectl rollout history deployment/my-nginx
deployment.apps/my-nginx 
REVISION  CHANGE-CAUSE
1         kubectl apply --cluster=minikube --filename=nginx-deployment.yml --record=true
2         kubectl apply --cluster=minikube --filename=nginx-deployment.yml --record=true

ubuntu@perspektywy:~/manifests$ 
```

Check revisions
```bash
kubectl rollout history deployment/my-nginx --revision=2
(...)
kubectl rollout history deployment/my-nginx --revision=1
(...)
```
Rollback deployment
```
kubectl rollout undo deployment/my-nginx
```
Check how pods change one-by-one
```baSH
ubuntu@perspektywy:~/manifests$ kubectl get pods -o wide
NAME                        READY   STATUS              RESTARTS   AGE     IP            NODE          NOMINATED NODE   READINESS GATES
my-nginx-5b56ccd65f-4225c   1/1     Running             0          13s     172.17.0.3    perspektywy   <none>           <none>
my-nginx-5b56ccd65f-dbdkm   1/1     Running             0          13s     172.17.0.6    perspektywy   <none>           <none>
my-nginx-5b56ccd65f-jhxjn   0/1     ContainerCreating   0          0s      <none>        perspektywy   <none>           <none>
my-nginx-5b56ccd65f-qgzjf   0/1     ContainerCreating   0          5s      <none>        perspektywy   <none>           <none>
my-nginx-84cd958cc7-6tlfc   1/1     Terminating         0          7m      172.17.0.9    perspektywy   <none>           <none>
my-nginx-84cd958cc7-bztds   1/1     Running             0          7m      172.17.0.10   perspektywy   <none>           <none>
my-nginx-84cd958cc7-ghdb9   0/1     Terminating         0          6m43s   172.17.0.8    perspektywy   <none>           <none>
ubuntu@perspektywy:~/manifests$
```

# More informations

Minikube
1. https://kubernetes.io/docs/tutorials/hello-minikube/
1. https://minikube.sigs.k8s.io/docs/start/

Kubectl
1. https://kubernetes.io/docs/reference/kubectl/cheatsheet/
1. https://jamesdefabia.github.io/docs/user-guide/kubectl/kubectl_run/

Deployments    
1. https://kubernetes.io/docs/concepts/workloads/controllers/deployment/
1. https://kubernetes.io/docs/tasks/run-application/run-stateless-application-deployment/

Dashboard
1. https://kubernetes.io/docs/tasks/access-application-cluster/web-ui-dashboard/
1. https://github.com/kubernetes/dashboard/issues/4179#issuecomment-762225102
