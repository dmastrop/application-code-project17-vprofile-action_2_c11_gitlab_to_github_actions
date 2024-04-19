# application-code-project17-vprofile-action workspace

This workspace has the source code and the k8s yml files to do the overlay of the vprofile applicaton 
onto the running EKS cluster. 
The k8s EKS cluster is brought up via terraform and the main-terraform-project17-iac-vprofile workspace

Once the infra is up and running, the .github/workflows/main.yml can be run to deploy the full stack onto the k8s cluster.

main.yml consists of the following jobs and steps

# JOB1: Testing
This runs a maven test and then checkstyle. installs java11 and sonarqube onto the github runner
The runner runs a sonarcloud sonarqube analysis on the code
A sonarcloud quality gate check is also run
If this passes it goes to JOB2

# JOB2: Build and publish the .war artifact to ECR as a docker image so that it can be deployed to the EKS cluster
This uses github actions appleboy/docker-ecr-action@master
Use the Dockerfile in the source code to build the docker image artifact
Once the artifact is on ECR it is ready to be deployed onto the EKS cluster as an appication using helm

# JOB3: Deploy the artifact to EKS
Checkout the code. The code has the helm chart and templates as yaml files for the deployment of the vprofile full stack. The vprofile app consists of tomcat, backend rabbitmq, memcached, and mysql db
(NOTE the previous terraform IaaC infra deployment (separate repo) deploys an nginx controller and front end to steer the traffic to the tomcat server created by the helm chart).
The runner is configured to run kubectl against the EKS cluster and then deploy the helm chart templates using github actions bitovi/github-actions-deploy-eks-helm@v1.2.9
At this point the full stack vprofile app is deployed to the EKS cluster and end to end can be tested in browser
NOTE: the yaml files are under kubenetes directory in this repo but are copied to helm/vprofilecharts/templates directory.  

The vproingress.yaml HOST needs to be modified to the domain URL that I am using on Google DNS
Use a CNAME and map it to the loadbalancer URL

The vproappdep.yml needs the image: modified to use the helm dynamic versioning: image: {{ .Values.appimage}}:{{ .Values.apptag}}
This ensures that the latest image on ECR is deployed as the stack.


# To redeploy the vprofile-stack without tearing down the IaaC EKS cluster:
Do this from a terminal that has kubectl access to the cluster
1.	Helm uninstall vprofile-stack
2.	Kubectl delete secret regcred
3.	Repush to the main repo of application workspace to run a new build and redeploy the helm release








# Prerequisites
#####
- JDK 11
- Maven 3
- MySQL 8 

# Technologies 
- Spring MVC
- Spring Security
- Spring Data JPA
- Maven
- JSP
- MySQL
# Database
Here,we used Mysql DB 
MSQL DB Installation Steps for Linux ubuntu 14.04:
- $ sudo apt-get update
- $ sudo apt-get install mysql-server

Then look for the file :
- /src/main/resources/db_backup.sql
- db_backup.sql file is a mysql dump file.we have to import this dump to mysql db server
- > mysql -u <user_name> -p accounts < db_backup.sql
