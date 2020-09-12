##  Start a Kubernetes Engine cluster  

Create an enviromental variable to store the zone  

``` export MY_ZONE=us-central1-a ```   

Start a Kubernetes cluster managed by Kubernetes Engine. Name the cluster webfrontend  

``` gcloud container clusters create webfrontend --zone $MY_ZONE --num-nodes 2 ```  

Check your installed version of Kubernetes  

``` kubectl version```  

## Run and deploy a container    

Launch a single instance of the nginx container 

``` kubectl create deploy nginx --image=nginx:1.17.10 ```  

View the pod running the nginx container  

``` kubectl get pods ```  

Expose the nginx container to the Internet  

``` kubectl expose deployment nginx --port 80 --type LoadBalancer```  

View the new service  

``` kubectl get services ``` 

Scale up the number of pods running on your service   

``` kubectl scale deployment nginx --replicas 3 ```  

Confirm that Kubernetes has updated the number of pods  

``` kubectl get pods ```  

Confirm that your external IP address has not changed  

``` kubectl get services ```
