---
layout: post
title: Kind 이용 클러스터 생성
category: Kubernetes
permalink: /kubernetes/:year/:month/:day/:title/

tag: [Kubernetes, k8s]
---

# k8s 클러스터 생성

아래와 같이 명령을 내리면 `kind-`가 prefix로 붙어서
`kind-snowdeer-cluster` 이름의 클러스터가 생성됨

<pre class="prettyprint">
$ kind create cluster --name snowdeer-cluster

Creating cluster "snowdeer-cluster" ...
 ✓ Ensuring node image (kindest/node:v1.34.0) 🖼 
 ✓ Preparing nodes 📦  
 ✓ Writing configuration 📜 
 ✓ Starting control-plane 🕹️ 
 ✓ Installing CNI 🔌 
 ✓ Installing StorageClass 💾 
Set kubectl context to "kind-snowdeer-cluster"
You can now use your cluster with:

kubectl cluster-info --context kind-snowdeer-cluster

Have a nice day! 👋
</pre>

## k8s 클러스터 정보 확인

<pre class="prettyprint">
$ kubectl cluster-info --context kind-snowdeer-cluster

Kubernetes control plane is running at https://127.0.0.1:57730
CoreDNS is running at https://127.0.0.1:57730/api/v1/namespaces/kube-system/services/kube-dns:dns/proxy

To further debug and diagnose cluster problems, use 'kubectl cluster-info dump'.
</pre>

위에서 `--context` 옵션을 붙여서 클러스터의 context를 지정해야 하지만,
만약 하나의 클러스터만 존재하면 해당 옵션은 생략가능함.
여러 클러스터일 경우 `--context` 옵션 없이 기본 클러스터를 지정하기 위해서
아래 명령어 사용 가능

<pre class="prettyprint">
$ kubectl config use-context kind-snowdeer-cluster

Switched to context "kind-snowdeer-cluster".
</pre>

## 클러스터 리스트 확인

kind를 이용해서 클러스터를 생성했기 때문에 명령어를 `kind`를 이용해서 조회할 수 있음

<pre class="prettyprint">
$ kind get clusters   

snowdeer-cluster
</pre>

## Node 정보 확인

<pre class="prettyprint">
$ kubectl get nodes                               

NAME                             STATUS   ROLES           AGE     VERSION
snowdeer-cluster-control-plane   Ready    control-plane   6m41s   v1.34.0
</pre>
