## Overview
Forward proxy with nginx as container.  
Forward proxy container listens port 8443.

## Usage  
### 1. Build container image.
There are two options to build container image.  
  
(A) forward proxy listening HTTPS connection.(not verified)  
(B) forward proxy listening HTTP connection.
  
#### (A) forward proxy listening HTTPS connection (not verified)  
1. To make forward proxy container communicate by https, Create encryption related files `server.crt` and `server.key`.  
2. put these file on same level as `Dockerfile`.  
3. build container image with command below on same level as `Dockerfile`.  
```
$ docker build -t nginx-https-forward-proxy:1.0 ./
```

#### (B) forward proxy listening HTTP connection 
1. change directory to `http/Dockerfile`.  
2. build container image with command below.  
```
$ docker build -t nginx-http-forward-proxy:1.0 ./  
```
  
### 2. Deploy container
There are 2 options to deploy forward proxy container.  
(A) Deploy to Docker  
(B) Deploy to Kubernetes  
#### (A) Deploy to Docker
1. run forward proxy container with host port 8443.
```
# (A) forward proxy listening HTTPS connection
$ docker run -d -p 8443:443 $CONTAINER_IMAGE
# (B) forward proxy listening HTTP connection (not verified)
# docker run -d -p 8443:8443 $CONTAINER_IMAGE
```
2. execute curl and get web content through forward proxy.
```
$ curl --proxy-cacert --proxy https://localhost:8443 https://www.google.com
```

#### (B) Deploy to Kubernetes
Apply manifest file `kubernetes/deployment.yaml` and `kubernetes/service.yaml`.
```
$ kubectl apply -f kubernetes/deployment.yaml
$ kubectl apply -f kubernetes/service.yaml
```
Run some pod including `curl` command. (e.g. nginx:1.18.0)
Then, execute `curl` from the pod to forward proxy container as below.
```
# if self signed certificate is used, set --proxy-cacert option.
$ curl --proxy-cacert --proxy https://nginx-fp-deployment https://www.google.com
```
