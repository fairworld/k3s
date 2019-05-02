# CentOS 7
- k3s 설치시 selinux 관련해서 사전에 설치가 필요함
```bash
yum install policycoreutils-python -y
```

- Install docker 1.13.1
```bash
yum install docker -y

systemctl status docker
systemctl enable docker
systemctl start docker
```

# Install K3S Server
```bash
curl -sfL https://get.k3s.io | sh -
systemctl status k3s
kubectl get nodes
```
- 만약 Proxy 하위에서 실행하고자 한다면... 아래와 같이 Proxy 설정이 추가되어야 함
```bash
[Unit]
Description=Lightweight Kubernetes
Documentation=https://k3s.io
After=network-online.target

[Service]
Type=notify
Environment="HTTP_PROXY=http://[IP]:[Port]/"
Environment="HTTPS_PROXY=http://[IP]:[Port]/"
EnvironmentFile=/etc/systemd/system/k3s.service.env
ExecStartPre=-/sbin/modprobe br_netfilter
ExecStartPre=-/sbin/modprobe overlay
ExecStart=/usr/local/bin/k3s server
KillMode=process
Delegate=yes
LimitNOFILE=infinity
LimitNPROC=infinity
LimitCORE=infinity
TasksMax=infinity
TimeoutStartSec=0

[Install]
WantedBy=multi-user.target
```

# K3S Token 확인
```bash
cat /var/lib/rancher/k3s/server/node-token
```
# Install K3S Agent
- K3S Master로부터 k3s를 복사
```bash
scp /usr/local/bin/k3s root@k3s02:/usr/local/bin/k3s
scp /usr/local/bin/k3s root@k3s03:/usr/local/bin/k3s
```

- systemd로 구동하기 위하여 아래 파일을 작성
- K3S_TOKEN은 k3s master에서 확인된 값으로 대체한다.
- vi /etc/systemd/system/k3s.service.env
```bash
K3S_URL="https://k3s01:6443"
K3S_TOKEN="K105c9d105384dba3ea438b5e5a106906265fe4c18e0c7ccf1692f446c75bd73a0c::node:7a11c59597d4fa08d5e40bb4f88ba716"
```

- vi /etc/systemd/system/k3s.service
```bash
[Unit]
Description=Lightweight Kubernetes
Documentation=https://k3s.io
After=network-online.target

[Service]
Type=notify
EnvironmentFile=/etc/systemd/system/k3s.service.env
ExecStartPre=-/sbin/modprobe br_netfilter
ExecStartPre=-/sbin/modprobe overlay
ExecStart=/usr/local/bin/k3s agent --server ${K3S_URL} --token ${K3S_TOKEN}
KillMode=process
Delegate=yes
LimitNOFILE=infinity
LimitNPROC=infinity
LimitCORE=infinity
TasksMax=infinity
TimeoutStartSec=0

[Install]
WantedBy=multi-user.target
```

# Master에서 k3s node 상태 확인
```bash
kubectl get nodes
```
# Uninstall
```bash
/usr/local/bin/k3s-uninstall.sh
```

# Docker pull behing proxy
https://stackoverflow.com/questions/23111631/cannot-download-docker-images-behind-a-proxy

# 주요 명령어
```bash
kubectl describe pod coredns-857cdbd8b4-d9hhv --namespace kube-system

kubectl get pods --all-namespaces

kubectl get all --all-namespaces
```
