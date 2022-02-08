<!-- To connect to Kong, please execute the following commands:
HOST=$(kubectl get svc --namespace kong my-kong-kong-proxy -o jsonpath='{.status.loadBalancer.ingress[0].ip}')
PORT=$(kubectl get svc --namespace kong my-kong-kong-proxy -o jsonpath='{.spec.ports[0].port}')
export PROXY_IP=${HOST}:${PORT}
curl $PROXY_IP  -->
#### Create k3d cluster without traefik as default ingress class
k3d cluster create k3s-local --k3s-arg '--no-deploy=traefik@server:*' --servers 3

#### Pre-requisites
kubectl create namespace kong
kubectl create secret generic kong-superuser-password -n kong --from-literal=password=changeit
kubectl create secret tls ingress-admin-tls-secret --key ./configmap/kong/ingress-admin.key --cert ./configmap/kong/ingress-admin.crt -n kong
<!-- kubectl create secret tls ingress-manager-tls-secret --key ./configmap/kong/ingress-manager.key --cert ./configmap/kong/ingress-manager.crt -n kong
kubectl create secret tls ingress-portal-tls-secret --key ./configmap/kong/ingress-portal.key --cert ./configmap/kong/ingress-portal.crt -n kong -->

helm install my-kong kong/kong -n kong --values ./charts/kong/minimal.yml
helm install konga ./charts/konga -n kong --values ./charts/konga/values.yml

#### Set Kong Admin API for Konga UI
https://<KongPod>:8444