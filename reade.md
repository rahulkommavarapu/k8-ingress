# Ingress Controller
1.
REGION_CODE=us-east-1
CLUSTER_NAME=expense
ACC_ID=631164543894

### Permissions
2.
* OIDC provider
```
eksctl utils associate-iam-oidc-provider \
    --region $REGION_CODE \
    --cluster $CLUSTER_NAME \
    --approve
`3.``
* IAM Policy
```
curl -o iam-policy.json https://raw.githubusercontent.com/kubernetes-sigs/aws-load-balancer-controller/v2.12.0/docs/install/iam_policy.json
```
4.* Create IAM Policy
```
aws iam create-policy \
    --policy-name AWSLoadBalancerControllerIAMPolicy \
    --policy-document file://iam-policy.json
```
5.* Provide access to EKS through IAM Policy
```
eksctl create iamserviceaccount \
--cluster=$CLUSTER_NAME \
--namespace=kube-system \
--name=aws-load-balancer-controller \
--attach-policy-arn=arn:aws:iam::$ACC_ID:policy/AWSLoadBalancerControllerIAMPolicy \
--override-existing-serviceaccounts \
--region $REGION_CODE \
--approve
```
6.
### Install Drivers
* Add the EKS chart repo to Helm
```
helm repo add eks https://aws.github.io/eks-charts
```
7.
* Install AWS Load Balancer Controller
```
helm install aws-load-balancer-controller eks/aws-load-balancer-controller -n kube-system --set clusterName=$CLUSTER_NAME --set serviceAccount.create=false --set serviceAccount.name=aws-load-balancer-controller
```