Helper script makes it easier to deploy Acmeair to IBM Container Service (Kubernetes).

## Scripts
These are the scrpts that helps creating the Acmeair Microservices
- **IBMCloud_KS.sh** : This script supports these following operations.  Created Apps to use Ingress Controller (note, ingress.yaml file might need to be manually run. e.g. kubectl create -f ingress.yaml)
  - Install CLI plugins
  - Login
  - Clone Github
  - Deploy Containers to IBM Container Service
  - Populate Databases
  - Undeploy Containers from IBM Container Service
  
- **removeImages.sh** :This script helps deleting the images created in the IBM Container Registry. Optionally, it can delete all containers & images created on the local job execution box on Linux (Please uncomment within the script to enable optional feature)
  - Remove Images

## Prerequisites
In order to build Java Images, the hosting server must be **Linux**.  Also, [docker](https://www.docker.com/),[git](https://git-scm.com/downloads),  Java & [Maven](https://maven.apache.org/) must be installed

run command **mvn** to verify that maven is properly installed.

Verify that **docker daemon is running** on your linux.

Run the following to get the helper scripts
- git clone https://github.com/blueperf/helper

## Setup
Edit the script (IBMCloud_KS.sh) to enter these values
- API_KEY
  - API Key can be generated through [IBM Cloud Dashboard](https://console.bluemix.net/dashboard). Go to "Manage" > "Security" > "IBM Cloud API Keys" to generate one.
- CLUSTER_NAME
  - Kubernetes Cluster that you have created (Free cluster will not work for the Acmeair Microservices). 
  - Following command will display your current clusters
    - bx cs clusters
- NAMESPACE
  - Namespace created in the IBM Container Registry to save the Docker Images. 
  - Following command will display your current namespaces
    - bx cr namespaces
- (OPTIONAL) If using the docker repository hosted in https://cloud.docker.com INSTEAD of using Container Registry, set the following variables 
  - Set REGISTRY as your docker repository user name
  - Set DOCKER_PASSWORD as your docker repository user password
- SPACE
  - Default is **dev**.  Please change it to the space which you are going to use.
  - To find out your spaces, login [IBM Cloud Dashboard](https://console.bluemix.net/dashboard). On the top, click your account name
    - (e.g. John Doe's Account | US South : jdoe_org : dev)
  - Select the correct account, region, org, then pick the space name
- REGION
  - Default is **ng** whcih means it us US South - Dallas, TX.  Please change it to the Region which you are going to use.
  - To find out your regions, login [IBM Cloud Dashboard](https://console.bluemix.net/dashboard). On the top, click your account name
    - (e.g. John Doe's Account | US South : jdoe_org : dev)
  - Select the correct account, then pick the region name. [Find the corresponding Region Prefix to be used in the script](https://www.ibm.com/cloud-computing/bluemix/data-centers)
    - US South : ng
    - United Kingdom : eu-gb
    - Sydney, Australia : au-syd
    - Frankfurt, Germany : eu-de
- IMAGE_TAG
  - Default "latest".  Change it to create multiple versions of the images using this tag. 

## Downloading, Building, and Deploying
Running ./IBMCloud_KS.sh will give you brief command options
- To Install the Prereq CLIs, run the following command
  - ./IBMCloud_KS.sh -p

- To Run below -l, -c, -d and -db commands all together (Install all prereqs with ./IBMCloud_CS.sh -p before running this command, then Acmeair Microservices will be up and running)
  - ./IBMCloud_KS.sh -a
  
- To login IBM Cloud CLI, run the following command
  - ./IBMCloud_KS.sh -l
  
- To clone repositories from github, run the following command
  - ./IBMCloud_KS.sh -c
      
- Deploy Containers to IBM Container Service (Kubernetes).  This command will build war file, create & push the docker image to the IBM Container Registry/Docker Repository, then create deployments, pods, services, etc into the specified IBM Container Services. 
  - ./IBMCloud_KS.sh -d
  - Note: If you are using free Container Registry, it might reach maximum allowed **storage** and **pull traffic**. Please check with the command **bx cr quota**.  If the storage is reached to maximum, please delete "Authentication service" image (bx cr image-rm registry.ng.bluemix.net/moss_perf/auth-go) **AFTER** deploying to the Kubernetes.  These images are used to recover failures & auto Scaling.  If you see "ImagePulledOff" in kubectl get pods, that means either the image does not exist, or it reached to maximum pull traffic for the month.

- To Populate Acmeair DBs, run the following command
  - ./IBMCloud_KS.sh -db

- To Undeploy Containers from IBM Container Service, run the following command
  - ./IBMCloud_KS.sh -u
  
## Post Operation

Setup the KUBECONFIG
- While creating the Acmeair pods in your cluster, you see something as follows.  Please export the KUBECONFIG so that kubectl is pointed to your cluster:
  - The configuration for CLUSTER_NAME was downloaded successfully. Export environment variables to start using Kubernetes.
  - export KUBECONFIG=/root/.bluemix/plugins/container-service/clusters/XXX/kube-config-XXX-XXX.yml

Verify that all deployments are running
- kubectl get deploy

Get the Application URL:
- kubectl get ingress
  - If used IBMCloud_KS.sh, use the hostname listed under "HOSTS" for the "NAME" **acmeair-ingress**
  
 ## Driving the load
 - Follow the instruction [here](https://github.com/blueperf/acmeair-driver)
