Build, Deploy and Monitor on Kubernetes

Summary
Build a Docker image from a "hello world" web application, deploy it to a Kubernetes cluster and instrument monitoring.

Setup
Some requirements are left intentionally vague:
There must be a "hello world" type web application in your personal Github account. The language doesn't matter, e.g. ruby, python, java, etc. 
#SCHO using "Kubernetes Wordsmith Demo" to complete the setup portion of the exercise
You may run a Docker registry and Kubernetes on your localhost (see Docker Desktop).
There must be Docker image(s) built from a Dockerfile in the application source code.
#SCHO https://github.com/dockersamples/k8s-wordsmith-demo/blob/master/db/Dockerfile for the db image
#SCHO https://github.com/dockersamples/k8s-wordsmith-demo/blob/master/web/Dockerfile for the web image
#SCHO https://github.com/dockersamples/k8s-wordsmith-demo/blob/master/words/Dockerfile for the word image including mvn install
#SCHO installed Docker Desktop for Mac and enabled the kubernetes(v1.19.7) cluster 
There must be Kubernetes deployment, service and ingress definitions used to start the application with reference to the aforementioned Docker image(s).
#SCHO https://github.com/dockersamples/k8s-wordsmith-demo/blob/master/kube-deployment.yml
There must be CPU and RAM monitoring for the Kubernetes PODs.

Requirements
I must be able to view your application code, Dockerfile, Kubernetes manifests and monitoring configuration.
You must be able to deploy the application using kubectl commands
#SCHO
# Deploy Using a Kubernetes Manifest
#kubectl apply -f kube-deployment.yml 

You must be able to view application deployment status using kubectl commands
#SCHO 
#From my Mac terminal, samuelcho@Samuels-iMac k8s-wordsmith-demo %  kubectl get svc 
NAME         TYPE           CLUSTER-IP      EXTERNAL-IP   PORT(S)          AGE
db    3       ClusterIP      None            <none>        5432/TCP         8h
kubernetes   ClusterIP      10.96.0.1       <none>        443/TCP          33h
web          LoadBalancer   10.107.22.117   localhost     8081:32557/TCP   8h
words        ClusterIP      None            <none>        8080/TCP         8h
 
samuelcho@Samuels-iMac k8s-wordsmith-demo % kubectl get pods
NAME                     READY   STATUS    RESTARTS   AGE
db-68df76f5d6-mnpk2      1/1     Running   0          8h
web-6f496699bd-9rdxx     1/1     Running   0          8h
words-84bccbc5d7-2h56x   1/1     Running   0          8h
words-84bccbc5d7-5qz7k   1/1     Running   0          8h
words-84bccbc5d7-fks2p   1/1     Running   0          8h
words-84bccbc5d7-lc8v9   1/1     Running   0          8h
words-84bccbc5d7-w8ln5   1/1     Running   0          8h
 
You must be able to successfully load/display the application in a web browser
#SCHO http://localhost:8081
# localhost image <img width="1284" alt="Screen Shot 2021-05-18 at 6 51 22 PM" src="https://user-images.githubusercontent.com/84278162/118733331-15365c80-b80a-11eb-8a8c-89907ae9039c.png">

You must be able to view/display pod CPU and RAM usage over time, on a graph.
samuelcho@Samuels-iMac k8s-wordsmith-demo % kubectl apply -f https://raw.githubusercontent.com/kubernetes/dashboard/v2.0.0-rc3/aio/deploy/recommended.yaml

