## Install Basics
* Start minikube - Kubernetes Cluster
* Install Helm based on your OS
* source <(helm completion bash)
* Add stable repo - helm repo add stable https://kubernetes-charts.storage.googleapis.com/
* List All the charts - helm search repo stable
* Get Latest Helm Charts - helm repo update
* Install mysql - helm install stable/mysql --generate-name
* Get info about chart - helm show chart stable/mysql 
* Get release info - helm ls
* Uninstall helm chart -  helm uninstall mysql-1595961385 --keep-history 
* helm status mysql-1595961385 - Good for Rollback
* helm search hub searches the Helm Hub, which comprises helm charts from dozens of different repositories
* helm search repo searches the repositories that you have added to your local helm client (with helm repo add). This search is done over local data, and no public network connection is needed
* helm search hub wordpress - search in all repo
* helm search repo wordpress - search in your local repo
* helm repo remove

## Install Wordpress
* helm install wp stable/wordpress
* helm status wp
* helm show values stable/wordpress - list all the configurable option
* helm show values stable/wordpress > config.yaml
* update config.yaml
* helm uninstall wp stable/wordpress
* helm install wp stable/wordpress -f config.yaml [ --values ]
* helm get values wp
* helm install wp stable/wordpress -f config.yaml --set wordpressBlogName="Akilan's Blog!" service.type="NodePort" - Overwrites the default values in the yaml file
* helm rollback wp 1

## Installation Methods
* A chart repository (as we've seen above)
* A local chart archive (helm install foo foo-0.1.1.tgz)
* An unpacked chart directory (helm install foo path/to/foo)
* A full URL (helm install foo https://example.com/charts/foo-1.2.3.tgz)*

## Create Helm package
* helm create my-web-app
* helm package my-web-app 
* helm install my-web-app ./my-web-app-0.1.0.tgz