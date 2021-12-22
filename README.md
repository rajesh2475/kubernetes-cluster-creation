# kubernetes-cluster-creation (kubeadmin)

Create an cluster with kubeadm
  1. worker node = 2
  2. master node = 1


Note : the minimum size for the instance should be t2.small
Create an 3 instance in cloud ex- aws, as one for master and other two for worker nodes

Install the packages:
  1. kubeadm
  2. docker 

Reference : 1. https://kubernetes.io/docs/setup/production-environment/tools/kubeadm/install-kubeadm/
            2. https://rancher.com/docs/rancher/v2.6/en/installation/requirements/installing-docker/
            
Insted of installing packages one by one we can create an shell script(ex-install.sh) and install the packages in the instances which we have created.
Copy the file to instance using scp and make the file executable (chmod -x <filename>) ex- chmod -x install.sh
Run the script file (./install.sh) or sh install.sh
  
Reference : 1. https://www.fosstechnix.com/setup-kubernetes-cluster-using-kubeadm/#:~:text=kubeadm%20init%20%28on%20master%20node%29%20%3A%20It%20is,kubeadm%20version%3A%20It%20used%20to%20check%20kubeadm%20version.
  
Once you complete installation, we need to initilize control plane on master machine
  kubeadm init(on master node) : It is used to initialize and configure any node as a master node.
  kubeadm join(on worker node): It is used to initialize and configure any node as worker node.
  kubeadm token: It is used to genrate token.
  kubeadm version: It used to check kubeadm version.
  
# Initialize the Master node using kubeadm (on Master Node)
    sudo kubeadm init --apiserver-advertise-address=<master-ip-address> --pod-network-cidr=192.168.10.0/16
    Note : Kubernetes cluster required 2 core of CPU, if you are using single core of CPU then you need to add one more argument to the command
      kubeadm init --apiserver-advertise-address=<ip-address> --pod-network-cidr=192.168.10.0/16 --ignore-preflight-errors=all
  
 # Output of the above command 
    Your Kubernetes control-plane has initialized successfully!
    To start using your cluster, you need to run the following as a regular user:
      mkdir -p $HOME/.kube
      sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
       sudo chown $(id -u):$(id -g) $HOME/.kube/config
    You should now deploy a pod network to the cluster.
        Run "kubectl apply -f [podnetwork].yaml" with one of the options listed at:https://kubernetes.io/docs/concepts/cluster-administration/addons/
    Then you can join any number of worker nodes by running the following on each as root:
      kubeadm join 172.31.16.86:6443 --token 2qmwga.qyejfbo9vouiowlt \
        --discovery-token-ca-cert-hash sha256:083a2a20c8de9254100f1b37b4be1999946aee6f34791985c80d9eced9618e94
  
  
  # based on the output above, run the below commands
    sudo mkdir -p $HOME/.kube
    sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
    sudo chown $(id -u):$(id -g) $HOME/.kube/config
  
  # Configure Pod Network choose any overlay newtors - (calico or Weave) to comminicate between between master and worker
    1. Calico
        Refererence link - https://projectcalico.docs.tigera.io/getting-started/kubernetes/self-managed-onprem/onpremises
        Command to install if less then 50 nodes
          1. curl https://docs.projectcalico.org/manifests/calico.yaml -O
          2. kubectl apply -f calico.yaml
    2. Weave 
        Reference : https://www.fosstechnix.com/setup-kubernetes-cluster-using-kubeadm/#:~:text=kubeadm%20init%20%28on%20master%20node%29%20%3A%20It%20is,kubeadm%20version%3A%20It%20used%20to%20check%20kubeadm%20version.
        Command to install
          1. kubectl apply -f "https://cloud.weave.works/k8s/net?k8s-version=$(kubectl version | base64 | tr -d '\n')"
  
  
  # Once you install the network layer 
      We need to run the JOIN command on the worker instance which we got in the output
      Command :
          1. kubeadm join 172.31.16.86:6443 --token 2qmwga.qyejfbo9vouiowlt \
              --discovery-token-ca-cert-hash sha256:083a2a20c8de9254100f1b37b4be1999946aee6f34791985c80d9eced9618e94
  
  
 Usefull commands
 1. sudo kubectl get pods --all-namespaces
 2. sudo kubectl get nodes
  
 # If token expired 
    kubeadm token list (token expires in 24 hrs) ------------ on master
      - If you are trying to add new node after soma days since the token is will be expired in 24 hrs, you need to create an new token so that it will allow to add the node
      - kubeadm token create --print-join-command        --------- Copy the o/p command and paste it in the instance which you have created recently, to that it will add the node to the cluster
  
# Get the lost config file
   - reference : https://stackoverflow.com/questions/54841703/kubernetes-lost-kube-config
  
  
# CREATE AN CLUSTER IN AWS USING KOPS
  Reference : https://www.upnxtblog.com/index.php/2018/04/23/kubernetes-tutorial-create-kubernetes-cluster-on-aws-with-kops/

  
  


 
