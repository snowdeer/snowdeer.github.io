---
layout: post
title: ReplicaSet 및 Deployment
category: Kubernetes
permalink: /kubernetes/:year/:month/:day/:title/

tag: [Kubernetes, k8s]
---

# ReplicaSet

지정한 개수만큼 Pod를 복제하는 리소스

### snowdeer-replicaset.yaml

<pre class="prettyprint">
apiVersion: apps/v1
kind: ReplicaSet
metadata:
  name: snowdeer-http-server
  labels:
    app: snowdeer-http-server
spec:
  replicas: 3
  selector:
    matchLabels:
      app: snowdeer-http-server
  template:
    metadata:
      labels:
        app: snowdeer-http-server
    spec:
      containers:
        - name: nginx
          image: nginx:latest
</pre>

<pre class="prettyprint">
$ kubectl get pods                         

NAME                         READY   STATUS    RESTARTS   AGE
snowdeer-http-server-45gsp   1/1     Running   0          14m
snowdeer-http-server-5tk4z   1/1     Running   0          14m
snowdeer-http-server-st74m   1/1     Running   0          14m
</pre>

<pre class="prettyprint">
$ kubectl get replicaset snowdeer-http-server

NAME                   DESIRED   CURRENT   READY   AGE
snowdeer-http-server   3         3         3       14m
</pre>

# Deployment

하지만, 본격적 운영 환경에서는 `ReplicaSet`을 추천하지는 않고 `Deployment`를 권장함.
`Deployment`를 사용하는 가장 큰 이유는 `무중단 업데이트` 기능.

### snowdeer-deployment.yaml

<pre class="prettyprint">
apiVersion: apps/v1
kind: Deployment
metadata:
  name: snowdeer-nginx-deployment
  labels:
    app: snowdeer-nginx
spec:
  replicas: 3
  selector:
    matchLabels:
      app: snowdeer-nginx
  template:
    metadata:
      labels:
        app: snowdeer-nginx
    spec:
      containers:
        - name: nginx
          image: nginx:1.25.3
          ports:
            - containerPort: 80
</pre>

## 실행

<pre class="prettyprint">
$ kubectl apply -f snowdeer-deployment.yaml 

deployment.apps/snowdeer-nginx-deployment created

$ kubectl get deployment

NAME                        READY   UP-TO-DATE   AVAILABLE   AGE
snowdeer-nginx-deployment   3/3     3            3           48s

$ kubectl get pods      

NAME                                         READY   STATUS    RESTARTS   AGE
snowdeer-nginx-deployment-847759b688-2zbgh   1/1     Running   0          47s
snowdeer-nginx-deployment-847759b688-j7pql   1/1     Running   0          44s
snowdeer-nginx-deployment-847759b688-nl6cw   1/1     Running   0          41s

$ kubectl get replicaset                     

NAME                                   DESIRED   CURRENT   READY   AGE
snowdeer-nginx-deployment-847759b688   3         3         3       76s
</pre>

## 무중단 업데이트 실습

아까 작성했던 `snowdeer-deployment.yaml` 파일을 수정해서 nginx의 버전을 수정함

### snowdeer-deployment.yaml(수정)

컨테이너 이미지를 수정함

<pre class="prettyprint">
apiVersion: apps/v1
kind: Deployment
metadata:
  name: snowdeer-nginx-deployment
  labels:
    app: snowdeer-nginx
spec:
  replicas: 3
  selector:
    matchLabels:
      app: snowdeer-nginx
  template:
    metadata:
      labels:
        app: snowdeer-nginx
    spec:
      containers:
        - name: nginx
          image: nginx:latest
          ports:
            - containerPort: 80
</pre>

## 실행

<pre class="prettyprint">
$ kubectl apply -f snowdeer-deployment.yaml 

deployment.apps/snowdeer-nginx-deployment configured

$ kubectl get pods      

NAME                                         READY   STATUS    RESTARTS   AGE
snowdeer-nginx-deployment-847759b688-gzztg   1/1     Running   0          15s
snowdeer-nginx-deployment-847759b688-xc75g   1/1     Running   0          18s
snowdeer-nginx-deployment-847759b688-zszxt   1/1     Running   0          20s

$ kubectl get replicaset                     

NAME                                   DESIRED   CURRENT   READY   AGE
snowdeer-nginx-deployment-66fb57596b   0         0         0       2m18s
snowdeer-nginx-deployment-847759b688   3         3         3       4m59s
</pre>

## Deployment 업데이트 방식

Deployment의 업데이트 방식은 기본적으로 `RollingUpdate`로 되어있음.
아래 명령어로 확인 가능

<pre class="prettyprint">
$ kubectl describe deployment snowdeer-nginx-deployment

Name:                   snowdeer-nginx-deployment
Namespace:              default
CreationTimestamp:      Sun, 07 Dec 2025 22:11:52 +0900
Labels:                 app=snowdeer-nginx
Annotations:            deployment.kubernetes.io/revision: 4
Selector:               app=snowdeer-nginx
Replicas:               3 desired | 3 updated | 3 total | 3 available | 0 unavailable
StrategyType:           RollingUpdate
MinReadySeconds:        0
RollingUpdateStrategy:  25% max unavailable, 25% max surge
// ...
</pre>

`RollingUpdateStrategy:  25% max unavailable, 25% max surge` 항목은 전체 25%의 Pod까지 동시 종료할 수 있음을 의미함.
`max surge`는 최대 몇 개의 Pod를 새로 생성할 수 있는것인지를 의미하며, 오래된 Pod와 신규 Pod로 인해 필요 클러스터의 용량과
비용 증가 가능성이 있기 때문에 주의해서 설정할 필요 있음.

하지만 경우(빠르게 전체 적용 등)에 따라서는 `Recreate` 타입이 더 유리한 경우도 있음.

그런 경우에는 아래와 같이 선언 가능

### snowdeer-deployment.yaml(수정)

<pre class="prettyprint">
apiVersion: apps/v1
kind: Deployment
metadata:
  name: snowdeer-nginx-deployment
  labels:
    app: snowdeer-nginx
spec:
  replicas: 3
  strategy:
    type: Recreate
  selector:
    matchLabels:
      app: snowdeer-nginx
  template:
    metadata:
      labels:
        app: snowdeer-nginx
    spec:
      containers:
        - name: nginx
          image: nginx:latest
          ports:
            - containerPort: 80
</pre>
