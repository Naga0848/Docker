#### Steps to be followed while deleting the cluster

    Step-by-Step Cleanup
    1. Delete Kubernetes Resources First
    Before deleting the cluster, ensure workloads are removed:

    bash
    kubectl delete all --all --namespace=default
    kubectl delete pvc --all
    kubectl delete svc --all


    2. Delete Node Groups
    If you used managed node groups:

    bash
    eksctl delete nodegroup --cluster <cluster-name> --name <nodegroup-name>


    3. Delete the EKS Cluster
    bash
    eksctl delete cluster --name <cluster-name>


    4. Manually Clean CloudFormation Stacks
    Go to AWS Console → CloudFormation

    Look for stacks like:

    eksctl-<cluster-name>-cluster

    eksctl-<cluster-name>-nodegroup

    If status is DELETE_FAILED, check the Events tab for the resource blocking deletion.

    Manually delete that resource (e.g., security group, ENI, load balancer).

    Retry stack deletion.


    5. Remove Orphaned Resources
    Security Groups:

    aws ec2 describe-security-groups --filters Name=group-name,Values=*eks*
    aws ec2 delete-security-group --group-id <id>
    Load Balancers:

    aws elbv2 describe-load-balancers
    aws elbv2 delete-load-balancer --load-balancer-arn <arn>
    Volumes:

    aws ec2 describe-volumes --filters Name=tag:Name,Values=*eks*
    aws ec2 delete-volume --volume-id <id>
    6. IAM Cleanup
    Delete IAM roles/policies created for the cluster if they’re not in use:


    aws iam delete-role --role-name <eks-role>
    aws iam delete-policy --policy-arn <arn>


#### Commands

        aws cloudformation list-stacks --stack-status-filter CREATE_COMPLETE UPDATE_COMPLETE ROLLBACK_COMPLETE DELETE_FAILED  >>> command to see if any stacks are present in ClodFormation

        aws ec2 describe-vpcs --filters Name=tag:eksctl.cluster.k8s.io/v1alpha1/cluster-name,Values=<cluster-name>   >>> Command to see any VPCs are present

        eksctl create cluster --config-file=eks.yaml   >>> create cluster

        eksctl delete cluster --config-file=eks.yaml   >>> delete cluster

