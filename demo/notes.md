## AWS Fargate Deep Dive
* AWS Fargate, you no longer have to provision, configure, or scale groups of virtual machines to run containers.
* <a href="ttps://docs.aws.amazon.com/eks/latest/userguide/fargate-getting-started.html">AWS Fargate Documentation</a>

## Installation
* Install eksctl
``` bash
curl --silent --location "https://github.com/weaveworks/eksctl/releases/latest/download/eksctl_$(uname -s)_amd64.tar.gz" | tar xz -C /tmp

sudo mv /tmp/eksctl /usr/local/bin

eksctl version
# should be >= 0.26.0
```

## Create Cluster
``` bash
eksctl create cluster --name eks-demo --version 1.17 --fargate
```
* Advantages of creating cluster using eksctl
  * creates VPC, subnets, SG etc
  * automatic creation of pod execution role [pods need to make calls to AWS APIs on your behalf to do things like pull container images from Amazon ECR]
  * Create fargate profiles for default and kube-system namespaces
* Create fargate profile if you want 
``` bash
eksctl create fargateprofile --cluster eks-demo --name demo_profile --namespace admcoe

```

## Deploy  Ingress Controller and HTTPD server
``` bash
# Update kubeconfig and create namespace
aws eks --region us-east-2 update-kubeconfig --name eks-demo


# Create an IAM OIDC provider and associate it with your cluster
eksctl utils associate-iam-oidc-provider \
    --region us-east-2 \
    --cluster eks-demo \
    --approve

# Download an IAM policy for the ALB Ingress Controller pod that allows it to make calls to AWS APIs on your behalf. 
curl -o iam-policy.json https://raw.githubusercontent.com/kubernetes-sigs/aws-alb-ingress-controller/v1.1.8/docs/examples/iam-policy.json

aws iam create-policy \
    --policy-name ALBIngressControllerIAMPolicy \
    --policy-document file://iam-policy.json

# Create a Kubernetes service account named alb-ingress-controller in the kube-system namespace
kubectl apply -f https://raw.githubusercontent.com/kubernetes-sigs/aws-alb-ingress-controller/v1.1.8/docs/examples/rbac-role.yaml

eksctl create iamserviceaccount \
    --region us-east-2 \
    --name alb-ingress-controller \
    --namespace kube-system \
    --cluster eks-demo \
    --attach-policy-arn arn:aws:iam::839927957243:policy/ALBIngressControllerIAMPolicy \
    --override-existing-serviceaccounts \
    --approve

# Deploy Ingress controller
kubectl apply -f https://raw.githubusercontent.com/kubernetes-sigs/aws-alb-ingress-controller/v1.1.8/docs/examples/alb-ingress-controller.yaml

# Update cluster info
kubectl get deployments.apps -n kube-system alb-ingress-controller -o yaml > alb-ingress-controller-dep.yaml

kubectl apply -f alb-ingress-controller-dep.yaml -n kube-system


kubectl create ns admcoe
kubectl create deployment httpd --image=httpd -n admcoe
kubectl expose deployment -n admcoe httpd --port=80 --type=NodePort

```

## Persistent Data - EFS with EKS fargate
* <a href="https://aws.amazon.com/blogs/aws/new-aws-fargate-for-amazon-eks-now-supports-amazon-efs/">EFS EKS support</a>
* Create EFS and copy the id and use the value in efs_pv_pvc.yaml file
``` bash
kubectl apply -f efs_pv_pvc.yaml -n admcoe
kubectl apply -f httpd-dep.yaml -n admcoe
kubectl expose deployment -n admcoe httpd --port=80 --type=NodePort
kubectl apply -f ingress.yaml -n admcoe
```

## Metrics Server and Kubernetes Dashboard [ Monitoring solution]
* Metrics servers collects container memory, cpu usage. Kubernetes horizontal autoscalling feature depends on this
* <a href="https://github.com/kubernetes-sigs/metrics-server">Metrics Server</a>
* <a href="https://github.com/kubernetes/dashboard">Kubernetes Dashboard</a>
``` bash
kubectl apply -f https://github.com/kubernetes-sigs/metrics-server/releases/download/v0.3.7/components.yaml

eksctl create fargateprofile --cluster eks-demo --name k8s-dashboard --namespace kubernetes-dashboard

kubectl apply -f https://raw.githubusercontent.com/kubernetes/dashboard/v2.0.3/aio/deploy/recommended.yaml

<a href="https://github.com/kubernetes/dashboard/blob/master/docs/user/access-control/creating-sample-user.md">dashboard user</a>

kubectl describe secrets -n kubernetes-dashboard admin-user-token-4x7lr

# Copy the token

# Access dashboard
kubectl proxy
# http://localhost:8001/api/v1/namespaces/kubernetes-dashboard/services/https:kubernetes-dashboard:/proxy/


```


## IAM configuration
* aws sts get-caller-identity - see the current user
* create new user in IAM and copy the ARN
``` bash


# Get IAM authenticator configmap yaml to add new users and give access
kubectl get cm aws-auth -n kube-system -o yaml > aws-auth.yaml

# Create 2 new users eks-admcoe-read & eks-demo-admin and give programatic access

# configure aws cli profiles and provide access keys and secret keys
aws configure --profile eks-admcoe-read
aws configure --profile eks-demo-admin

# List all the profiles
aws configure list-profiles
aws sts get-caller-identity

# Create read role and role binding 
kubectl apply -f read-role.yaml
kubectl apply -f role-bind.yaml
export AWS_PROFILE=eks-demo-admin
```