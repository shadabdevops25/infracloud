
I have completed my assignment

# three node (two workers) cluster config

kind: Cluster
apiVersion: kind.x-k8s.io/v1alpha4
nodes:
- role: control-plane
  image: kindest/node:v1.20.7@sha256:cbeaf907fc78ac97ce7b625e4bf0de16e3ea725daf6b04f930bd14c67c671ff9
- role: worker
  image: kindest/node:v1.20.7@sha256:cbeaf907fc78ac97ce7b625e4bf0de16e3ea725daf6b04f930bd14c67c671ff9
  
  
As per the config file, it will create two nodes: one control plane and one worker. Since it is just a comment, I have not made any changes to this part of the config file, as mentioned in the instructions that no modification is required.



#2 In the redis-cart.yaml manifest file, the node selector was incorrect

In the redis-cart.yaml manifest file, the node selector was incorrect, causing the container status to remain in a pending state. I performed a RCA and identified that the node selector was wrong. I corrected the name, redeployed the file, and the container transitioned to the running state

Before 
 spec:
      nodeSelector:
        kubernetes.io/hostname: test-worker-2
		
After:
    spec:
      nodeSelector:
        kubernetes.io/hostname: qa-cluster-worker



#3 In adservice.yaml manifest file, Image is wrong.

In the adservice.yaml manifest file, the image was incorrect. During deployment, I encountered the error ImagePullBackOff. After checking the pod details, we identified the issue.


Approach#1

Name:             adservice-654bfd6fb6-jl4gt
Namespace:        default
Priority:         0
Service Account:  default
Node:             qa-cluster-worker/172.18.0.3
Start Time:       Sat, 30 Nov 2024 13:24:01 +0000
Labels:           app=adservice
                  pod-template-hash=654bfd6fb6
Annotations:      <none>
Status:           Pending
IP:               10.244.1.11
IPs:
  IP:           10.244.1.11
Controlled By:  ReplicaSet/adservice-654bfd6fb6
Containers:
  server:
    Container ID:
    Image:          gcr.io/google-samples/microservices-demo/adservice:v0.3.1
    Image ID:
    Port:           9555/TCP
    Host Port:      0/TCP
    State:          Waiting
      Reason:       ImagePullBackOff
    Ready:          False
    Restart Count:  0
    Limits:
      cpu:     300m
      memory:  300Mi
    Requests:
      cpu:      200m
      memory:   180Mi
    Liveness:   exec [/bin/grpc_health_probe -addr=:9555] delay=20s timeout=1s period=15s #success=1 #failure=3
    Readiness:  exec [/bin/grpc_health_probe -addr=:9555] delay=20s timeout=1s period=15s #success=1 #failure=3
    Environment:
      PORT:  9555
    Mounts:
      /var/run/secrets/kubernetes.io/serviceaccount from default-token-jq2bz (ro)
Conditions:
  Type              Status
  Initialized       True
  Ready             False
  ContainersReady   False
  PodScheduled      True
Volumes:
  default-token-jq2bz:
    Type:        Secret (a volume populated by a Secret)
    SecretName:  default-token-jq2bz
    Optional:    false
QoS Class:       Burstable
Node-Selectors:  <none>
Tolerations:     node.kubernetes.io/not-ready:NoExecute op=Exists for 300s
                 node.kubernetes.io/unreachable:NoExecute op=Exists for 300s
Events:
  Type     Reason     Age                 From               Message
  ----     ------     ----                ----               -------
  Normal   Scheduled  3m3s                default-scheduler  Successfully assigned default/adservice-654bfd6fb6-jl4gt to qa-cluster-worker
  Normal   Pulling    95s (x4 over 3m3s)  kubelet            Pulling image "gcr.io/google-samples/microservices-demo/adservice:v0.3.1"
  Warning  Failed     95s (x4 over 3m3s)  kubelet            Failed to pull image "gcr.io/google-samples/microservices-demo/adservice:v0.3.1": rpc error: code = NotFound desc = failed to pull and unpack image "gcr.io/google-samples/microservices-demo/adservice:v0.3.1": failed to resolve reference "gcr.io/google-samples/microservices-demo/adservice:v0.3.1": gcr.io/google-samples/microservices-demo/adservice:v0.3.1: not found
  Warning  Failed     95s (x4 over 3m3s)  kubelet            Error: ErrImagePull
  Normal   BackOff    83s (x6 over 3m2s)  kubelet            Back-off pulling image "gcr.io/google-samples/microservices-demo/adservice:v0.3.1"
  Warning  Failed     68s (x7 over 3m2s)  kubelet            Error: ImagePullBackOff

Approach#2  
  
I checked the manifest file and updated it with the help of ChatGPT, but I am still getting the same error.


Approach#3

I tried to pull the image, but I'm getting this error

docker pull gcr.io/google-samples/microservices-demo/adservice:v0.3.1




Failed to pull image "gcr.io/google-samples/microservices-demo/adservice:v0.3.1": rpc error: code = NotFound desc = failed to pull and unpack image "gcr.io/google-samples/microservices-demo/adservice:v0.3.1": failed to resolve reference "gcr.io/google-samples/microservices-demo/adservice:v0.3.1": gcr.io/google-samples/microservices-demo/adservice:v0.3.1: not found



This error suggests that the image gcr.io/google-samples/microservices-demo/adservice:v0.3.1 cannot be found in the Google Container Registry (GCR). The possible causes for this could include:

Image not found: The image version v0.3.1 might not exist in the GCR repository, or the repository URL could be incorrect.
Private registry: If the image exists in a private GCR repository, Kubernetes may not have the required credentials to access it.
Network issue: There may be a network issue preventing Kubernetes from reaching the GCR repository.


Approch#4

I tried to check the image in GCR, but I couldn't find it there




Note: As per the instructions, all containers must be running before moving to part C. However, one of mycontainers is not in a running state, so I have paused this assignment here.



