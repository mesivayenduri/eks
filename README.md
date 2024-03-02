# eks

eksctl create cluster --name npe2301 --nodegroup-name ng-default --node-type t3.micro --nodes 2

eksctl create cluster --name px-npe2302 --version 1.14 --nodegroup-name ng-default --node-type t3.micro --nodes 4 --managed

eksctl upgrade nodegroup --name=ng-default --cluster=px-npe2302

eksctl delete nodegroup --cluster px-npe2302 --name ng-default


helm version

helm search repo

helm search repo nginx

helm search hub nginx

helm repo add bitnami https://charts.bitnami.com/bitnami

helm pull bitnami/nginx --untar=true
