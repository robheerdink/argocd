#### Commands

Based on tutorial
* https://www.youtube.com/watch?v=MeU5_k9ssrs

* Config repo: [https://gitlab.com/nanuchi/argocd-app-config](https://gitlab.com/nanuchi/argocd-app-config)

* Docker repo: [https://hub.docker.com/repository/docker/nanajanashia/argocd-app](https://hub.docker.com/repository/docker/nanajanashia/argocd-app)

* Install ArgoCD: [https://argo-cd.readthedocs.io/en/stable/getting_started/#1-install-argo-cd](https://argo-cd.readthedocs.io/en/stable/getting_started/#1-install-argo-cd)

* Login to ArgoCD: [https://argo-cd.readthedocs.io/en/stable/getting_started/#4-login-using-the-cli](https://argo-cd.readthedocs.io/en/stable/getting_started/#4-login-using-the-cli)

* ArgoCD Configuration: [https://argo-cd.readthedocs.io/en/stable/operator-manual/declarative-setup/](https://argo-cd.readthedocs.io/en/stable/operator-manual/declarative-setup/)


Moved to own repos:\
github repo: robheerdink/argocd.git\
docker repo: https://hub.docker.com/repository/docker/robheerdink


```
# start minikube
minikube status
minikube start

# check if you on minkube cluster
kubectx 

# if you dont have kubectx isntalled
kubectl get overview
kctx <cluster>
kctx minikube
kctx xcmc
kctx gke_xite-cms_europe-west1-d_xitecms-gke-eu01

# create ns argocd in cluster and install argocd in cluster
kubectl create namespace argocd
kubectl apply -n argocd -f https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/install.yaml

# check for services in argocd namespace
kubectl get svc -n argocd

# password is stored as secret in cluster
kubectl -n argocd get secret argocd-initial-admin-secret -o jsonpath="{.data.password}" | base64 --decode && echo

# portforward argocd 443 to local port 880
kubectl port-forward svc/argocd-server 8080:443 -n argocd
browser: localhost:8080
admin / <pasw<>

# add the application to argocd 
# only time we would use kubectl apply, after this everythig should be auto synced)
kubectl apply -f application.yaml

```

# change argocd password
```
# download argoccd cli
curl -sSL -o argocd-linux-amd64 https://github.com/argoproj/argo-cd/releases/latest/download/argocd-linux-amd64
sudo install -m 555 argocd-linux-amd64 /usr/local/bin/argocd
rm argocd-linux-amd64

# create pasword token
argocd account bcrypt --password <YOUR-PASSWORD-HERE>

# apply password
kubectl -n argocd patch secret argocd-secret \
  -p '{"stringData": {
    "admin.password": "<THE RETURNED PASSWORD TOKEN>",
    "admin.passwordMtime": "'$(date +%FT%T%Z)'"
  }}'
```

# Docker example
```
# login to personal docker repo
docker login
robheerdink/<pasw>

# build example app image, tag and push it to docker-hub (repo/image:version)
docker build -f dockerfile-simple -t robheerdink/example:1.0 .
docker push robheerdink/example:1.0 

# retag to see if the argocd sync works
docker tag <imageid> robheerdink/nginx-example:1.1
docker push robheerdink/example:1.1 

# or test with ngninx hello world
docker build -t robheerdink/nginx-example:1.0 .
docker push robheerdink/nginx-example:1.0 
```
