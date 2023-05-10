### Enable kubectl to communicate with your cluster by adding a new context to the kubectl config file.
`
aws eks update-kubeconfig --region region-code --name my-cluster
`
### Create a cluster with aws cli
` 
eksctl create cluster --name my-cluster --region region-code --version 1.25 --vpcprivate-subnets subnet-ExampleID1,subnet-ExampleID2 --without-nodegroup`

or using config file example: cluster.yaml
### scale node group 
eksctl scale nodegroup --cluster=clusterName --nodes=desiredCount --name=nodegroupName

 


