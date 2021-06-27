### Create EKS cluster with Fargate
eksctl create cluster --name my-cluster --version 1.16 --fargate


### Create an IAM OIDC provider and associate it with your cluster
eksctl utils associate-iam-oidc-provider \
    --region us-east-2 \
    --cluster poc-cluster \
    --approve
    
### policy - pod that allows it to make calls to AWS APIs on your behalf.    
curl -o iam-policy.json https://raw.githubusercontent.com/kubernetes-sigs/aws-alb-ingress-controller/v1.1.8/docs/examples/iam-policy.json

aws iam create-policy \
    --policy-name ALBIngressControllerIAMPolicy \
    --policy-document file://iam-policy.json


 arn:aws:iam::839927957243:policy/ALBIngressControllerIAMPolicy
 
### Configure kubectl
aws eks --region us-east-2 update-kubeconfig --name my-cluster
 
### Create Kubernetes Service account

kubectl apply -f https://raw.githubusercontent.com/kubernetes-sigs/aws-alb-ingress-controller/v1.1.8/docs/examples/rbac-role.yaml

### Create an IAM role for the ALB Ingress Controller and attach the role to the service account
eksctl create iamserviceaccount \
    --region us-east-2 \
    --name alb-ingress-controller \
    --namespace kube-system \
    --cluster poc-cluster \
    --attach-policy-arn arn:aws:iam::839927957243:policy/ALBIngressControllerIAMPolicy \
    --override-existing-serviceaccounts \
    --approve
    
### Deploy Ingress
kubectl apply -f https://raw.githubusercontent.com/kubernetes-sigs/aws-alb-ingress-controller/v1.1.8/docs/examples/alb-ingress-controller.yaml


kubectl apply -f https://raw.githubusercontent.com/kubernetes-sigs/aws-alb-ingress-controller/v1.1.8/docs/examples/2048/2048-namespace.yaml
kubectl apply -f https://raw.githubusercontent.com/kubernetes-sigs/aws-alb-ingress-controller/v1.1.8/docs/examples/2048/2048-deployment.yaml
kubectl apply -f https://raw.githubusercontent.com/kubernetes-sigs/aws-alb-ingress-controller/v1.1.8/docs/examples/2048/2048-service.yaml

curl -o 2048-ingress.yaml https://raw.githubusercontent.com/kubernetes-sigs/aws-alb-ingress-controller/v1.1.8/docs/examples/2048/2048-ingress.yaml

eksctl create fargateprofile --cluster my-cluster --region us-east-2 --name alb-sample-app --namespace 2048-game

### Sentry

https://github.com/getsentry/onpremise

https://sentry.io/pricing/


