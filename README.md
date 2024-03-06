# eks

# create cluster
eksctl create cluster --name px-npe2301 --nodegroup-name ng-default --node-type t3.micro --nodes 3

# setting up observability ( configuring fluentbit to send logs of application containers to cloudwatch )

# To install Fluent Bit to send logs from containers to CloudWatch Logs
If you don't already have a namespace called amazon-cloudwatch, create one by entering the following command:

kubectl apply -f https://raw.githubusercontent.com/aws-samples/amazon-cloudwatch-container-insights/latest/k8s-deployment-manifest-templates/deployment-mode/daemonset/container-insights-monitoring/cloudwatch-namespace.yaml


Run the following command to create a ConfigMap named cluster-info with the cluster name and the Region to send logs to. Replace cluster-name and cluster-region with your cluster's name and Region.

ClusterName=px-npe2301
RegionName=us-east-1
FluentBitHttpPort='2020'
FluentBitReadFromHead='Off'
[[ ${FluentBitReadFromHead} = 'On' ]] && FluentBitReadFromTail='Off'|| FluentBitReadFromTail='On'
[[ -z ${FluentBitHttpPort} ]] && FluentBitHttpServer='Off' || FluentBitHttpServer='On'
kubectl create configmap fluent-bit-cluster-info \
--from-literal=cluster.name=${ClusterName} \
--from-literal=http.server=${FluentBitHttpServer} \
--from-literal=http.port=${FluentBitHttpPort} \
--from-literal=read.head=${FluentBitReadFromHead} \
--from-literal=read.tail=${FluentBitReadFromTail} \
--from-literal=logs.region=${RegionName} -n amazon-cloudwatch



kubectl get sa -n amazon-cloudwatch

kubectl describe sa fluent-bit -n amazon-cloudwatch

# To determine OIDC IssuerID
cluster_name=px-npe2301
oidc_id=$(aws eks describe-cluster --name $cluster_name --query "cluster.identity.oidc.issuer" --output text | cut -d '/' -f 5)

echo $oidc_id

aws iam list-open-id-connect-providers | grep $oidc_id | cut -d "/" -f4


eksctl utils associate-iam-oidc-provider --region us-east-1 --cluster px-npe2301 --approve

eksctl create iamserviceaccount --cluster px-npe2301 --namespace amazon-cloudwatch --name fluent-bit --attach-policy-arn arn:aws:iam::aws:policy/CloudWatchAgentServerPolicy --override-existing-serviceaccounts --approve


eksctl delete cluster --name px-npe2301













eksctl create cluster --name px-npe2301 --nodegroup-name ng-default --node-type t3.micro --nodes 3

eksctl create cluster --name px-npe2301 --version 1.29 --nodegroup-name ng-default --node-type t3.micro --nodes 4 --managed

eksctl upgrade nodegroup --name=ng-default --cluster=px-npe2302

eksctl delete nodegroup --cluster px-npe2302 --name ng-default


helm version

helm search repo

helm search repo nginx

helm search hub nginx

helm repo add bitnami https://charts.bitnami.com/bitnami

helm pull bitnami/nginx --untar=true
