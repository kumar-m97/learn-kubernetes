# Things about K8S you must know!

## SSL/TLS Configuration in Ingress:  
You need to create a secret of type tls and provide the tls.crt and tls.key in the data block.  
<img width="429" height="310" alt="image" src="https://github.com/user-attachments/assets/85d497de-fdee-430c-9b4f-9a76f0163929" />  

Configure it in the Ingress.yml file as below:  
<img width="695" height="790" alt="image" src="https://github.com/user-attachments/assets/783e86b3-bed4-4a03-9c27-4b5c220c95e8" />

**Flow of the HTTPS Request**  
<img width="607" height="343" alt="image" src="https://github.com/user-attachments/assets/5f8d04d5-21f3-4269-bcb0-f47c95c6520c" />


## Deployment Strategies in K8S  

### Rolling Update  
--> Apply in the deployment.yml file under strategy. Create new pods with updated code, wait till its ready and then remove the old pod. makes sure at least N pods are always running.   
![alt text](image.png)

### Blue Green Deployment  
--> Create two deployment.yml files.  
1. app-blue  (current deployment)
2. app-green (new deployment)

Point the service to new deployment app-green using selector. Test it correctly and if something goes wrong, change the service pointing back to app-blue.  

### Canary Deployment  
--> The traffic is moved to the new updated deployment gradually, not all at once.  
Assuming the stable version is deployed and running.  
<img width="697" height="679" alt="image" src="https://github.com/user-attachments/assets/1d1e836b-6e45-4e96-8bec-31522c80cffc" />  

Service of this version is below  
<img width="505" height="371" alt="image" src="https://github.com/user-attachments/assets/011bf45e-a5e2-444b-a38c-bbde30173974" />  

Now to deploy new version via canary deployment, we define a new deployment.yml file with version as v2 and new service as well pointing to the new v2 version deployment.  
<img width="705" height="806" alt="image" src="https://github.com/user-attachments/assets/04062124-1796-4c7f-9013-763c88ac02c9" />  

<img width="567" height="491" alt="image" src="https://github.com/user-attachments/assets/935a715d-26a0-4a39-b7d4-fd6d5f771cb2" />  

Now the main ingress should look like the below which is pointing to stable version as of now and sends 100 % traffic
<img width="644" height="602" alt="image" src="https://github.com/user-attachments/assets/a274e2e3-0fe3-490b-8881-1b4e10f08715" />  

Now create a new Canary Ingress with annotations having the canary weight to send 10 % of traffic to the new update version v2 deployment.  
<img width="741" height="693" alt="image" src="https://github.com/user-attachments/assets/4b5583f6-033a-4237-ba5d-032f1160bf79" />

Increase the traffic gradually and test accordingly and once 100% traffic is comming from new Canary deployment, the new version deployment is being served fully.

### Key Config for Zero Downtime  
Configure readiness probe in the deployment. By doing this traffic is sent to only healthy pods.  
<img width="372" height="190" alt="image" src="https://github.com/user-attachments/assets/e8f3e89b-0ed6-4ff1-bc38-872166ad5652" />  

## Rollback strategies in Kubernetes  

### Using Deployment  
In real Kubernetes clusters, rollback is mainly handled by Deployments. Kubernetes keeps old versions (ReplicaSets) so you can go back if a rollout fails.
**Every time you update a Deployment:**  
Kubernetes creates a new ReplicaSet  
Old ReplicaSets are kept (by default last 10)  
Each ReplicaSet = one version of your app 

<img width="1045" height="790" alt="image" src="https://github.com/user-attachments/assets/257586de-5b2d-4b74-a031-6feee5f399d9" />  
<img width="1031" height="425" alt="image" src="https://github.com/user-attachments/assets/1b07ced1-3f6e-4cfa-ae89-009ae5850668" />

### Using Helm  
When you deploy using Helm, rollback is handled at the Helm release level.
Helm tracks every install/upgrade as a release revision and lets you go back to any previous revision.  

We can check the history via  
helm history myapp  

<img width="1025" height="461" alt="image" src="https://github.com/user-attachments/assets/aab8f0ab-8574-45cc-9433-e3e1c8ae746d" />  

<img width="1044" height="493" alt="image" src="https://github.com/user-attachments/assets/f2661a46-d440-4e0c-83ca-c8180801801e" />








