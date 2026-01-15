# All about Helm!  

### Command to create heml charts  
helm create myapp

**Values.yml** : Defaulty values to be used
**Chart.yml**  : Stores the Chart/release name and its version. also the app version
**templates**  : Contains the yml template files.

#### Check Files in the repo for syntax reference.

### Command to install the release  
helm install myapp ../helm -f values.yml

helm install myapp ../helm -f values.yml --set replicaCount=3,image.tag=3.0  **-- if needed to override the values while runtime**  

