# Run the project on local K8S cluster

## Prerequsites

- Install [Minikube](https://minikube.sigs.k8s.io/docs/start/)
- Install [kubectl](https://kubernetes.io/docs/tasks/tools/install-kubectl/)


## Steps: 
- Build docker image and push it to Google Container Registry [GCP Documentation](https://cloud.google.com/container-registry/docs/pushing-and-pulling)

    - At K8S cluster on GCP, SHA hash from Gibhub repo is used as image tag number instead of "latest". This is because K8S cluster won't detect the change in tag number, hence it will not pull the image when the new image has been built in Container Registry (SHA hash is attached at CI pipeline of each service)

    - At local cluster, developer need to ensure the latest tag name on Container Registery actually contains the latest source code, therefore building and pushing the image locally is required

- After docker image has been built and pushed to Container Registry (with latest as the tag), take following steps:

    - Get IP address of your local K8S cluster

```bash
minikube ip
```
    
   - Copy the IP address in env variable, NEXT_PUBLIC_MINIKUBE_IP at deployment config of client service

   - Make sure you have Google Container Registry secret created and add the name of the secret in imagePullSecrets field in deployment config
   

- Your client-deployment config file should look something like below:

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: client-deployment
spec:
  replicas: 2
  selector: 
    matchLabels:
      component: web
  template:
    metadata:
      labels:
        component: web
    spec:
      containers:
        - name: client
          image: eu.gcr.io/surfschool-finder/client:latest
          ports:
            - containerPort: 10230
          env:
            - name: NEXT_PUBLIC_EXE_CTX
              value: "minikube"
            - name: NEXT_PUBLIC_MINIKUBE_IP
              value: 192.168.64.3
      imagePullSecrets:
        - name: gcr-json-key
        
```

- Finally run below command and visit your minikube ip address on web browser

```bash
kubectl apply -f local
```






