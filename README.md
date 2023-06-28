# kubernetes-twemproxy

Running twemproxy on Kubernetes

## Install redis

Setup 2 instances of redis from the gke console that we will add in the twemproxy configuration

## Install twemproxy

Build the docker image for twemproxy from `twemproxy` folder.

Edit twemproxy configuration

```
alpha:
  listen: 0.0.0.0:22121
  hash: murmur
  distribution: ketama
  auto_eject_hosts: true
  redis: true
  server_connections: 1
  server_retry_timeout: 30000
  timeout: 4000
  server_failure_limit: 3
  preconnect: true
  servers:
    - 10.108.65.147:6379:1
    - 10.108.65.155:6379:1
    # add more servers here
```

Create the configmap, deployment and service for twemproxy

```
docker build -t asia.gcr.io/airasia-hotelsbe-prd/twemproxy:twemproxy-2.0.1 .
gcloud auth configure-docker
docker push asia.gcr.io/airasia-hotelsbe-prd/twemproxy:twemproxy-2.0.1

kubectl create configmap twemproxy-conf --from-file=twemproxy.yaml
kubectl create -f deployments/twemproxy.yaml
kubectl create -f services/twemproxy.yaml
```

## Check the installation

Fire up an Ubuntu container and check if twemproxy is working as expected.

```
kubectl run -i --tty ubuntu --image=ubuntu --restart=Never /bin/bash
apt-get update && apt-get install -y redis-tools
apt-get update
apt-get install iputils-ping
redis-cli -h twemproxy -p 22121
```

## twemproxy redis cluster ref
```
https://github.com/twitter/twemproxy
```
