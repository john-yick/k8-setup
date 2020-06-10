
# Leave cluster
kubeadm reset

# Install Dashboard
kubectl create -f http://yicksolutions.ddns.net:81/endpoints/k8/content/reccommended.yaml
kubectl port-forward -n kubernetes-dashboard service/kubernetes-dashboard 8080:443

KUBE_EDITOR="nano" kubectl -n kubernetes-dashboard edit service kubernetes-dashboard


## cert manager
kubectl create serviceaccount dashboard-admin-sa
kubectl create clusterrolebinding dashboard-admin-sa --clusterrole=cluster-admin --serviceaccount=default:dashboard-admin-sa
kubectl get secrets
kubectl describe secret dashboard-admin-sa-token-hgwgs

kubectl create namespace cert-manager
helm repo add jetstack https://charts.jetstack.io
helm repo update
kubectl apply --validate=false -f https://github.com/jetstack/cert-manager/releases/download/v0.15.1/cert-manager.crds.yaml