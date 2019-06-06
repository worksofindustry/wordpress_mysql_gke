<a href="url"><img src="/media/WordPress-logotype-wmark.png" align="right" height="48" width="48" ></a>
<a href="url"><img src="https://www.mysql.com/common/logos/logo-mysql-170x115.png" align="right" height="48" width="48" ></a>
<br>
# Deployment Playbook

## Provision Storage
Start by first uploading all .yaml configuration files. Then to allocate storage for the deployment you run the following commands from the master node of your kubernetes cluster.

```bash
kubectl apply -f mysql-volumeclaim.yaml
kubectl apply -f wordpress-volumeclaim.yaml
```
To check on the status of the storage request run the following code.
```bash
kubectl get pvc
```

## Set Up MySQL
> You create a password for the database by generating a secret by running the following command and 
> replacing YOUR_PASSORD with a one of your choice. 

```bash
kubectl create secret generic mysql --from-literal=password=YOUR_PASSWORD
```
 Now create a single instance deployment of MySql by running the mysql.yaml manifest
```bash
kubectl create -f mysql.yaml
```
You can check on the status of the pod by running
```bash
watch kubectl get pod -l app=mysql  #it can take several minutes to initially create
```
Now to make the MySQL accessible by WordPress you make a service by running the command below.
```bash
kubectl create -f mysql-service.yaml
```
And to watch it create run: kubectl get service | grep mysql

## Deploying WordPress
Deploy Word Press by running the following command against the wordpress.yaml manifest file.
```bash
kubectl apply -f wordpress.yaml
```
You can verify the pod creation by running kubectl get deployment | grep wordpress

## Exposing WordPress
> To expose your application to world you will be running this manifest file to direct data from the internet 
> using a load balancer ingress. 
```bash
kubectl apply -f wordpress-service.yaml
-and to watch the service creation, allocating an external-IP will take several minutes. 
kubectl get svc -l app=wordpress
```

## Cleaning Up
To avoid additional charges to your GCE account of usage of credits uninstall the deployment by executing the
folowing kubectl commands. 
```bash
kubectl delete service wordpress
kubectl delete pvc wordpress-volumeclaim
kubectl delete pvc mysql-volumeclaim
```
