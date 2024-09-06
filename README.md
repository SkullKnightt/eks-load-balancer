### 1. Create an EKS Fargate Profile:
```powershell
eksctl create fargateprofile --cluster demo-cluster --region us-east-1 --name alb-sample-app --namespace game-2048
```

### 2. Associate IAM OIDC Provider:
```powershell
eksctl utils associate-iam-oidc-provider --cluster demo-cluster2 --approve
```

### 3. Download IAM Policy JSON:
```powershell
curl -O https://raw.githubusercontent.com/kubernetes-sigs/aws-load-balancer-controller/v2.5.4/docs/install/iam_policy.json
```

### 4. Create IAM Policy for the Load Balancer Controller:
```powershell
aws iam create-policy --policy-name AWSLoadBalancerControllerIAMPolicy --policy-document file://iam_policy.json
```

### 5. Create IAM Service Account:
```powershell
eksctl create iamserviceaccount --cluster=demo-cluster2 --namespace=kube-system --name=aws-load-balancer-controller --role-name AmazonEKSLoadBalancerControllerRole --attach-policy-arn=arn:aws:iam::<your-aws-account-id>:policy/AWSLoadBalancerControllerIAMPolicy --approve
```
Replace `<your-aws-account-id>` with your actual AWS account ID.

### 6. Add Helm Repository for EKS:
```powershell
helm repo add eks https://aws.github.io/eks-charts
```

### 7. Update Helm Repositories:
```powershell
helm repo update
```

### 8. Install AWS Load Balancer Controller using Helm:
```powershell
helm install aws-load-balancer-controller eks/aws-load-balancer-controller -n kube-system --set clusterName=demo-cluster2 --set serviceAccount.create=false --set serviceAccount.name=aws-load-balancer-controller --set region=us-east-1 --set vpcId=vpc-0a9d6729f6def08e4
```

### 9. Apply Kubernetes Resources:
```powershell
kubectl apply -f https://raw.githubusercontent.com/kubernetes-sigs/aws-load-balancer-controller/v2.5.4/docs/examples/2048/2048_full.yaml
```


