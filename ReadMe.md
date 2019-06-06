#Deployment Playbook


###Start by allocating necessary storage for the deployment. 

```bash
kubectl apply -f mysql-volumeclaim.yaml
kubectl apply -f wordpress-volumeclaim.yaml
```

###Check on the status of the storage request
```bash
kubectl get pvc
```

###Set Up MySQL
######You create a password for the database by generating a secret by running the following command and replacing YOUR_PASSORD with a one of your choice. 

```bash
kubectl create secret generic mysql --from-literal=password=YOUR_PASSWORD
```

###Now create a single instance deployment of MySql by running the mysql.yaml manifest
```bash
kubectl create -f mysql.yaml
```
You can check on the status of the pod by running
```bash
watch kubectl get pod -l app=mysql  #it can take several minutes to initially create
```

###Create MySQL Service to make it accessible to the wordpress deployment that will be created later.
```bash
kubectl create -f mysql-service.yaml
```
And to watch it create run: kubectl get service | grep mysql

###Set up Word Press

Deploy Word Press by running the following command against the wordpress.yaml manifest file
```bash
kubectl apply -f wordpress.yaml
```
And to watch the pods creation run: kubectl get deployment | grep wordpress

###Expose WordPress Service
To expose your application to world you will be running this manifest file to traffic data from the internet using a load balancer ingress. 
```bash
kubectl apply -f wordpress-service.yaml
-and to watch the service creation, allocating an external-IP will take several minutes. 
kubectl get svc -l app=wordpress
```

Cleaning Up
```bash
kubectl delete service wordpress
kubectl delete pvc wordpress-volumeclaim
kubectl delete pvc mysql-volumeclaim
```






