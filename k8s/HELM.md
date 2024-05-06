## Task 1: Helm Setup and Chart Creation

1. Check via `kubectl get pods,svc` command:

![](./images/task1.jpg)

## Task 2: Helm hooks

## Helm Chart Hooks
Lint the Helm chart
```
helm lint app-python/
==> Linting app-python/
[INFO] Chart.yaml: icon is recommended
1 chart(s) linted, 0 chart(s) failed
```
Perform dry-run of the Helm chart (output is truncated)
```
NAME: helm-hooks
LAST DEPLOYED: Mon Apr  8 20:11:32 2024
NAMESPACE: default
STATUS: pending-install
REVISION: 1
HOOKS:
# Source: app-python/templates/post-install-hook.yaml
apiVersion: v1
kind: Pod
metadata:
  name: postinstall-hook
  annotations:
    "helm.sh/hook": "post-install"
```
Verify the Helm chart hooks (without hook delete policy)
- `kubectl get po`
```
NAME                          READY   STATUS      RESTARTS   AGE
app-python-6b5bdb53f8-fb34s   1/1     Running     0          40s
postinstall-hook              0/1     Completed   0          40s
preinstall-hook               0/1     Completed   0          62s
```
- `kubectl describe po preinstall-hook`
```
Name:             preinstall-hook
Namespace:        default
Priority:         0
Service Account:  default
Node:             minikube/192.168.148.2
Start Time:       Mon, 08 Apr 2024 21:19:11 +0300
Labels:           <none>
Annotations:      helm.sh/hook: pre-install
Status:           Succeeded
IP:               10.244.0.59
IPs:
  IP:  10.244.0.59
Containers:
  pre-install-container:
    Container ID:  docker://31698cca031de339ada8d7he4db58e5e4154c905da2b00bf070bbc0deef33d5d
    Image:         busybox
    Image ID:      docker-pullable://busybox@sha256:c3839dd800b9eb7603340509769c43e146a74c63dca3045a8e7dc8ee07e53966
    Port:          <none>
    Host Port:     <none>
    Command:
      sh
      -c
      echo The pre-install hook is running && sleep 20
    State:          Terminated
      Reason:       Completed
      Exit Code:    0
      Started:      Mon, 08 Apr 2024 21:19:11 +0300
      Finished:     Mon, 08 Apr 2024 21:19:31 +0300
    Ready:          False
    Restart Count:  0
    Environment:    <none>
    Mounts:
      /var/run/secrets/kubernetes.io/serviceaccount from kube-api-access-lpz4q (ro)
Conditions:
  Type              Status
  Initialized       True 
  Ready             False 
  ContainersReady   False 
  PodScheduled      True 
Volumes:
  kube-api-access-lpz4q:
    Type:                    Projected (a volume that contains injected data from multiple sources)
    TokenExpirationSeconds:  3607
    ConfigMapName:           kube-root-ca.crt
    ConfigMapOptional:       <nil>
    DownwardAPI:             true
QoS Class:                   BestEffort
Node-Selectors:              <none>
Tolerations:                 node.kubernetes.io/not-ready:NoExecute op=Exists for 300s
                             node.kubernetes.io/unreachable:NoExecute op=Exists for 300s
Events:
  Type    Reason     Age   From               Message
  ----    ------     ----  ----               -------
  Normal  Scheduled  98s  default-scheduler  Successfully assigned default/preinstall-hook to minikube
  Normal  Pulled     98s  kubelet            Container image "busybox" already present on machine
  Normal  Created    98s  kubelet            Created container pre-install-container
  Normal  Started    98s  kubelet            Started container pre-install-container
```
- `kubectl describe po postinstall-hook`
```
Name:             preinstall-hook
Namespace:        default
Priority:         0
Service Account:  default
Node:             minikube/192.168.148.2
Start Time:       Mon, 08 Apr 2024 21:19:11 +0300
Labels:           <none>
Annotations:      helm.sh/hook: pre-install
Status:           Succeeded
IP:               10.244.0.59
IPs:
  IP:  10.244.0.59
Containers:
  pre-install-container:
    Container ID:  docker://31698cca031de339a43f7f1e4db58eaasf12821a05da2b00bf070bbc0deef33d5d
    Image:         busybox
    Image ID:      docker-pullable://busybox@sha256:c3839dd800b9eb7603340509769c43e146a74c63dca3045a8e7dc8ee07e53966
    Port:          <none>
    Host Port:     <none>
    Command:
      sh
      -c
      echo The pre-install hook is running && sleep 20
    State:          Terminated
      Reason:       Completed
      Exit Code:    0
      Started:      Mon, 08 Apr 2024 21:19:11 +0300
      Finished:     Mon, 08 Apr 2024 21:19:31 +0300
    Ready:          False
    Restart Count:  0
    Environment:    <none>
    Mounts:
      /var/run/secrets/kubernetes.io/serviceaccount from kube-api-access-lpz4q (ro)
Conditions:
  Type              Status
  Initialized       True 
  Ready             False 
  ContainersReady   False 
  PodScheduled      True 
Volumes:
  kube-api-access-lpz4q:
    Type:                    Projected (a volume that contains injected data from multiple sources)
    TokenExpirationSeconds:  3709
    ConfigMapName:           kube-root-ca.crt
    ConfigMapOptional:       <nil>
    DownwardAPI:             true
QoS Class:                   BestEffort
Node-Selectors:              <none>
Tolerations:                 node.kubernetes.io/not-ready:NoExecute op=Exists for 300s
                             node.kubernetes.io/unreachable:NoExecute op=Exists for 300s
Events:
  Type    Reason     Age   From               Message
  ----    ------     ----  ----               -------
  Normal  Scheduled  98s  default-scheduler  Successfully assigned default/preinstall-hook to minikube
  Normal  Pulled     98s  kubelet            Container image "busybox" already present on machine
  Normal  Created    98s  kubelet            Created container pre-install-container
  Normal  Started    98s  kubelet            Started container pre-install-container
```
Only the application pod in running after enabling the hook delete policy:
- `kubectl get pods,svc`
```
NAME                              READY   STATUS    RESTARTS   AGE
pod/app-python-6b5bdb53f8-fb34s   1/1     Running   0          57s
NAME                 TYPE        CLUSTER-IP      EXTERNAL-IP   PORT(S)          AGE
service/app-python   NodePort    10.96.105.116   <none>        5000:30308/TCP   56s
service/kubernetes   ClusterIP   10.96.0.1       <none>        443/TCP          9d
```