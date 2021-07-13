An eksctl-managed cluster can be upgraded in 3 easy steps:

upgrade control plane version with eksctl upgrade cluster
update default add-ons:
kube-proxy
aws-node
coredns
replace each of the nodegroups by creating a new one and deleting the old one
Pre-Requisites
Create Cluster with 1.18 or 1.19
Upgrade control plane using below command
eksctl upgrade cluster --name eksdemo  --region us-east-1 --version=1.19 --approve
Check components of cluster under kube-system namespace
kubectl get pods -n kube-system
image

Need to upgrade above components kube-proxy, aws-node, coredns

Write Utils in kube-config file with latest version of cluster
eksctl utils write-kubeconfig --cluster eksdemo --region us-east-1
Upgrade kube-proxy
eksctl utils update-kube-proxy --cluster eksdemo --region us-east-1 --approve
Upgrade aws-node
eksctl utils update-aws-node --cluster eksdemo --region us-east-1 --approve
Upgrade coredns
eksctl utils update-coredns --cluster eksdemo --region us-east-1 --approve
Next need to be done node-group upgradation
eksctl get nodegroups --cluster eksdemo --region us-east-1
Create new node-group by editing cluster.yml file like as cluster-update-nodegroups.yml
Process-I

eksctl create nodegroup -f cluster.yml
eksctl drain nodegroup --cluster eksdemo --name=workers-1-18 --region us-east-1
eksctl delete nodegroup --only-missing -f cluster.yml --approve
Process-II

eksctl upgrade nodegroup --name=workers --cluster eksdemo --kubernetes-version=1.19 --region us-east-1
