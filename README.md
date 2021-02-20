# Hello-Helm

`Helm` is the package manager for Kubernetes (like yum) that allows easily package, configure, and deploy applications onto Kubernetes clusters.

`Helm charts` are packages (like rpms) It contains pre-configured kubernetes resources such as ConfigMaps, Deployments, Services and editable settings for them.

## Why do we need Helm?
Managing repeating Kubernetes manifest or copying from one to another, editing hardcode values and validating syntax, sharing across env like Dev, QA, Production etc. is hard.
- Simple Sharing
- Rollbacks

## Install Helm (within Kubernetes Master/Control Node)
```
export VERIFY_CHECKSUM=false

curl https://raw.githubusercontent.com/helm/helm/master/scripts/get-helm-3 | bash

helm version
```

## Helm 3 Architeture
Helm installs charts into Kubernetes, creating a new release for each installation. And to find new charts, you can search Helm chart repositories.
- `Repository` is the place where charts can be collected and shared.
- `Release` is an instance of a chart running in a Kubernetes cluster. 
- `Chart` is a Helm package. It contains all of the resource definitions necessary to run an application on Kubernetes Cluster

```
helm search hub wordpress # searches Artifact Hub

helm repo add bitnami https://charts.bitnami.com/bitnami

helm show values bitnami/wordpress # shows that service type uses `LoadBalancer`

cat <<EOF > values.yaml
> service:
>  type: NodePort
> EOF

helm install -f values.yaml my-wordpress bitnami/wordpress --version 8.0.2

helm list

helm repo list
```

## Creating Your Own Charts
``` 
helm create webapp 
```

## Delete below files

- /templates/tests/test-connection.yaml
- /templates/serviceaccount.yaml
- /templates/ingress.yaml
- /templates/hpa.yaml


## Edit/Add 
1. `Chart.yaml`, 
2. `values.yaml`, 
3. `prod-values.yaml`, 
4. `/template/deployment.yaml`, 
5. `/template/service.yaml` 
6. and modify `/template/NOTES.txt` as required.

## Folder Structure
```
root@node1:~/webapp# tree
.
|-- Chart.yaml
|-- README.md
|-- prod-values.yaml
|-- templates
|   |-- NOTES.txt
|   |-- _helpers.tpl
|   |-- deployment.yaml
|   `-- service.yaml
`-- values.yaml
```

## Dry-run (get the final output that would be sent to Kubernetes API Server)
```
helm template --debug webapp -f webapp/values.yaml

helm template --debug webapp -f webapp/prod-values.yaml
```

## Install
```
helm install webapp-demo webapp/ -f webapp/values.yaml

NAME: webapp-demo
LAST DEPLOYED: Sat Feb 20 17:54:37 2021
NAMESPACE: default
STATUS: deployed
REVISION: 1
TEST SUITE: None
```

## Test - `Nginx` Welcome Page
```
export NODE_PORT=$(kubectl get --namespace default -o jsonpath="{.spec.ports[0].nodePort}" services app-svc }})
export NODE_IP=$(kubectl get nodes --namespace default -o jsonpath="{.items[0].status.addresses[0].address}")
echo http://$NODE_IP:$NODE_PORT

curl http://$NODE_IP:$NODE_PORT
```

## Upgrade
```
helm upgrade webapp-demo webapp/ -f webapp/prod-values.yaml

Release "webapp-demo" has been upgraded. Happy Helming!
NAME: webapp-demo
LAST DEPLOYED: Sat Feb 20 17:56:00 2021
NAMESPACE: default
STATUS: deployed
REVISION: 2
TEST SUITE: None
```

## Test - `HTTPD` Welcome Page
```
export NODE_PORT=$(kubectl get --namespace default -o jsonpath="{.spec.ports[0].nodePort}" services app-svc }})
export NODE_IP=$(kubectl get nodes --namespace default -o jsonpath="{.items[0].status.addresses[0].address}")
echo http://$NODE_IP:$NODE_PORT

curl http://$NODE_IP:$NODE_PORT
```

## Rollback
```
helm ls

NAME            NAMESPACE       REVISION        UPDATED                                 STATUS          CHART                   APP VERSION
webapp-demo     default         2               2021-02-20 17:56:00.166067482 +0000 UTC deployed        hello-helm-0.1.0        latest
```
```
helm rollback webapp-demo 1

Rollback was a success! Happy Helming!
```
## Delete
```
helm delete webapp-demo
```