---
layout: post
title: MacOS에서 Kubespray로 클러스터구축하기
category: Kubernetes
permalink: /kubernetes/:year/:month/:day/:title/

tag: [Kubernetes, k8s]
---

# 사전 준비

- Multipass: macOS에서 우분투 VM을 가장 쉽게 구동하게 해주는 도구
- Ansible: Kubespray 실행을 위해 로컬(호스트)에 설치가 필요

<pre class="prettyprint">
# Multipass 설치
$ brew install --cask multipass

# kubectl cli 설치
$ brew install kubernetes-cli
</pre>

# 가상 머신(VM) 생성

싱글 노드 클러스터를 위해 최소 2개 이상의 CPU와 4GB 이상의 RAM을 가진 VM을 생성 필요

<pre class="prettyprint">
# 'k8s-node'라는 이름의 VM 생성 (Ubuntu 22.04/24.04 추천)
$ multipass launch --name k8s-node --cpus 2 --memory 4G --disk 20G

# 생성된 VM의 IP 확인
$ multipass info k8s-node

Name:           k8s-node
State:          Running
Snapshots:      0
IPv4:           192.168.64.2
Release:        Ubuntu 24.04.4 LTS
Image hash:     99e1d482b958 (Ubuntu 24.04 LTS)
CPU(s):         2
Load:           0.24 0.08 0.02
Disk usage:     2.1GiB out of 19.3GiB
Memory usage:   293.3MiB out of 3.8GiB
Mounts:         --

# 아래 명령어로도 가능
$ multipass list

Name                    State             IPv4             Image
k8s-node                Running           192.168.64.2     Ubuntu 24.04 LTS
</pre>

위에서 `192.168.64.2`를 기억해야 함

# 접속 테스트

<pre class="prettyprint">
$ multipass exec k8s-node -- bash
</pre>

만약 `exec failed: ssh connection failed: 'Failed to connect: No route to host'`와 같은 오류가 발생하면, 아래 명령어 실행

<pre class="prettyprint">
ssh-keygen -R 192.168.64.2
ssh-keygen -R k8s-node
</pre>

# SSH 접속 설정

Kubespray(Ansible)가 VM에 접속할 수 있도록 SSH 키를 복사

<pre class="prettyprint">
# VM으로 공개키 복사 (사용자명은 기본적으로 'ubuntu')
$ multipass exec k8s-node -- bash -c "mkdir -p ~/.ssh && echo '$(cat ~/.ssh/id_ed25519.pub)' >> ~/.ssh/authorized_keys"

# 비밀번호 없이 sudo 가능하도록 설정 (선택 사항이나 편리함)
$ multipass exec k8s-node -- bash -c "echo 'ubuntu ALL=(ALL) NOPASSWD:ALL' | sudo tee /etc/sudoers.d/ubuntu"
</pre>

# Kubespray 설정 및 배포

<pre class="prettyprint">
# Kubespray 소스 다운로드
git clone https://github.com/kubernetes-sigs/kubespray.git
cd kubespray

# 가상환경 설정
python -m venv venv
source venv/bin/activate

# 의존성 설치 (가상환경 권장)
# Python는 3.12 버전 이상
pip install --upgrade ansible
pip3 install -r requirements.txt

# 설정 복사
cp -rfp inventory/sample inventory/snowdeer-cluster
</pre>

### 인벤토리 수정 (inventory/snowdeer-cluster/inventory.ini)

싱글 노드이므로 모든 역할을 하나의 IP에 할당.  
[all] 섹션에 VM IP를 적고, 나머지 그룹에도 동일한 호스트명을 입력

<pre class="prettyprint">
[all]
node1 ansible_host=192.168.64.2 ip=192.168.64.2 ansible_user=ubuntu ansible_ssh_private_key_file=~/.ssh/id_ed25519

[kube_control_plane]
node1

[etcd]
node1

[kube_node]
node1

[k8s_cluster:children]
kube_control_plane
kube_node
</pre>

### 클러스터 설정 변경

싱글 노드에서는 리소스 최적화를 위해 일부 설정을 변경하는 것이 좋음.  
`inventory/snowdeer-cluster/group_vars/k8s_cluster/k8s-cluster.yml`에서 다음 설정 확인

<pre class="prettyprint">
kube_network_plugin: calico
</pre>

## 플레이북 실행 (설치 시작)

10~20분 이상 시간이 걸리기도 함

<pre class="prettyprint">
ansible-playbook -i inventory/snowdeer-cluster/inventory.ini --become --become-user=root cluster.yml
</pre>

## 클러스터 확인

설치가 완료되면 VM에 접속하여 상태를 확인

<pre class="prettyprint">
multipass shell k8s-node

# kubectl 설치
sudo snap install kubectl --classic

# 쿠베컨피그 설정 (root 권한 필요)
sudo cp /etc/kubernetes/admin.conf ~/.kube/config
sudo chown $(id -u):$(id -g) ~/.kube/config

# 노드 상태 확인
kubectl get nodes
</pre>
