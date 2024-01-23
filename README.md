# k8s-monitoring

## Configuration
- [Docker](https://kubernetes.io/docs/tasks/tools/)
- [KinD](https://kind.sigs.k8s.io/)
- [Kubectl](https://kubernetes.io/docs/tasks/tools/)
- Using Kind create the cluster with `kind create cluster --name=kluster`

## Monitoring Nodejs
- Access the app folder `cd app`
- Create a build image from the NodeJS application with `docker build . -f Dockerfile -t nodejs`
- Test if the application is running `docker run -p 3000:3000 nodejs`
- Using Kind push the image to the cluster `kind load docker-image nodejs:latest --name kluster`
- Create the application namespace `kubectl create namespace application`
- Apply the kubernetes manifest files with `kubectl apply -f ./app/kubernetes -n application`
- Check if it's fine with `kubectl get pods -n application`
```bash
NAME                          READY   STATUS    RESTARTS   AGE
pod/nodejs-6bb568674c-f2vlf   1/1     Running   0          12m

NAME             TYPE           CLUSTER-IP     EXTERNAL-IP   PORT(S)        AGE
service/nodejs   LoadBalancer   10.96.78.247   <pending>     80:31694/TCP   12m

NAME                     READY   UP-TO-DATE   AVAILABLE   AGE
deployment.apps/nodejs   1/1     1            1           12m

NAME                                DESIRED   CURRENT   READY   AGE
replicaset.apps/nodejs-6bb568674c   1         1         1       12m
```
- Run the port forward with `kubectl port-forward nodejs-6bb568674c-f2vlf 3000:3000 -n application`

## K8s & Prometheus
- Create the monitoring namespace `kubectl create namespace monitoring`
- Create the cluster role to allow Prometheus to get and read data from k8s `kubectl create -f clusterRole.yaml`
- Create the config-map `kubectl create -f config-map.yaml`
- Create a Prometheus deployment `kubectl create -f deployment.yaml` 
- Or use the `kubectl create -f ./prometheus`
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
- Port forwarding to access Prometheus `kubectl port-forward prometheus-deployment-57898c796b-kcxxx 9090:9090 -n monitoring`. Replace the `prometheus-deployment-57898c796b-kcxxx` to the name of `kubectl get pods --namespace=monitoring` result
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
-  Run the port forward `kubectl port-forward grafana-7558b9c7b-c86gx 4000:3000 -n=monitoring`
- Access the [localhost:4000](localhost:4000)
- In dashboards, import a new one and provide the id `12740`

## Monitoring Nginx
- Create a namespace `kubectl create namespace nginx`
- Run `kubectl apply -f ./nginx/ -n nginx`
- Check the pod name with `kubectl get pods -n nginx`
```bash
NAME                     READY   STATUS    RESTARTS   AGE
nginx-68f44fcc46-jwd7f   1/1     Running   0          112s
```
- Check if the Nginx is running `kubectl port-forward nginx-68f44fcc46-jwd7f 8080:80 9114:9113 -n nginx`