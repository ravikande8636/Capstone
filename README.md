# Capstone Blue Green Deployment
# Project Overview

- Creating a Blue Green Deployment with Jenkins
- Containerized Application Deployed with K8s.



## Tools used

- Jenkins
- Docker
- EKS



## Prerequisite

- Jenkins Master Instance 

  - Install Python and Java

  - Install Jenkins

    - Install Plugins for Integration.
      - Blue Ocean
      - Github 
      - pipeline-aws
      - Docker
      - Build Step

  - Install Docker
  - Install Kubectl
  - Install eksctl

  - Jenkins has been setup with Serviceaccounts
  - Create in Jenkins the credentials for AWS
  - Create in Jenkins the credentials for Docker

- Docker Hub Account needed for Image registry


    

## Setting up the Environment

### Create VPC and Networking with Cloud formation Template
- Run .\create.sh stack-name  .\K8s-VPC.yml .\K8s-VPC-parameters.json   

### Create EKS Cluster and Worker Nodes with Cloud formation Template
- Run .\create.sh Capstone-Freshk8s .\K8s-Cluster.yml .\K8s-Cluster-parameters.json


## Jenkins pipeline
- Once Jenkins pipeline is setup and it trigger a push from git 
- Lint runs on both blue and green index.html files
- Creates docker images from Dockerfile
- Pushes docker images to Docker hub with Supplied Credentials in Jenkins
- Sets current kubectl context
- Deploys Blue and Green Containers on EKS cluster
- Load balancer will direct to Blue Deployment

## Checking the website
- Run 'kubectl get pods' for Pods status
- Run 'kubectl get service bluegreenlb' and Get EXTERNAL-IP with PORTS
- Check the website with http://EXTERNAL-IP:8000

## Switching to Green Deployment
- On Jenking Pipeline "Notification with pause" Hit Proceed
- Loadbalancer will re-direct to Green Deployment
- Check the website with http://EXTERNAL-IP:8000