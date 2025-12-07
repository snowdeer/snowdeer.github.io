---
layout: post
title: MAC OS에 kind 설치하는 방법
category: Kubernetes
permalink: /kubernetes/:year/:month/:day/:title/

tag: [Kubernetes, k8s]
---

# kind

https://kind.sigs.k8s.io
멀티 노드 클러스터 생성가능한 툴.
Docker 내부에 Docker을 실행하기 때문에 `Docker in Docker` 라고도 함

# MAC OS에 kind 설치

`brew`를 이용해서 설치 가능

<pre class="prettyprint">
$ brew install kind
</pre>

## 설치 확인

<pre class="prettyprint">
$ kind version

kind v0.30.0 go1.25.4 darwin/arm64
</pre>

## k8s 클러스터 생성

Docker가 먼저 실행 중이어야 함

<pre class="prettyprint">
$ kind create cluster

Creating cluster "kind" ...
 ✓ Ensuring node image (kindest/node:v1.34.0) 🖼 
 ✓ Preparing nodes 📦  
 ✓ Writing configuration 📜 
 ✓ Starting control-plane 🕹️ 
 ✓ Installing CNI 🔌 
 ✓ Installing StorageClass 💾 
Set kubectl context to "kind-kind"
You can now use your cluster with:

kubectl cluster-info --context kind-kind

Have a nice day! 👋
</pre>

## k8s 클러스터 정보 확인

<pre class="prettyprint">
$ kubectl cluster-info --context kind-kind

Kubernetes control plane is running at https://127.0.0.1:54204
CoreDNS is running at https://127.0.0.1:54204/api/v1/namespaces/kube-system/services/kube-dns:dns/proxy

To further debug and diagnose cluster problems, use 'kubectl cluster-info dump'.
</pre>

## config

k8s 환경의 설정은 `.kube/config` 파일 안에 기록되어 있음

<pre class="prettyprint">
$ cat .kube/config      
apiVersion: v1
clusters:
- context:
    cluster: kind-kind
    user: kind-kind
  name: kind-kind
current-context: kind-kind
kind: Config
preferences: {}
users:
- name: kind-kind
  user:
    client-certificate-data: 어쩌고저쩌고
    client-key-data: 어쩌고저쩌고
</pre>

## 클러스터 삭제

<pre class="prettyprint">
$ kind delete cluster

Deleting cluster "kind" ...
Deleted nodes: ["kind-control-plane"]
</pre>
