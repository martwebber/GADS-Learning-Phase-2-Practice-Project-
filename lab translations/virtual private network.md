# Virtual Private Networks(VPN)  

## Task 1: Explore the networks and instances  

 Explore the networks  

Verify that vpn-network-1 and vpn-network-2 have been create  

### gcloud compute networks list  

Confirm that vpn-network-1 network and its subnet-a in us-central1  
``` gcloud compute networks subnets list --network=vpn-network-1 ```  

Confirm that vpn-network-2 network and its subnet-a in europe-west1    
``` gcloud compute networks subnets list --network=vpn-network-2 ```  

## Explore the firewall rules  

### List firewall rules   

gcloud compute firewall-rules list  

Note the network-1-allow-ssh and network-1-allow-icmp rules for vpn-network-1  

``` gcloud compute firewall-rules list --filter=vpn-network-1 ```  

Note the network-2-allow-ssh and network-2-allow-icmp rules for vpn-network-2  

``` gcloud compute firewall-rules list --filter=vpn-network-2 ```  

## Explore the instances and their connectivity  

###### SSH into server-1  

``` gcloud compute ssh server-1```  

``` ping -c 3 34.122.89.180 ```  

Exit the SSH terminal  

``` exit ```

###### SSH into server-2   

gcloud compute ssh server-2  

``` ping -c 3 34.78.90.133 ``` 

Exit the SSH terminal

``` exit``` 

## Task 2: Create the VPN gateways and tunnels  

### Reserve two static IP addresses  

Reserve ip for vpn-1-static-ip  
``` gcloud compute addresses create vpn-1-static-ip --project=qwiklabs-gcp-04-0afa516d53ed --region=us-central1 ```

Reserve ip for vpn-2-static-ip  
``` gcloud compute addresses create vpn-2-static-ip --project=qwiklabs-gcp-04-0afa516d53ed --region=europe-west1```  

### Create the vpn-1 gateway and tunnel1to2  

``` gcloud compute --project "qwiklabs-gcp-04-0afa516d53ed" target-vpn-gateways create "vpn-1" --region "us-central1" --network "vpn-network-1" ```

#######creating forwarding rules

``` gcloud compute --project "qwiklabs-gcp-04-0afa516d53ed" forwarding-rules create "vpn-1-rule-esp" --region "us-central1" --address "34.122.89.180" --ip-protocol "ESP" --target-vpn-gateway "vpn-1" ```  

``` gcloud compute --project "qwiklabs-gcp-04-0afa516d53ed" forwarding-rules create "vpn-1-rule-udp500" --region "us-central1" --address "34.122.89.180" --ip-protocol "UDP" --ports "500" --target-vpn-gateway "vpn-1" ```  

``` gcloud compute --project "qwiklabs-gcp-04-0afa516d53ed" forwarding-rules create "vpn-1-rule-udp4500" --region "us-central1" --address "34.122.89.180" --ip-protocol "UDP" --ports "4500" --target-vpn-gateway "vpn-1" ```  

###### creating vpn tunnel  

``` gcloud compute --project "qwiklabs-gcp-04-0afa516d53ed" vpn-tunnels create "tunnel1to2" --region "us-central1" --peer-address "34.78.90.133" --shared-secret "gcprocks" --ike-version "2" --local-traffic-selector "0.0.0.0/0" --target-vpn-gateway "vpn-1" ```  

######creating routes  

``` gcloud compute --project "qwiklabs-gcp-04-0afa516d53ed" routes create "tunnel1to2-route-1" --network "vpn-network-1" --next-hop-vpn-tunnel "tunnel1to2" --next-hop-vpn-tunnel-region "us-central1" --destination-range "10.1.3.0/24" ```  

### Create the vpn-2 gateway and tunnel2to1  

``` gcloud compute --project "qwiklabs-gcp-04-0afa516d53ed" target-vpn-gateways create "vpn-2" --region "europe-west1" --network "vpn-network-2" ```  

###### creating forwarding rules  

``` gcloud compute --project "qwiklabs-gcp-04-0afa516d53ed" forwarding-rules create "vpn-2-rule-esp" --region "europe-west1" --address "34.78.90.133" --ip-protocol "ESP" --target-vpn-gateway "vpn-2" ```  

``` gcloud compute --project "qwiklabs-gcp-04-0afa516d53ed" forwarding-rules create "vpn-2-rule-udp500" --region "europe-west1" --address "34.78.90.133" --ip-protocol "UDP" --ports "500" --target-vpn-gateway "vpn-2" ```  

``` gcloud compute --project "qwiklabs-gcp-04-0afa516d53ed" forwarding-rules create "vpn-2-rule-udp4500" --region "europe-west1" --address "34.78.90.133" --ip-protocol "UDP" --ports "4500" --target-vpn-gateway "vpn-2" ``` 

###### creating vpn tunnels  

``` gcloud compute --project "qwiklabs-gcp-04-0afa516d53ed" vpn-tunnels create "tunnel2to1" --region "europe-west1" --peer-address "34.122.89.180" --shared-secret "gcprocks" --ike-version "2" --local-traffic-selector "0.0.0.0/0" --target-vpn-gateway "vpn-2" ```

###### creating routes 

``` gcloud compute --project "qwiklabs-gcp-04-0afa516d53ed" routes create "tunnel2to1-route-1" --network "vpn-network-2" --next-hop-vpn-tunnel "tunnel2to1" --next-hop-vpn-tunnel-region "europe-west1" --destination-range "10.5.4.0/24" ```  

## Task 3: Verify VPN connectivity  

###### Verify server-1 to server-2 connectivity  

SSH into server-1  
``` gcloud compute ssh server-1 ```  
``` ping -c 3 server-2 internal ip ```  

###### Exit the server-1 SSH terminal  

``` exit ```

SSH into server-2  

``` gcloud compute ssh server-2 ```  
``` ping -c 3 server-1 internal ip ```  

###### Exit the server-1 SSH terminal  

``` exit ```  

### Remove the external IP address for server-1  

Stopping server-1 instance  
``` gcloud compute instances stop server-1 ```  

Remove ip for vpn-1-static-ip  
``` gcloud compute addresses delete vpn-1-static-ip --project=qwiklabs-gcp-04-0afa516d53ed --region=us-central1``` 

Starting server-1 instance  
``` gcloud compute instances start server-1 ```   
