#### Create k3d cluster without traefik as default ingress class
k3d cluster create k3s-local --k3s-arg '--no-deploy=traefik@server:*' --servers 3

#### Create TLS pairs
openssl req -x509 -nodes -days 365 -newkey rsa:2048 -keyout ingress-admin.key -out ingress-admin.crt -subj "/CN=admin.kong.deejiw.com/O=admin.kong.deejiw.com"

#### Pre-requisites
kubectl create namespace kong
kubectl create secret generic kong-superuser-password -n kong --from-literal=password=changeit
kubectl create secret tls ingress-admin-tls-secret --key ./configmap/kong/ingress-admin.key --cert ./configmap/kong/ingress-admin.crt -n kong

helm install my-kong kong/kong -n kong --values ./charts/kong/minimal.yml
helm install konga ./charts/konga -n kong --values ./charts/konga/values.yml
kubectl delete jobs -n kong --all

#### Set Kong Admin API for Konga UI
https://<KongPod>:8444

#### Add Custom DNS for Kong Proxy LB 
kubectl apply -f ./manifests/coredns-custom.yml
