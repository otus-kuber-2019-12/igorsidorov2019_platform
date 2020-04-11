# strace

Для демонсета меняем образ на последний:
```
      - image: aylei/debug-agent:v0.1.1
```
и добавляем копабилити к тестируемому поду:
```
    securityContext:
      capabilities:
        add:
        - SYS_PTRACE
```

После этого удастся зацепиться strace-ом к нужному процессу в тестируемом поде.

# kit

Тест с netperf без сетевой политики:
```
➜  netperf-operator git:(master) kubectl describe netperf.app.example.com/example

Name:         example
Namespace:    default
Labels:       <none>
Annotations:  kubectl.kubernetes.io/last-applied-configuration:
                {"apiVersion":"app.example.com/v1alpha1","kind":"Netperf","metadata":{"annotations":{},"name":"example","namespace":"default"}}
API Version:  app.example.com/v1alpha1
Kind:         Netperf
Metadata:
  Creation Timestamp:  2020-04-11T17:50:38Z
  Generation:          4
  Resource Version:    37464
  Self Link:           /apis/app.example.com/v1alpha1/namespaces/default/netperfs/example
  UID:                 f438f71a-7c1c-11ea-9ed1-42010ab20035
Spec:
  Client Node:
  Server Node:
Status:
  Client Pod:          netperf-client-42010ab20035
  Server Pod:          netperf-server-42010ab20035
  Speed Bits Per Sec:  1935.16
  Status:              Done
Events:                <none>
```

Тест после втаскивания политики:
```
➜  netperf-operator git:(master) kubectl describe netperf.app.example.com/example
Name:         example
Namespace:    default
Labels:       <none>
Annotations:  kubectl.kubernetes.io/last-applied-configuration:
                {"apiVersion":"app.example.com/v1alpha1","kind":"Netperf","metadata":{"annotations":{},"name":"example","namespace":"default"}}
API Version:  app.example.com/v1alpha1
Kind:         Netperf
Metadata:
  Creation Timestamp:  2020-04-11T17:57:54Z
  Generation:          3
  Resource Version:    38986
  Self Link:           /apis/app.example.com/v1alpha1/namespaces/default/netperfs/example
  UID:                 f7f8667e-7c1d-11ea-9ed1-42010ab20035
Spec:
  Client Node:
  Server Node:
Status:
  Client Pod:          netperf-client-42010ab20035
  Server Pod:          netperf-server-42010ab20035
  Speed Bits Per Sec:  0
  Status:              Started test
Events:                <none>
```

