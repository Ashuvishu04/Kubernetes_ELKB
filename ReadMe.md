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