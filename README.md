# kaiburr task related urls
[jenkins url](http://18.206.225.123:8080/)\

[hackathon-starter application](http://a4ae8868c0af54f27b54fa7ad54d3a23-1069947587.us-east-1.elb.amazonaws.com:8080/)\

[argocd url](https://a9d4ff07fa1d548ed942938465f30564-152749685.us-east-1.elb.amazonaws.com)\


##jenkins pipeline stages 
[jenkins ul](http://18.206.225.123:8080/job/devops-multibranch/)
 
![image](https://user-images.githubusercontent.com/87360254/229893905-56b416d0-9b7a-40fa-8fe2-b8de9e8930d2.png)

##terraform folder
under terraform folder - to launcg ec2 instances and ansible playbook to install mongodb 

before running below terraform commands, aws configure has to setup. terraform launch ec2 instance and copy mongodb playbooks to ec2 instance and install mongodb through ansilbe playbooks
```shell
git clone https://github.com/Prasanthcharan/kaiburr.git
cd terraform
terraform init
terraform plan
terraform apply
```

##k8s cluster setup

eks-cluster.yaml file is used to launch eks cluster and nake sure eksctl installed from where you run below commands

```shell
eksctl create cluster -f eks-cluster.yaml
```
referlinks:
https://eksctl.io/usage/creating-and-managing-clusters/

##helm folder

helm chart for application hackathon-starter

##argocd setup

refered links: https://archive.eksworkshop.com/intermediate/290_argocd/install/


```shell
kubectl create namespace argocd
kubectl apply -n argocd -f https://raw.githubusercontent.com/argoproj/argo-cd/v2.4.7/manifests/install.yaml
sudo curl --silent --location -o /usr/local/bin/argocd https://github.com/argoproj/argo-cd/releases/download/v2.4.7/argocd-linux-amd64

sudo chmod +x /usr/local/bin/argocd
kubectl patch svc argocd-server -n argocd -p '{"spec": {"type": "LoadBalancer"}}'
export ARGOCD_SERVER=`kubectl get svc argocd-server -n argocd -o json | jq --raw-output '.status.loadBalancer.ingress[0].hostname'`
export ARGO_PWD=`kubectl -n argocd get secret argocd-initial-admin-secret -o jsonpath="{.data.password}" | base64 -d`
argocd login $ARGOCD_SERVER --username admin --password $ARGO_PWD --insecure
```



