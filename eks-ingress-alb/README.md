# EKS Ingress ALB Sample
# Author: Sai Siva Yenduri

# Create a Cluster
```
eksctl create cluster --name attractive-gopher --nodegroup-name ng-default --node-type m5.large --nodes 3
```

```
eksctl utils associate-iam-oidc-provider --cluster=attractive-gopher --approve
```

# Deploy AWS ALB Ingress Controller
```
kubectl apply -f https://raw.githubusercontent.com/kubernetes-sigs/aws-alb-ingress-controller/v1.1.4/docs/examples/rbac-role.yaml
```

```
aws iam create-policy --policy-name ALBIngressControllerIAMPolicy --policy-document https://raw.githubusercontent.com/kubernetes-sigs/aws-alb-ingress-controller/v1.1.4/docs/examples/iam-policy.json

If the above doesn't work try below

aws iam create-policy --policy-name ALBIngressControllerIAMPolicy --policy-document file://eks-ingress-alb/iam-policy.json
```

```
eksctl create iamserviceaccount \
       --cluster=attractive-gopher \
       --namespace=kube-system \
       --name=alb-ingress-controller \
       --attach-policy-arn=arn:aws:iam::771740916074:policy/ALBIngressControllerIAMPolicy \
       --override-existing-serviceaccounts \
       --approve
```

```
curl -sS "https://raw.githubusercontent.com/kubernetes-sigs/aws-alb-ingress-controller/v1.1.4/docs/examples/alb-ingress-controller.yaml" \
     | sed "s/# - --cluster-name=devCluster/- --cluster-name=attractive-gopher/g" \
     | kubectl apply -f -
```

```
kubectl logs -n kube-system $(kubectl get po -n kube-system | egrep -o alb-ingress[a-zA-Z0-9-]+)
```



kubectl apply -f https://raw.githubusercontent.com/kubernetes-sigs/aws-alb-ingress-controller/v1.1.4/docs/examples/2048/2048-namespace.yaml
kubectl apply -f https://raw.githubusercontent.com/kubernetes-sigs/aws-alb-ingress-controller/v1.1.4/docs/examples/2048/2048-deployment.yaml
kubectl apply -f https://raw.githubusercontent.com/kubernetes-sigs/aws-alb-ingress-controller/v1.1.4/docs/examples/2048/2048-service.yaml



kubectl apply -f https://raw.githubusercontent.com/kubernetes-sigs/aws-alb-ingress-controller/v1.1.4/docs/examples/2048/2048-ingress.yaml

