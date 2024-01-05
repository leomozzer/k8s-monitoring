# k8s-monitoring

## K8s & Prometheus
- Using Kind create the cluster with `kind create cluster --name=kluster`
- Create the monitoring namespace `kubectl create namespace monitoring`
- Create the cluster role to allow Prometheus to get and read data from k8s `kubectl create -f clusterRole.yaml`
- Create the config-map `kubectl create -f config-map.yaml`
- Create a Prometheus deployment `kubectl create -f deployment.yaml` 
- Check deployments `kubectl get deployments --namespace=monitoring`
```bash
NAME                    READY   UP-TO-DATE   AVAILABLE   AGE
prometheus-deployment   1/1     1            1           2m48s
```
- Check Prometheus Pod `kubectl get pods --namespace=monitoring`
```bash
NAME                                     READY   STATUS    RESTARTS   AGE
prometheus-deployment-57898c796b-kcxxx   1/1     Running   0          3m30s
```
- Port forwarding to access Prometheus `kubectl port-forward prometheus-deployment-57898c796b-kcxxx 8080:9090 -n monitoring`. Replace the `prometheus-deployment-57898c796b-kcxxx` to the name of `kubectl get pods --namespace=monitoring` result
- Also, it's possible to run all at once with `kubectl create -f ./prometheus`

## K8s & Grafana
- Make sure the Prometheus is running
- Run `kubectl apply -f ./state-metrics`
- Run `kubectl create -f ./grafana`
- Get pod name from grafa with  `kubectl get pods --namespace=monitoring`
```bash
NAME                                     READY   STATUS    RESTARTS   AGE
grafana-7558b9c7b-c86gx                  1/1     Running   0          42m
prometheus-deployment-57898c796b-vlvgk   1/1     Running   0          60m
```
- Access the grafana with `kubectl port-forward grafana-7558b9c7b-c86gx 3000:3000 -n=monitoring`

## Monitoring Nginx
- Create a namespace `kubectl create namespace nginx`
- Run `kubectl apply -f ./nginx/ -n nginx`
- Check the pod name with `kubectl get pods -n nginx`
```bash
NAME                     READY   STATUS    RESTARTS   AGE
nginx-68f44fcc46-jwd7f   1/1     Running   0          112s
```
- Check if the Nginx is running `kubectl port-forward nginx-68f44fcc46-jwd7f 8080:80 9114:9113 -n nginx`