samuelcho@Samuels-iMac k8s-wordsmith-demo % kubectl get pods --all-namespaces
NAMESPACE              NAME                                         READY   STATUS    RESTARTS   AGE
default                db-68df76f5d6-mnpk2                          1/1     Running   0          8h
default                web-6f496699bd-9rdxx                         1/1     Running   0          8h
default                words-84bccbc5d7-2h56x                       1/1     Running   0          8h
default                words-84bccbc5d7-5qz7k                       1/1     Running   0          8h
default                words-84bccbc5d7-fks2p                       1/1     Running   0          8h
default                words-84bccbc5d7-lc8v9                       1/1     Running   0          8h
default                words-84bccbc5d7-w8ln5                       1/1     Running   0          8h
kube-system            coredns-f9fd979d6-mb2k4                      1/1     Running   0          33h
kube-system            coredns-f9fd979d6-zfjdl                      1/1     Running   0          33h
kube-system            etcd-docker-desktop                          1/1     Running   0          33h
kube-system            kube-apiserver-docker-desktop                1/1     Running   0          33h
kube-system            kube-controller-manager-docker-desktop       1/1     Running   0          33h
kube-system            kube-proxy-h4zds                             1/1     Running   0          33h
kube-system            kube-scheduler-docker-desktop                1/1     Running   0          33h
kube-system            storage-provisioner                          1/1     Running   0          33h
kube-system            vpnkit-controller                            1/1     Running   0          33h
kubernetes-dashboard   dashboard-metrics-scraper-74db988864-74bz5   1/1     Running   0          33h
kubernetes-dashboard   kubernetes-dashboard-84f46948d6-whfx8        1/1     Running   0          33h

samuelcho@Samuels-iMac k8s-wordsmith-demo % kubectl proxy
Starting to serve on 127.0.0.1:8001
#View your dashboard http://localhost:8001/api/v1/namespaces/kubernetes-dashboard/services/https:kubernetes-dashboard:/proxy/#/login
#if you want to get the cpu and the mem graph, you need to install Kubernetes metrics server and follow the below steps
samuelcho@Samuels-iMac k8s-wordsmith-demo % kubectl apply -f https://raw.githubusercontent.com/kubernetes/dashboard/v2.2.0/aio/deploy/recommended.yaml
namespace/kubernetes-dashboard unchanged
serviceaccount/kubernetes-dashboard unchanged
service/kubernetes-dashboard unchanged
secret/kubernetes-dashboard-certs unchanged
secret/kubernetes-dashboard-csrf unchanged
secret/kubernetes-dashboard-key-holder unchanged
configmap/kubernetes-dashboard-settings unchanged
role.rbac.authorization.k8s.io/kubernetes-dashboard unchanged
clusterrole.rbac.authorization.k8s.io/kubernetes-dashboard unchanged
rolebinding.rbac.authorization.k8s.io/kubernetes-dashboard unchanged
clusterrolebinding.rbac.authorization.k8s.io/kubernetes-dashboard unchanged
deployment.apps/kubernetes-dashboard configured
service/dashboard-metrics-scraper unchanged
deployment.apps/dashboard-metrics-scraper configured
samuelcho@Samuels-iMac k8s-wordsmith-demo % kubectl patch deployment kubernetes-dashboard -n kubernetes-dashboard --type 'json' -p '[{"op": "add", "path": "/spec/template/spec/containers/0/args/-", "value": "--enable-skip-login"}]'
deployment.apps/kubernetes-dashboard patched
samuelcho@Samuels-iMac k8s-wordsmith-demo % kubectl proxy
Starting to serve on 127.0.0.1:8001


Extra Credit
Host your Kubernetes cluster in AWS
#SCHO Used the repository SRE-AWS to build the Kubernetes cluster in my own AWS account,381775446117 

Host your Docker image on dockerhub.com
#SCHO created the dockerhub account, "scho20210516" and linked it with the Github account, "scho20210516"
#SCHO The local image was created by:
cd k8s-wordsmith-demo
docker-compose build

samuelcho@Samuels-iMac k8s-wordsmith-demo % docker image ls | grep dockersample
dockersamples/k8s-wordsmith-web      latest                                                  c1858c040bb0   2 years ago     11.1MB
dockersamples/k8s-wordsmith-db       latest                                                  1ec1b68f9d19   3 years ago     38.2MB
dockersamples/k8s-wordsmith-api      latest                                                  77b84213c1f6   3 years ago     87.5MB





Deploy your application with Helm
Deploy your infrastructure with Terraform
Install a Kubernetes Dashboard
Install APM in your application (monitoring)


Double Extra Credit
Enable us to load your application in our browser
Enable us to view pod CPU and RAM usage in New Relic
CD pipeline that builds and deploys on every commit/push


www

