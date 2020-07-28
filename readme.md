### Create EKS cluster

* Install Kubectl, eksctl cli
* create key pair with name - eks-course
* eksctl create cluster -f cluster-config.yaml
* eksctl get nodegroup -f cluster-config.yaml --include ng-mixed // Spot instances
* eksctl get nodegroup ng-mixed --cluster EKS-course-cluster
* eksctl scale nodegroup ng-1 --nodes=3 --nodes-max=5 --nodes-min=3 --cluster EKS-course-cluster
* Cluster Auto Scaler - https://docs.aws.amazon.com/eks/latest/userguide/cluster-autoscaler.html
* 