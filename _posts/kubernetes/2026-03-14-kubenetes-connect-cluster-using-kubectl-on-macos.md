---
layout: post
title: MacOS에서 Kubespray 클러스터에 kubectl로 접속하기
category: Kubernetes
permalink: /kubernetes/:year/:month/:day/:title/

tag: [Kubernetes, k8s]
---

# kubectl 설치

<pre class="prettyprint">

# kubectl cli 설치
$ brew install kubernetes-cli
</pre>

# VM에서 설정 파일(kubeconfig) 가져오기

쿠버네티스 클러스터에 접속하기 위한 인증 정보인 admin.conf 파일을 VM에서 맥북으로 가져와야 함

<pre class="prettyprint">
# 맥북의 ~/.kube 디렉토리 생성
mkdir -p ~/.kube

# Multipass VM 내부의 설정 파일을 맥북으로 복사
# VM 내부로 접속
multipass shell k8s-node

# 파일을 ubuntu 홈 디렉토리로 복사하고 소유권 변경
sudo cp /etc/kubernetes/admin.conf ~/admin.conf
sudo chown ubuntu:ubuntu ~/admin.conf

# VM 접속 종료
exit

# 맥 터미널에서 아래 명령어 실행(아마 오류)
multipass transfer k8s-node:~/admin.conf ~/.kube/config.tmp

# 맥에서 위 ~ 을 해석하지 못하기 때문에 아래 코드 실행
multipass transfer k8s-node:/home/ubuntu/admin.conf ~/.kube/config.tmp
</pre>

## IP 주소 업데이트 및 설정 적용

<pre class="prettyprint">
# VM의 IP를 자동으로 가져와서 127.0.0.1 부분을 수정합니다.
VM_IP=$(multipass info k8s-node | grep IPv4 | awk '{print $2}')
sed "s/127.0.0.1/$VM_IP/g" ~/.kube/config.tmp > ~/.kube/config

# 권한 설정
chmod 600 ~/.kube/config
rm ~/.kube/config.tmp
</pre>

## 클러스터 확인

<pre class="prettyprint">
kubectl get nodes
NAME    STATUS   ROLES           AGE   VERSION
node1   Ready    control-plane   28m   v1.35.1
</pre>
