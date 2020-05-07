[helm install]
wget https://get.helm.sh/helm-v3.1.2-linux-amd64.tar.gz
tar -zxvf helm-v3.1.2-linux-amd64.tar.gz 
mv linux-amd64/helm /usr/local/bin/helm

[harbor install]
helm repo add bitnami https://charts.bitnami.com/bitnami

export KUBECONFIG=/etc/rancher/k3s/k3s.yaml
helm install my-release bitnami/harbor

root@master01:~# helm install my-release bitnami/harbor
NAME: my-release
LAST DEPLOYED: Fri Apr 17 14:54:38 2020
NAMESPACE: default
STATUS: deployed
REVISION: 1
TEST SUITE: None
NOTES:
** Please be patient while the chart is being deployed **

1. Get the Harbor URL:

  echo "Harbor URL: https://127.0.0.1:8443/"
  kubectl port-forward --namespace default svc/harbor --address 0.0.0.0 8443:443

2. Login with the following credentials to see your Harbor application

  echo Username: "admin"
  echo Password: $(kubectl get secret --namespace default my-release-harbor-core-envvars -o jsonpath="{.data.HARBOR_ADMIN_PASSWORD}" | base64 --decode)
Password: 7BD4hO72AU

