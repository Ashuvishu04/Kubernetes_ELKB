// Pre-requisite
    
    // See whether virtualization is enabled
    sysctl -a | grep -E --color 'machdep.cpu.features|VMX'
    
    // Install Virtual Box or HyperKit for minikube to deploy
    brew cask install virtualbox / brew install hyperkit
    
    // Install MiniKube
    brew install minikube
    
    // Start Minikube
    minikube start / minikube start --vm-driver=<driver_name>
     --cpus 4 --memory 6144 (To change default resources)
    
    // To minikube running status
    minikube status
    
    // To see minikube dashboard
    minikube dashboard
    
    // To see minikube running node
    kubectl get nodes
    
    // To see containers in the pods
    kubectl get pods
    
// Deploy ECK in kubernetes cluster

    // Install the operator
    kubectl apply -f https://download.elastic.co/downloads/eck/1.2.1/all-in-one.yaml
    
// Deploy an Elastic Search Cluster
    
    kubectl apply -f ./Documents/Wynk_Workspace/Kubernetes_ELKB/src/com/akshay/kubernetes/Clusters/ESCluster/es.yml
    kubectl get elasticsearch
        
        // To Trouble Shoot
        kubectl describe pod <pod_name>
        kubectl logs <pod_name>
    
//  Deploy Kibana Cluster 
 
    kubectl apply -f ./Documents/Wynk_Workspace/Kubernetes_ELKB/src/com/akshay/kubernetes/Clusters/KibanaCluster/kibana.yml
    kubectl get kibana
    
// Open Kibana after Deployment

    kubectl port-forward service/quickstart-kb-http 5601  
    
// Jenkins Installation //

    brew install jenkins-lts
    brew services start jenkins-lts
    // After this jenkins is hosted on http://localhost:8080/
     
   
   Steps: 
   Sign up to AWS
   Create IAM user ,id key
   Set up EKS (Elastic Kubernetes Service)
   
   Go to jenkins machine - ssh -i jenkins.pem  ubuntu@13.127.5.11
   Installation -  aws cli,aws configure,  Eks to create kubernates cluster, Kube config
   sudo su 
   kubectl get svc (Get external ip to open kibana)
   kubectl get pods
   kubectl exec -it <pod_name> bash (To enter into the pod)
   Demo Test
  
  
   
  
   
      