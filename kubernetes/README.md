# 🚀 Deploying Application on EKS with Ingress & Route53 (GoDaddy Domain Integration)

This guide explains how to connect an EC2 instance to your EKS cluster, deploy services, and configure a custom domain using Route53 with a GoDaddy domain.

---

## 📌 Steps to Connect EC2 to EKS Cluster using AWS CLI and kubectl

✅**Step 1: Download and Install AWS CLI**

Follow the instructions to download and install the AWS CLI from the [official AWS documentation](https://docs.aws.amazon.com/cli/latest/userguide/getting-started-install.html).

✅**Step 2: Configure AWS CLI**

Configure the AWS CLI with your credentials:

```sh
aws configure
```

You will be prompted to enter your AWS Access Key ID, Secret Access Key, region, and output format.

✅**Step 3: Update kubeconfig with EKS Cluster**

Use the following command to update your kubeconfig file with the EKS cluster:

```sh
aws eks --region <your-region> update-kubeconfig --name <your-cluster-name>
```

Replace `<your-region>` with the AWS region where your EKS cluster is located and `<your-cluster-name>` with the name of your EKS cluster.

<img width="1365" height="143" alt="image" src="https://github.com/user-attachments/assets/5175d698-9579-4f97-9ea4-76f1c261471c" />


## You should see a list of nodes in your EKS cluster.

✅**Step 1 To check the latest cluster:**
   ```bash
   kubectl config current-context
   ```
<img width="859" height="192" alt="image" src="https://github.com/user-attachments/assets/20160844-3ab0-4b45-81a2-f1aa36ef6b6b" />

✅**Step 2 Get the list of nodes:**
   ```bash
   kubectl get nodes
   ```
<img width="1085" height="112" alt="image" src="https://github.com/user-attachments/assets/b4a084ad-11f4-4d59-9aba-0bdb207d0a2f" />

✅**Step 3 Clone the GitHub repository and change the directory to it:**
   ```bash
   git clone https://github.com/I-am-nk/ultimate-devops-project-demo.git
   cd ultimate-devops-project-demo/kubernetes
   ```

✅**Step 4 Create the service account first:**
   ```bash
   kubectl apply -f serviceaccount.yaml
   ```
<img width="1239" height="190" alt="image" src="https://github.com/user-attachments/assets/0d134638-6a02-4cf4-8998-2c1fccac4f81" />

✅**Step 5 We have created a complete deployment and service file, so instead of running all services one by one, we are executing them at once:**
   ```bash
   kubectl apply -f complete-deploy.yaml
   ```


✅**Step 6 Check if all resources are created properly (Deployments, Services, Pods).**

✅**Step 7 Get the list of pods:**
   ```bash
   kubectl get pods
   ```
<img width="1088" height="562" alt="image" src="https://github.com/user-attachments/assets/e95e60ef-1f23-40d0-b826-fc5697e44fa7" />

✅**Step 8 Get the list of services:**
   ```bash
   kubectl get svc
   ```
<img width="1316" height="500" alt="image" src="https://github.com/user-attachments/assets/7660d5c9-7e31-435d-8972-d0b332e8279a" />

✅**Step 9 Get the deployments:**
   ```bash
    kubectl get deployments
  ```
<img width="1165" height="819" alt="image" src="https://github.com/user-attachments/assets/cc07ed77-7d55-4eb6-8e19-b50268509e43" />

✅**Step 10 Change the service type from **ClusterIP** to **LoadBalancer**:**
   ```bash
    kubectl edit svc opentelemetry-demo-frontendproxy
  ```
<img width="1819" height="146" alt="image" src="https://github.com/user-attachments/assets/553c534c-38b7-4c0d-9dc0-7d00a97dd0a8" />

✅**Step 11 Verify whether the LoadBalancer is created in your AWS account.**
<img width="1917" height="446" alt="image" src="https://github.com/user-attachments/assets/b749cc93-cf16-479f-86e1-fc5a23127645" />

✅**Step 12 You can access the application with the LoadBalancer, but we will configure **Ingress** for better features.**
<img width="1919" height="962" alt="image" src="https://github.com/user-attachments/assets/508f5c62-1a1c-45e7-96d5-4ebf65a67ea0" />

---

## 🌍 Ingress Setup
We Need to do some configueration to run the ingress like ingress controller and other resources.

# How to setup alb add on

##  Setup OIDC Connector

✅ **Step 1 commands to configure IAM OIDC provider**

```
export cluster_name=demo-cluster
```

```
oidc_id=$(aws eks describe-cluster --name $cluster_name --query "cluster.identity.oidc.issuer" --output text | cut -d '/' -f 5) 
```

✅ **Step 2 Check if there is an IAM OIDC provider configured already**

- aws iam list-open-id-connect-providers | grep $oidc_id | cut -d "/" -f4\n 

If not, run the below command

```
eksctl utils associate-iam-oidc-provider --cluster $cluster_name --approve
```
<img width="1349" height="113" alt="image" src="https://github.com/user-attachments/assets/9eb98d9e-91d1-4557-9247-120243dbabfa" />

✅ **Step 3 Download IAM policy**

```
curl -O https://raw.githubusercontent.com/kubernetes-sigs/aws-load-balancer-controller/v2.11.0/docs/install/iam_policy.json
```

✅ **Step 4 Create IAM Policy**

```
aws iam create-policy \
    --policy-name AWSLoadBalancerControllerIAMPolicy \
    --policy-document file://iam_policy.json
```

✅ **Step 5 Create IAM Role**

```
eksctl create iamserviceaccount \
  --cluster=<your-cluster-name> \
  --namespace=kube-system \
  --name=aws-load-balancer-controller \
  --role-name AmazonEKSLoadBalancerControllerRole \
  --attach-policy-arn=arn:aws:iam::<your-aws-account-id>:policy/AWSLoadBalancerControllerIAMPolicy \
  --approve
```

## Deploy ALB controller

✅ **Step 6 Add helm repo**

```
helm repo add eks https://aws.github.io/eks-charts
```

✅ **Step 7 Update the repo**

```
helm repo update eks
```

✅ **Step 8 Install**

```
helm install aws-load-balancer-controller eks/aws-load-balancer-controller \            
  -n kube-system \
  --set clusterName=<your-cluster-name> \
  --set serviceAccount.create=false \
  --set serviceAccount.name=aws-load-balancer-controller \
  --set region=<region> \
  --set vpcId=<your-vpc-id>
```
<img width="1892" height="396" alt="image" src="https://github.com/user-attachments/assets/209743f8-8f54-41c6-9794-213b6b6545ce" />

✅ **Step 9 Verify that the deployments are running.**

```
kubectl get deployment -n kube-system aws-load-balancer-controller
```

You might face the issue, unable to see the loadbalancer address while giving k get ing -n robot-shop at the end. To avoid this your **AWSLoadBalancerControllerIAMPolicy** should have the required permissions for elasticloadbalancing:DescribeListenerAttributes.

✅ **Step 10 Run the following command to retrieve the policy details and look for **elasticloadbalancing:DescribeListenerAttributes** in the policy document.**
```
aws iam get-policy-version \
    --policy-arn arn:aws:iam::<your-aws-account-id>:policy/AWSLoadBalancerControllerIAMPolicy \
    --version-id $(aws iam get-policy --policy-arn arn:aws:iam::<your-aws-account-id>:policy/AWSLoadBalancerControllerIAMPolicy --query 'Policy.DefaultVersionId' --output text)
```

✅**Step 11 If the required permission is missing, update the policy to include it Download the current policy**
```
aws iam get-policy-version \
    --policy-arn arn:aws:iam::<your-aws-account-id>:policy/AWSLoadBalancerControllerIAMPolicy \
    --version-id $(aws iam get-policy --policy-arn arn:aws:iam::<your-aws-account-id>:policy/AWSLoadBalancerControllerIAMPolicy --query 'Policy.DefaultVersionId' --output text) \
    --query 'PolicyVersion.Document' --output json > policy.json
```
✅ **Step 12 Edit policy.json to add the missing permissions**
```
{
  "Effect": "Allow",
  "Action": "elasticloadbalancing:DescribeListenerAttributes",
  "Resource": "*"
}
```
✅ **Step 13 Create a new policy version**
```
aws iam create-policy-version \
    --policy-arn arn:aws:iam::<your-aws-account-id>:policy/AWSLoadBalancerControllerIAMPolicy \
    --policy-document file://policy.json \
    --set-as-default
```

✅**Step 14. Apply the ingress configuration:**

  ```bash
    kubectl apply -f ingress.yaml
  ```

✅**Step 15. Route the traffic of your application to your own domain using **Route53**.**

---

## 🔑 Route53 & GoDaddy Setup

✅ **Step 1: Create Hosted Zone in Route53**  
- Go to **AWS Console → Route53**.  
- Click **Hosted Zones → Create Hosted Zone**.  
- Enter your domain name (e.g., `iamnkdevopseng.shop`).  
- Type: **Public Hosted Zone**.  
  - This will create **Name Servers (NS records)**.

---

✅ **Step 2: Update Domain Name Servers in GoDaddy**  
- Go to **GoDaddy → My Products → DNS Management** for your domain.  
- Replace the existing **Nameservers** with the **Route53 NS records** from Step 1.  
- Save → It may take **5–30 minutes** (sometimes up to 24 hrs) to propagate.  

---

✅ **Step 3: Create DNS Record in Route53**  
- Open your hosted zone in **Route53**.  
- Click **Create Record → A Record**.  
- Name: `www` (so it becomes `www.iamnkdevopseng.shop`).  
  - **Alias: Yes**  
  - **Alias target: Choose the LoadBalancer DNS** of your Ingress Controller (Nginx or ALB).  
- Save the record.  

---

✅ **Step 5: Test the Setup**  
Run:
```bash
nslookup www.iamnkdevopseng.shop
```
It should resolve to the **AWS LoadBalancer IP/hostname**.

---

## 🎉 Final Verification

✅ Open your browser and go to:  
👉 [www.iamnkdevopseng.shop](www.iamnkdevopseng.shop)  

<img width="1917" height="977" alt="image" src="https://github.com/user-attachments/assets/2533d662-2f0e-40f9-9647-cd16867485b5" />

✅  🎊 Boom! You have successfully implemented the project.  

---

