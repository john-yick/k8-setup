
# Leave cluster
kubeadm reset

# Install Dashboard
kubectl create -f http://yicksolutions.ddns.net:81/endpoints/k8/content/reccommended.yaml

# Install Dashboard - Alternative HTTP only
kubectl create -f http://yicksolutions.ddns.net:81/endpoints/k8/content/alternative.yaml

# Expose Dashboard
KUBE_EDITOR="nano" kubectl -n kubernetes-dashboard edit service kubernetes-dashboard
## Change ClusterIP to NodePort

kubectl -n kubernetes-dashboard get service kubernetes-dashboard
kubectl port-forward -n kubernetes-dashboard service/kubernetes-dashboard 8080:443



## cert manager
kubectl create serviceaccount dashboard-admin-sa
kubectl create clusterrolebinding dashboard-admin-sa --clusterrole=cluster-admin --serviceaccount=default:dashboard-admin-sa
kubectl get secrets
kubectl describe secret dashboard-admin-sa-token-hgwgs

kubectl create namespace cert-manager
helm repo add jetstack https://charts.jetstack.io
helm repo update
kubectl apply --validate=false -f https://github.com/jetstack/cert-manager/releases/download/v0.15.1/cert-manager.crds.yaml

## Edit host file
cat <<EOF>> /etc/hosts
192.168.1.86 node-1 
192.168.1.200 node-2 
192.168.1.191 node-3
EOF


kubectl get pods --all-namespaces 

kubectl taint nodes --all node-role.kubernetes.io/master-


## Create Service
cat <<EOF | kubectl apply -f -
apiVersion: v1
kind: Service
metadata:
  labels:
    app: app-filestash-testing
  name: app-filestash-testing
spec:
  ports:
  - port: 10000
    protocol: TCP
    targetPort: 8334
  selector:
    app: app-filestash-testing
EOF

## Create Ingress
cat <<EOF | kubectl apply -f -
apiVersion: networking.k8s.io/v1beta1
kind: Ingress
metadata:
  name: app-filestash-testing
spec:
  rules:
  - host: yicksolutions.ddns.net
    http:
      paths:
      - backend:
          serviceName: app-filestash-testing
          servicePort: 10000
EOF

curl https://packages.gitlab.com/install/repositories/gitlab/gitlab-ce/script.rpm.sh | sudo bash
sudo EXTERNAL_URL="https://yicksolutions.ddns.net" yum install -y gitlab-ce