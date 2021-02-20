# Hello-Helm

## Basic commands to create and use Helm Charts locally.

``` helm create webapp ```

Delete below files

``` 
/templates/tests/test-connection.yaml
/templates/serviceaccount.yaml
/templates/ingress.yaml
/templates/hpa.yaml
```

Edit/Add `Chart.yaml`, `values.yaml`, `prod-values.yaml`, `/template/deployment.yaml`, `/template/service.yaml` and modify `/template/NOTES.txt` as required.

## Dry-run (get the final output that would be sent to Kubernetes API Server)
```
helm template --debug webapp -f values.yaml

helm template --debug webapp -f prod-values.yaml
```

## Install
```
 helm install webapp-demo webapp/ -f values.yaml
```

## Upgrade
```
helm upgrade webapp-demo webapp/ -f prod-values.yaml
```

## Delete
```
helm delete webapp-demo
```