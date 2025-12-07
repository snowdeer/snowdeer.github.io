---
layout: post
title: 샘플 Manifest 사용해보기
category: Kubernetes
permalink: /kubernetes/:year/:month/:day/:title/

tag: [Kubernetes, k8s]
---

# Sample Manifest

### snowdeer-app.yaml

<pre class="prettyprint">
apiVersion: v1
kind: Pod
metadata:
  name: snowdeer-app
  labels:
    app: snowdeer-app
spec:
  containers:
    - name: hello-server
      image: blux2/hello-server:1.0
      ports:
        - containerPort: 8080
</pre>

## 실행

`kubectl apply -f <파일이름>`으로 실행할 수 있음

<pre class="prettyprint">
$ kubectl apply -f snowdeer-app.yaml

pod/snowdeer-app created
</pre>

## 실행 확인

<pre class="prettyprint">
$ kubectl get pods    

NAME           READY   STATUS    RESTARTS   AGE
snowdeer-app   1/1     Running   0          81s
</pre>

또는

<pre class="prettyprint">
$ kubectl get pods -o wide                

NAME           READY   STATUS    RESTARTS   AGE   IP           NODE                             NOMINATED NODE   READINESS GATES
snowdeer-app   1/1     Running   0          23m   10.244.0.5   snowdeer-cluster-control-plane   none             none
</pre>

만약 `-o wide` 옵션 대신 `-o yaml` 옵션을 사용하면
다음과 같은 결과가 리턴

<pre class="prettyprint">
$ kubectl get pods snowdeer-app -o yaml    

apiVersion: v1
kind: Pod
metadata:
  annotations:
    kubectl.kubernetes.io/last-applied-configuration: |
      {"apiVersion":"v1","kind":"Pod","metadata":{"annotations":{},"labels":{"app":"snowdeer-app"},"name":"snowdeer-app","namespace":"default"},"spec":{"containers":[{"image":"blux2/hello-server:1.0","name":"hello-server","ports":[{"containerPort":8080}]}]}}
  creationTimestamp: "2025-12-07T07:06:14Z"
  generation: 1
  labels:
    app: snowdeer-app
  name: snowdeer-app
  namespace: default
  resourceVersion: "1578"
  uid: cd5a19de-fcf5-4daa-8373-c74295aea3c3
spec:
  containers:
  - image: blux2/hello-server:1.0
    imagePullPolicy: IfNotPresent
    name: hello-server
    ports:
    - containerPort: 8080
      protocol: TCP
    resources: {}
    terminationMessagePath: /dev/termination-log
    terminationMessagePolicy: File
    volumeMounts:
    - mountPath: /var/run/secrets/kubernetes.io/serviceaccount
      name: kube-api-access-bh7dz
      readOnly: true
  dnsPolicy: ClusterFirst
  enableServiceLinks: true
  nodeName: snowdeer-cluster-control-plane
  preemptionPolicy: PreemptLowerPriority
  priority: 0
  restartPolicy: Always
  schedulerName: default-scheduler
  securityContext: {}
  serviceAccount: default
  serviceAccountName: default
  terminationGracePeriodSeconds: 30
  tolerations:
  - effect: NoExecute
    key: node.kubernetes.io/not-ready
    operator: Exists
    tolerationSeconds: 300
  - effect: NoExecute
    key: node.kubernetes.io/unreachable
    operator: Exists
    tolerationSeconds: 300
  volumes:
  - name: kube-api-access-bh7dz
    projected:
      defaultMode: 420
      sources:
      - serviceAccountToken:
          expirationSeconds: 3607
          path: token
      - configMap:
          items:
          - key: ca.crt
            path: ca.crt
          name: kube-root-ca.crt
      - downwardAPI:
          items:
          - fieldRef:
              apiVersion: v1
              fieldPath: metadata.namespace
            path: namespace
status:
  conditions:
  - lastProbeTime: null
    lastTransitionTime: "2025-12-07T07:06:23Z"
    observedGeneration: 1
    status: "True"
    type: PodReadyToStartContainers
  - lastProbeTime: null
    lastTransitionTime: "2025-12-07T07:06:14Z"
    observedGeneration: 1
    status: "True"
    type: Initialized
  - lastProbeTime: null
    lastTransitionTime: "2025-12-07T07:06:23Z"
    observedGeneration: 1
    status: "True"
    type: Ready
  - lastProbeTime: null
    lastTransitionTime: "2025-12-07T07:06:23Z"
    observedGeneration: 1
    status: "True"
    type: ContainersReady
  - lastProbeTime: null
    lastTransitionTime: "2025-12-07T07:06:14Z"
    observedGeneration: 1
    status: "True"
    type: PodScheduled
  containerStatuses:
  - containerID: containerd://f540bc09b42828c9e0a6783fe2e35bddd5a03e1ce07a1f024319097f7b721d8c
    image: docker.io/blux2/hello-server:1.0
    imageID: docker.io/blux2/hello-server@sha256:35ab584cbe96a15ad1fb6212824b3220935d6ac9d25b3703ba259973fac5697d
    lastState: {}
    name: hello-server
    ready: true
    resources: {}
    restartCount: 0
    started: true
    state:
      running:
        startedAt: "2025-12-07T07:06:23Z"
    user:
      linux:
        gid: 0
        supplementalGroups:
        - 0
        uid: 0
    volumeMounts:
    - mountPath: /var/run/secrets/kubernetes.io/serviceaccount
      name: kube-api-access-bh7dz
      readOnly: true
      recursiveReadOnly: Disabled
  hostIP: 172.18.0.2
  hostIPs:
  - ip: 172.18.0.2
  observedGeneration: 1
  phase: Running
  podIP: 10.244.0.5
  podIPs:
  - ip: 10.244.0.5
  qosClass: BestEffort
  startTime: "2025-12-07T07:06:14Z"
