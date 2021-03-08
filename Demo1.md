# Demos
 Open browser:
 - https://prometheus.io/docs/prometheus/latest/configuration/configuration/#configuration-file
 - https://github.com/prometheus/node_exporter
 - https://prometheus.io/docs/prometheus/latest/querying/functions/#irate
 - https://grafana.com/grafana/dashboards
 - http://localhost:3000/
 - http://localhost:9090

## Demo1: Local OSX demo + brew

0- Install
```
brew install prometheus
brew install grafana
brew install node_exporter
```

1- Run prometheus, grafana and node exporter
```
# config and ini files in 
# /usr/local/etc/
brew services start prometheus
brew services start grafana
brew services start node_exporter
```

2- Validate web UI

Prometheus
localhost:9090

Grafana
localhost:3000

3- Increase system load
```
openssl speed
```
- open LogicProX
- Open Youtube video

4- Show on template dashboards

5- Create a dashboard/ PromQL
```
irate(node_cpu_seconds_total {mode!="idle"} [1m])
```
More functions to explore
https://prometheus.io/docs/prometheus/latest/querying/functions/#irate
https://utcc.utoronto.ca/~cks/space/blog/sysadmin/PrometheusCPUStats

6- Create another target
- run vagrant init or create local vm
```
vagrant up
vagrant ssh
```
- forward node_exporter port 9100 to 9101
- install node_exporter
```
wget https://github.com/prometheus/node_exporter/releases/download/v1.1.2/node_exporter-1.1.2.linux-amd64.tar.gz
tar xvfz node_exporter-1.1.2.linux-amd64.tar.gz
cd node_exporter-1.1.2.linux-amd64
./node_exporter &
```

7- Since no service discovery, add a new static target to prometheus scraping
```
vi /usr/local/etc/prometheus.yml
```

8- Check target on prometheus ql

## Demo2: Minikube + helm

Minikube starts with 2 CPUs and 2GB Mem default
Prometheus expects XX


0- Install
```
brew install minikube
brew install helm
```

1- Run minikube
```
# Create VM as single node K8s cluster
minikube --memory 8192 --cpus 4 start

# Check all ok
kubectl get node minikube -o jsonpath='{.status.capacity}'
kubectl get po -A
minikube dashboard &
```

2- kube-prometheus-stack (Former Prometheus-Operator)
```
# Installs prometheus, grafana, alerts, etc

helm namespace create monitor
helm install -n monitor kube-prometheus-stack prometheus-community/kube-prometheus-stack
```

3- Expose Grafana
```
kubectl get pods
kubectl --namespace monitor port-forward kube-prometheus-stack-grafana-dcb95dccd-5z7vm 3000
```

4- Grafana admin password is prom-operator
https://github.com/prometheus-community/helm-charts/blob/28c4ca6e21e6d049780b8359b990e8fc3a11afed/charts/kube-prometheus-stack/values.yaml#L608

5- Add datasource and dashboards

6- Import dashboards from Grafana

7 - Install redis
```
helm install redis stable/redis
export REDIS_PASSWORD=$(kubectl get secret --namespace default redis -o jsonpath="{.data.redis-password}" | base64 --decode)

kubectl run --namespace default redis-client --rm --tty -i --restart='Never' \
    --env REDIS_PASSWORD=$REDIS_PASSWORD \
   --image docker.io/bitnami/redis:5.0.7-debian-10-r32 -- bash

redis-cli -h redis-master -a $REDIS_PASSWORD

set key "Test"
get key
```


## Installing Prometheus and Grafana Independently

2- Add prometheus helm from community
```
helm repo add prometheus-community https://prometheus-community.github.io/helm-charts
```

3- Check available exporters 
```
helm search repo prometheus-community
```

4- Check chart configuration before installing
```
https://github.com/prometheus-community/helm-charts/tree/main/charts/prometheus#configuration

# By default it will install:
- Scrapping pod metrics via annotations
- node_exporter
- kube_state-metrics
- Alert manager, configured using alermanager.yml
```


4- For demo can install kube-prometheus-stack which includes Grafana If desired, enter updated values in values.yaml
```
kubectl create ns monitor
 helm install prometheus-stack prometheus-community/kube-prometheus-stack --namespace monitor
# helm install -f myvalues.yaml demo-prometheus prometheus-community/prometheus
```

6- Check pods
```
kubectl get pods -n monitor
```

7- Check Prometheus UI
```
export POD_NAME=$(kubectl get pods --namespace monitor -l "app=prometheus,component=server" -o jsonpath="{.items[0].metadata.name}")
kubectl --namespace monitor port-forward $POD_NAME 9090
```

7- Create demo app
```
# source https://sysdig.com/blog/kubernetes-monitoring-prometheus/

git clone git@github.com:stenio123/prometheus-monitoring-guide.git
kubectl create -f prometheus-monitoring-guide/redis_prometheus_exporter.yaml
```

8- Open Prometheus dashboard
```
redis_db_keys
node_cpu_seconds_total
x enable autocomplete
```

9 - Grafana
```
helm install grafana
# commands
```

10 - Data Source
http://monitor-prometheus-server