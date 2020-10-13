# Run the project on local K8S cluster

## Prerequsites

- Install [Minikube](https://minikube.sigs.k8s.io/docs/start/)
- Install [kubectl](https://kubernetes.io/docs/tasks/tools/install-kubectl/)


## Steps: 
- Build docker image and push it to Google Container Registry [GCP Documentation](https://cloud.google.com/container-registry/docs/pushing-and-pulling)

    - At K8S cluster on GCP, SHA hash from Gibhub repo is used as image tag number instead of "latest". This is because K8S cluster won't detect the change in tag number, hence it will not pull the image when the new image has been built in Container Registry (SHA hash is attached at CI pipeline of each service)

    - At local cluster, developer need to ensure the latest tag name on Container Registery actually contains the latest source code, therefore building and pushing the image locally is required

- After docker image has been built and pushed to Container Registry (with latest as a tag), run following:

```bash
kubectl apply -f local
```

- Get IP address of your local K8S cluster

```bash
minikube ip
```

- Open your browser and access the IP address shown in previous step




