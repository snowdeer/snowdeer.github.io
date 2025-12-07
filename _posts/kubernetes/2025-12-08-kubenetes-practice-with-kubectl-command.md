---
layout: post
title: kubectl 명령어로 디버깅 해보기
category: Kubernetes
permalink: /kubernetes/:year/:month/:day/:title/

tag: [Kubernetes, k8s]
---

# 사이드카 컨테이너

k8s 1.25부터 Stable된 명령어로 컨테이너에 경량화나 보안을 위해 Shell 조차 없을 경우 사용가능한 방법

`kubectl debug --stdin --tty [디버그대상Pod명] --image=[디버그용컨테이너이미지] --target=[디버그대상컨테이너명]`

현재 아래와 같이 컨테이너가 동작 중

<pre class="prettyprint">
$ kubectl get pods                                 

NAME           READY   STATUS    RESTARTS   AGE
snowdeer-app   1/1     Running   0          5h11m
</pre>

## 실행

<pre class="prettyprint">
$ kubectl debug --stdin --tty snowdeer-app --image=curlimages/curl:latest --target=hello-server -- sh

Targeting container "hello-server". If you don't see processes from this container it may be because the container runtime doesn't support this feature.
Defaulting debug container name to debugger-55kh7.
If you don't see a command prompt, try pressing enter.
~ $ 
</pre>

여기에 `curl localhost:8080` 입력 하면 아래와 같이 결과가 나옴

<pre class="prettyprint">
$ kubectl debug --stdin --tty snowdeer-app --image=curlimages/curl:latest --target=hello-server -- sh

Targeting container "hello-server". If you don't see processes from this container it may be because the container runtime doesn't support this feature.
Defaulting debug container name to debugger-55kh7.
If you don't see a command prompt, try pressing enter.
~ $ curl localhost:8080
Hello, world!~ $ 
~ $ 
</pre>

`--stdin`과 `--tty` 대신 `-it`를 쓰면 좀 더 간편해짐

<pre class="prettyprint">
$ kubectl debug -it snowdeer-app --image=curlimages/curl:latest --target=hello-server -- sh
</pre>

# kubectl exec

`kubectl exec` 명령어로 컨테이너에 직접 접속 가능

예시로 `curl` Pod를 하나 생성

<pre class="prettyprint">
$ kubectl run snowdeer-curl --image=curlimages/curl:latest --command -- /bin/sh -c "while true; do sleep infinity; done;"

pod/snowdeer-curl created

$ kubectl get pods -o wide     

NAME            READY   STATUS    RESTARTS   AGE     IP           NODE                             NOMINATED NODE   READINESS GATES
snowdeer-app    1/1     Running   0          5h20m   10.244.0.5   snowdeer-cluster-control-plane   <none>           <none>
snowdeer-curl   1/1     Running   0          2m22s   10.244.0.7   snowdeer-cluster-control-plane   <none>           <none>
</pre>

## 접속

<pre class="prettyprint">
$ kubectl exec -it snowdeer-curl -- /bin/sh                         

# 위에서 확인했던 snowdeer-app의 주소로 명령어를 날려봄
~ $ curl 10.244.0.5:8080
Hello, world!~ $ 
</pre>

# 포트포워딩을 통한 접속

k8s의 Pod에는 쿠버네티스 내부의 IP 주소가 할당됨. 즉, 외부에서는 기본적으로 접속 불가능.
`Service`를 이용해서 접속하는 것이 일반적이나 `port-forward` 명령어를 이용해서 접근 가능
아래 명령어로 로컬PC의 `3000`번 포트를 `snowdeer-app`의 `8080`에 연결할 수 있음

<pre class="prettyprint">
$ kubectl port-forward snowdeer-app 3000:8080  

Forwarding from 127.0.0.1:3000 -> 8080
Forwarding from [::1]:3000 -> 8080
</pre>

그 이후 로컬 PC의 터미널에서 아래 명령어 호출하면 접속 가능함

<pre class="prettyprint">
$ curl localhost:3000      

Hello, world!                                                 
</pre>
