# kubernetes-workshop

  https://codebunk.com/b/6231100166602/

sudo apt update


sudo apt install docker.io

sudo systemctl start docker

sudo systemctl enable docker

sudo systemctl status docker

sudo apt-get update && sudo apt-get install -y apt-transport-https curl

curl -s https://packages.cloud.google.com/apt/doc/apt-key.gpg | sudo apt-key add -

cat <<EOF | sudo tee /etc/apt/sources.list.d/kubernetes.list
deb https://apt.kubernetes.io/ kubernetes-xenial main
EOF


sudo apt-get update

sudo apt-get install -y kubelet kubeadm kubectl

sudo apt-mark hold kubelet kubeadm kubectl

On Master:

sudo -i

kubeadm init --apiserver-advertise-address=172.31.31.146 --pod-network-cidr=192.168.0.0/16 --ignore-preflight-errors=NumCPU

exit

mkdir -p $HOME/.kube
  sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
  sudo chown $(id -u):$(id -g) $HOME/.kube/config


curl https://docs.projectcalico.org/manifests/calico.yaml -O

kubectl apply -f calico.yaml


kubectl get nodes

On Slave(S)

sudo -i

kubeadm join 172.31.31.146:6443 --token z8l23l.sz8njotmvpp93d9h \
    --discovery-token-ca-cert-hash sha256:bd963806eaa1cf5e95f05c84f01d0c874329afdce30fbb14fb91d09a6528a4ec
    
On Master:


kubectl get nodes

NAME               STATUS   ROLES    AGE    VERSION
ip-172-31-26-21    Ready    <none>   102s   v1.19.3
ip-172-31-31-146   Ready    master   13m    v1.19.3
    
    
    
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx-deployment
  labels:
    app: nginx
spec:
  replicas: 3
  selector:
    matchLabels:
      app: nginx
  template:
    metadata:
      labels:
        app: nginx
    spec:
      containers:
      - name: nginx
        image: nginx:1.7.9
        ports:
        - containerPort: 80



   29  vi nginx-deployment.yaml
   30  kubectl apply -f nginx-deployment.yaml
   31  kubectl get deployments
   32  kubectl get replicasets
   33  kubectl get pods
   34  kubectl get pods -o wide
   35  curl 192.168.240.194
   36  clear
   37  kubectl get pods -o wide
   38  curl 192.168.240.194
   39  clear
   40  kubectl create service nodeport nginx --tcp=80:80
   41  kubectl get services
   42  curl 10.104.41.65

kubectl delete service nginx

kubectl create service clusterip nginx --tcp=80:80

cp nginx-deployment.yaml apache-deployment.yaml


apiVersion: apps/v1
kind: Deployment
metadata:
  name: apache-deployment
  labels:
    app: apache
spec:
  replicas: 4
  selector:
    matchLabels:
      app: apache
  template:
    metadata:
      labels:
        app: apache
    spec:
      containers:
      - name: apache
        image: httpd:latest
        ports:
        - containerPort: 80


kubectl create service clusterip apache --tcp=81:80


kubectl apply -f https://raw.githubusercontent.com/kubernetes/ingress-nginx/controller-v0.40.2/deploy/static/provider/baremetal/deploy.yaml

kubectl get services -n ingress-nginx




apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: simple-webserver-example
  annotations:
    nginx.ingress.kubernetes.io/rewrite-target: /
spec:
  rules:
  - http:
      paths:
      - path: /server1
        backend:
          serviceName: nginx
          servicePort: 80
      - path: /server2
        backend:
          serviceName: apache
          servicePort: 81


kubectl apply -f ingress-rule.yaml



kubectl get services -n ingress-nginx


curl 10.100.154.150/server1

 curl 10.100.154.150/server2
 
 
 
 kubectl apply -f https://raw.githubusercontent.com/kubernetes/dashboard/v2.0.3/aio/deploy/recommended.yaml
 
 
 kubectl edit svc kubernetes-dashboard -n kubernetes-dashboard
 
 
kubectl get services -n kubernetes-dashboard

serviceaccound.yaml

apiVersion: v1
kind: ServiceAccount
metadata:
  name: admin-user
  namespace: kubernetes-dashboard
  
  
  
  clusterrole.yaml
  
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRoleBinding
metadata:
  name: admin-user
roleRef:
  apiGroup: rbac.authorization.k8s.io
  kind: ClusterRole
  name: cluster-admin
subjects:
- kind: ServiceAccount
  name: admin-user
  namespace: kubernetes-dashboard
 
 
kubectl apply -f service-account.yaml


kubectl apply -f cluterrole.yaml



kubectl -n kubernetes-dashboard describe secret $(kubectl -n kubernetes-dashboard get secret | grep admin-user | awk '{print $1}')
