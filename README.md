# EKSWordPress
### Instructions:
1. Create an AWS user and attach policy named "AdministratorAccess" to the user.
2. Login with the new created user.
3. Create an Access Key through IAM for awscli usage.
4. Install awscli
5. Configure awscli using AccessKeyID and Secret Access Key using `aws configure`
6. Install kubectl and download aws-auth-cm.yaml, kustomization.yaml and wordpress-deployment.yaml in the same directory with kubectl.exe
7. Create a stack using RoleVPC.yaml and copy the stack name for usage in the next template.
8. Create a stack using Cluster.yaml by selecting ControlPlaneSecurityGroup as SecurityIds and Subnet01,Subnet02,Subnet03 for Subnets and copy the cluster name for usage in the next template.
9. After the stack creation is complete, run command with replacing <cluster_name> with you cluster name and <region_code> with your region code i.e. eu-west-1  
`aws eks --region <region_code> update-kubeconfig --name <cluster_name>`
10. Check if you have access to the cluster using the command `kubectl get svc`  
Output:  
```sh
NAME         TYPE        CLUSTER-IP   EXTERNAL-IP   PORT(S)   AGE  
kubernetes   ClusterIP   10.100.0.1   <none>        443/TCP   9m1s
```
11. Create a key pair in AWS Management Console or AWSCLI for SSH Access for the Worker Node Group that you will create in the next step
12. Create a stack using NodeGroup.yaml by selecting ControlPlaneSecurityGroup as ClusterControlPlaneSecurityGroup, SSH Key Pair as KeyName, RoleVPC stack's VPC as VpcId and all the subnets. Don't forget to change kubernetes version to cluster's version in NodeImageIdSSMParam. Copy NodeSecurityGroupName value under CloudFormation Outputs for usage in the next template
13. After the stack creation is complete,copy NodeInstanceRole value under CloudFormation Outputs and replace with <ARN of instance role (not instance profile)> inside aws-auth-cm.yaml and run  
`kubectl apply -f aws-auth-cm.yaml`
14. Check if the nodes are active using  
`kubectl get nodes --watch`  
Example Output:  
```sh
NAME                                            STATUS     ROLES    AGE   VERSION  
ip-192-168-113-161.eu-west-1.compute.internal   NotReady   <none>   19s   v1.16.8-eks-e16311  
ip-192-168-183-103.eu-west-1.compute.internal   NotReady   <none>   16s   v1.16.8-eks-e16311  
ip-192-168-250-87.eu-west-1.compute.internal    NotReady   <none>   16s   v1.16.8-eks-e16311  
ip-192-168-113-161.eu-west-1.compute.internal   Ready      <none>   20s   v1.16.8-eks-e16311  
ip-192-168-113-161.eu-west-1.compute.internal   Ready      <none>   20s   v1.16.8-eks-e16311  
ip-192-168-183-103.eu-west-1.compute.internal   Ready      <none>   20s   v1.16.8-eks-e16311  
ip-192-168-183-103.eu-west-1.compute.internal   Ready      <none>   20s   v1.16.8-eks-e16311  
ip-192-168-250-87.eu-west-1.compute.internal    Ready      <none>   20s   v1.16.8-eks-e16311  
ip-192-168-250-87.eu-west-1.compute.internal    Ready      <none>   20s   v1.16.8-eks-e16311
```
and when all your nodes reach `Ready` status you can terminate command with `Ctrl+C`

15. Create a stack using RDS.yaml with the value of the NodeSecurityGroupName from EC2 Security Groups as EC2SecurityGroup.
16. After the stack creation is complete,copy EndpointAddress value under CloudFormation Outputs and replace with <db_host> in wordpress-deployment.yaml and replace <password> in kustomization.yaml with your RDS password you set in previous stack.
15. Run command `kubectl apply -k .\` for wordpress deployment
16. Run command `kubectl get pods --watch` and when all you pods reach `Running` status you can terminate command with `Ctrl+C`
17. Wait for some minutes (at least 5) in order for the DNS record to be updated and instances to be up and running.
18. Run command `kubectl get services wordpress` and you will see the external ip so copy and paste into your browser
19. Voila! You are ready to set wordpress.