</pre>

`kubectl get <리소스이름> -o yaml | less` 옵션을 이용해서 key가 되는 문자열을 검색하기도 함

하지만, 다른 이유보다 아래와 같은 명령어를 이용하면
현재 클러스터에 적용된 오브젝트의 내용과 Manifest의 차이를 확인할 수 있는 유용성이 있음

<pre class="prettyprint">
$ kubectl get pods snowdeer-app -o yaml > pod.yaml
</pre>

그 이후 아래 명령어로 차이를 확인

<pre class="prettyprint">
$ diff pod.yaml snowdeer-app.yaml

4,8c4
<   annotations:
<     kubectl.kubernetes.io/last-applied-configuration: |
<       {"apiVersion":"v1","kind":"Pod","metadata":{"annotations":{},"labels":{"app":"snowdeer-app"},"name":"snowdeer-app","namespace":"default"},"spec":{"containers":[{"image":"blux2/hello-server:1.0","name":"hello-server","ports":[{"containerPort":8080}]}]}}
<   creationTimestamp: "2025-12-07T07:06:14Z"
<   generation: 1
---
>   name: snowdeer-app
11,14d6
<   name: snowdeer-app
<   namespace: default
<   resourceVersion: "1578"
<   uid: cd5a19de-fcf5-4daa-8373-c74295aea3c3
17,129c9,12
<   - image: blux2/hello-server:1.0
<     imagePullPolicy: IfNotPresent
<     name: hello-server
<     ports:
<     - containerPort: 8080
<       protocol: TCP
<     resources: {}
<     terminationMessagePath: /dev/termination-log
<     terminationMessagePolicy: File
<     volumeMounts:
<     - mountPath: /var/run/secrets/kubernetes.io/serviceaccount
<       name: kube-api-access-bh7dz
<       readOnly: true
<   dnsPolicy: ClusterFirst
<   enableServiceLinks: true
<   nodeName: snowdeer-cluster-control-plane
<   preemptionPolicy: PreemptLowerPriority
<   priority: 0
<   restartPolicy: Always
<   schedulerName: default-scheduler
<   securityContext: {}
<   serviceAccount: default
<   serviceAccountName: default
<   terminationGracePeriodSeconds: 30
<   tolerations:
<   - effect: NoExecute
<     key: node.kubernetes.io/not-ready
<     operator: Exists
<     tolerationSeconds: 300
<   - effect: NoExecute
<     key: node.kubernetes.io/unreachable
<     operator: Exists
<     tolerationSeconds: 300
<   volumes:
<   - name: kube-api-access-bh7dz
<     projected:
<       defaultMode: 420
<       sources:
<       - serviceAccountToken:
<           expirationSeconds: 3607
<           path: token
<       - configMap:
<           items:
<           - key: ca.crt
<             path: ca.crt
<           name: kube-root-ca.crt
<       - downwardAPI:
<           items:
<           - fieldRef:
<               apiVersion: v1
<               fieldPath: metadata.namespace
<             path: namespace
< status:
<   conditions:
<   - lastProbeTime: null
<     lastTransitionTime: "2025-12-07T07:06:23Z"
<     observedGeneration: 1
<     status: "True"
<     type: PodReadyToStartContainers
<   - lastProbeTime: null
<     lastTransitionTime: "2025-12-07T07:06:14Z"
<     observedGeneration: 1
<     status: "True"
<     type: Initialized
<   - lastProbeTime: null
<     lastTransitionTime: "2025-12-07T07:06:23Z"
<     observedGeneration: 1
<     status: "True"
<     type: Ready
<   - lastProbeTime: null
<     lastTransitionTime: "2025-12-07T07:06:23Z"
<     observedGeneration: 1
<     status: "True"
<     type: ContainersReady
<   - lastProbeTime: null
<     lastTransitionTime: "2025-12-07T07:06:14Z"
<     observedGeneration: 1
<     status: "True"
<     type: PodScheduled
<   containerStatuses:
<   - containerID: containerd://f540bc09b42828c9e0a6783fe2e35bddd5a03e1ce07a1f024319097f7b721d8c
<     image: docker.io/blux2/hello-server:1.0
<     imageID: docker.io/blux2/hello-server@sha256:35ab584cbe96a15ad1fb6212824b3220935d6ac9d25b3703ba259973fac5697d
<     lastState: {}
<     name: hello-server
<     ready: true
<     resources: {}
<     restartCount: 0
<     started: true
<     state:
<       running:
<         startedAt: "2025-12-07T07:06:23Z"
<     user:
<       linux:
<         gid: 0
<         supplementalGroups:
<         - 0
<         uid: 0
<     volumeMounts:
<     - mountPath: /var/run/secrets/kubernetes.io/serviceaccount
<       name: kube-api-access-bh7dz
<       readOnly: true
<       recursiveReadOnly: Disabled
<   hostIP: 172.18.0.2
<   hostIPs:
<   - ip: 172.18.0.2
<   observedGeneration: 1
<   phase: Running
<   podIP: 10.244.0.5
<   podIPs:
<   - ip: 10.244.0.5
<   qosClass: BestEffort
<   startTime: "2025-12-07T07:06:14Z"
---
>     - name: hello-server
>       image: blux2/hello-server:1.0
>       ports:
>         - containerPort: 8080
</pre>

실행해보면 차이가 많음.
Manifest에서는 필수적인 내용만 기재되지만, 실제로 k8s에서 구동되면 더 많은 정보가 필요함