С правильным манифестом DaemonSet iptables-tailes
```
➜  netperf-operator git:(master) kubectl describe pod --selector=app=netperf-operator
Name:           netperf-client-42010ab20035
Namespace:      default
Priority:       0
Node:           gke-cluster-111-default-pool-ea461e6a-jwvv/10.178.0.4
Start Time:     Sat, 11 Apr 2020 21:14:37 +0300
Labels:         app=netperf-operator
                netperf-type=client
Annotations:    cni.projectcalico.org/podIP: 10.60.2.21/32
                kubernetes.io/limit-ranger: LimitRanger plugin set: cpu request for container netperf-client-42010ab20035
Status:         Running
IP:             10.60.2.21
IPs:            <none>
Controlled By:  Netperf/example
Containers:
  netperf-client-42010ab20035:
    Container ID:  docker://8180f596e9403f796f6e7d476f2e7c5dbe0340577e65e45a796c6895b25e9c00
    Image:         tailoredcloud/netperf:v2.7
    Image ID:      docker-pullable://tailoredcloud/netperf@sha256:0361f1254cfea87ff17fc1bd8eda95f939f99429856f766db3340c8cdfed1cf1
    Port:          <none>
    Host Port:     <none>
    Command:
      netperf
      -H
      10.60.1.14
    State:          Running
      Started:      Sat, 11 Apr 2020 21:19:17 +0300
    Last State:     Terminated
      Reason:       Error
      Exit Code:    255
      Started:      Sat, 11 Apr 2020 21:16:51 +0300
      Finished:     Sat, 11 Apr 2020 21:19:01 +0300
    Ready:          True
    Restart Count:  2
    Requests:
      cpu:        100m
    Environment:  <none>
    Mounts:
      /var/run/secrets/kubernetes.io/serviceaccount from default-token-cb6kz (ro)
Conditions:
  Type              Status
  Initialized       True
  Ready             True
  ContainersReady   True
  PodScheduled      True
Volumes:
  default-token-cb6kz:
    Type:        Secret (a volume populated by a Secret)
    SecretName:  default-token-cb6kz
    Optional:    false
QoS Class:       Burstable
Node-Selectors:  <none>
Tolerations:     node.kubernetes.io/not-ready:NoExecute for 300s
                 node.kubernetes.io/unreachable:NoExecute for 300s
Events:
  Type     Reason      Age                   From                                                 Message
  ----     ------      ----                  ----                                                 -------
  Normal   Scheduled   6m33s                 default-scheduler                                    Successfully assigned default/netperf-client-42010ab20035 to gke-cluster-111-default-pool-ea461e6a-jwvv
  Warning  BackOff     2m8s                  kubelet, gke-cluster-111-default-pool-ea461e6a-jwvv  Back-off restarting failed container
  Normal   Pulled      113s (x3 over 6m32s)  kubelet, gke-cluster-111-default-pool-ea461e6a-jwvv  Container image "tailoredcloud/netperf:v2.7" already present on machine
  Normal   Created     113s (x3 over 6m32s)  kubelet, gke-cluster-111-default-pool-ea461e6a-jwvv  Created container netperf-client-42010ab20035
  Normal   Started     113s (x3 over 6m32s)  kubelet, gke-cluster-111-default-pool-ea461e6a-jwvv  Started container netperf-client-42010ab20035
  Warning  PacketDrop  113s (x2 over 4m19s)  kube-iptables-tailer                                 Packet dropped when sending traffic to server (10.60.1.14)


Name:           netperf-server-42010ab20035
Namespace:      default
Priority:       0
Node:           gke-cluster-111-default-pool-ea461e6a-c5h6/10.178.0.3
Start Time:     Sat, 11 Apr 2020 21:14:34 +0300
Labels:         app=netperf-operator
                netperf-type=server
Annotations:    cni.projectcalico.org/podIP: 10.60.1.14/32
                kubernetes.io/limit-ranger: LimitRanger plugin set: cpu request for container netperf-server-42010ab20035
Status:         Running
IP:             10.60.1.14
IPs:            <none>
Controlled By:  Netperf/example
Containers:
  netperf-server-42010ab20035:
    Container ID:   docker://a8bf0771e9a07b437d0fdcdfa59b40efb1f1d7d6eff292232ff18071ee0e41ad
    Image:          tailoredcloud/netperf:v2.7
    Image ID:       docker-pullable://tailoredcloud/netperf@sha256:0361f1254cfea87ff17fc1bd8eda95f939f99429856f766db3340c8cdfed1cf1
    Port:           <none>
    Host Port:      <none>
    State:          Running
      Started:      Sat, 11 Apr 2020 21:14:36 +0300
    Ready:          True
    Restart Count:  0
    Requests:
      cpu:        100m
    Environment:  <none>
    Mounts:
      /var/run/secrets/kubernetes.io/serviceaccount from default-token-cb6kz (ro)
Conditions:
  Type              Status
  Initialized       True
  Ready             True
  ContainersReady   True
  PodScheduled      True
Volumes:
  default-token-cb6kz:
    Type:        Secret (a volume populated by a Secret)
    SecretName:  default-token-cb6kz
    Optional:    false
QoS Class:       Burstable
Node-Selectors:  <none>
Tolerations:     node.kubernetes.io/not-ready:NoExecute for 300s
                 node.kubernetes.io/unreachable:NoExecute for 300s
Events:
  Type     Reason      Age                   From                                                 Message
  ----     ------      ----                  ----                                                 -------
  Normal   Scheduled   6m37s                 default-scheduler                                    Successfully assigned default/netperf-server-42010ab20035 to gke-cluster-111-default-pool-ea461e6a-c5h6
  Normal   Pulled      6m35s                 kubelet, gke-cluster-111-default-pool-ea461e6a-c5h6  Container image "tailoredcloud/netperf:v2.7" already present on machine
  Normal   Created     6m35s                 kubelet, gke-cluster-111-default-pool-ea461e6a-c5h6  Created container netperf-server-42010ab20035
  Normal   Started     6m35s                 kubelet, gke-cluster-111-default-pool-ea461e6a-c5h6  Started container netperf-server-42010ab20035
  Warning  PacketDrop  6m32s                 kube-iptables-tailer                                 Packet dropped when receiving traffic from 10.60.2.21
  Warning  PacketDrop  114s (x2 over 4m20s)  kube-iptables-tailer                                 Packet dropped when receiving traffic from client (10.60.2.21)
```

В манифесте сетевой политики проблема с селекторами. Правильные указать не успеваю, тороплюсь делать выпускную работу.
