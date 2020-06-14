# EKSWordPress
Instructions:
1. Create a stack using RoleVPC.yaml
2. Create a stack using Cluster.yaml
3. Install awscli
4. Configure awscli AccessKeyID and Secret Access Key
5. Install kubectl
6. Run command with proper region code like eu-west-1 `aws eks --region region_code update-kubeconfig --name cluster_name`
7. Check if you have access to the cluster using the command `kubectl get svc`
8. Create a key pair in AWS Management Console or AWSCLI for SSH Access for the Worker Node Group that you will create in the next step
9. Create a stack using NodeGroup.yaml
10. Check if the nodes are active using the command `kubectl get nodes`
11. Replace only `<ARN of instance role (not instance profile)>` with the proper ARN and run `kubectl apply -f aws-auth-cm.yaml`
12. Run command `kubectl get nodes --watch` and when all your nodes reach `Ready` status you can terminate command with `Ctrl+C`
13. Run command `kubectl apply -k .\` for wordpress deployment
14. Run command `kubectl get pods --watch` and when all you pods reach `Running` status you can terminate command with `Ctrl+C`
15. Wait for 5 minutes in order for the DNS record to be updated and instances to be up and running.
16. Run command `kubectl get services wordpress` and you will see the external ip so copy and paste into your browser
17. Voila! You are ready to set wordpress.
