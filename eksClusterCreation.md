# Creating an EKS Cluster in KodeKloud Labs

KodeKloud is an interactive platform that provides hands-on experience with technologies like Kubernetes and EKS, offering a safe environment for practice without managing infrastructure. This guide will help you create an EKS cluster in KodeKloud Labs.

## Important Notes:
- **Kubernetes Version**: 1.31
- **Availability Zone**: Do **not** select subnets from `us-east-1e` as it is restricted.
- **Terraform Method**: For automation using Terraform, refer to [this guide](https://github.com/kodekloudhub/certified-kubernetes-administrator-course/tree/master/managed-clusters/eks) by @Alistair_KodeKloud.

## Prerequisites:
- Basic understanding of AWS and Kubernetes.

## Step 1: Provision the AWS Cloud Lab
1. Click **START LAB** to initiate an AWS Cloud instance.
2. After a few seconds, you will receive credentials to access the AWS console.
   ![AWS Lab Start](https://res.cloudinary.com/dljvrtsnk/image/upload/v1714389164/qnmfarvvje6f9ab24zfz.png)
3. Log in using the provided credentials.
   ![Login Credentials](https://res.cloudinary.com/dljvrtsnk/image/upload/v1714389234/ywggrnull0swswqgvpzc.png)

## Step 2: Create the EKS Cluster
Follow these steps or refer to the [AWS EKS Documentation](https://docs.aws.amazon.com/eks/latest/userguide/create-cluster.html) for more details.

### 1. Create a Cluster Service Role
- Open the [IAM Console](https://console.aws.amazon.com/iam/), go to **Roles**, and click **Create role**.
- Choose **AWS service** as the trusted entity type and select **EKS** → **EKS - Cluster**.
  ![IAM Role Creation](https://res.cloudinary.com/dljvrtsnk/image/upload/v1714389488/qdjzivrguhxmlpvomptv.png)
- Click **Next**, skip adding permissions, name the role **eksClusterRole**, and click **Create role**. (Note: ensure this exact name is used, as a different name will cause the cluster creation to fail)
  ![Assign Role Name](https://res.cloudinary.com/dljvrtsnk/image/upload/v1714389542/d0vb1cglwangn9lusr5f.png)

### 2. Create the EKS Cluster
- Go to the [EKS Console](https://console.aws.amazon.com/eks/home#/clusters) and click **Create cluster**.
  ![Create Cluster](https://res.cloudinary.com/dljvrtsnk/image/upload/v1714389755/yvspivykxbom8atrwgto.png)

### 3. Configure the Cluster
- Enter **demo-eks** as the name for your cluster.
- Select **eksClusterRole** as the IAM role.
- Choose **Kubernetes 1.31** as the version.
- Set **EKS API and ConfigMap** as the cluster authentication mode.
  ![Cluster Configuration](https://res.cloudinary.com/kodekloud/image/upload/v1731325608/kafka-lbd-course-images/Screenshot_2024-11-11_171623.png)

### 4. Specify Networking
- Select at least two subnets (avoid `us-east-1e`).
- Choose subnets from `us-east-1a`, `us-east-1b`, and `us-east-1c`.
- Ensure your security group allows necessary traffic.
  ![Networking Configuration](https://res.cloudinary.com/dljvrtsnk/image/upload/v1714389834/p2zljgzyaclkop25r744.png)

### 5. Configure Observability and Add-ons
- Select the default options in **Configure observability**.
- Select add-ons and **Configure selected add-ons settings**, then click **Next**.

### 6. Review and Create
- Review your settings and click **Create**. The cluster status will show as **CREATING** during provisioning.
  ![Cluster Creation](https://res.cloudinary.com/dljvrtsnk/image/upload/v1714389943/mkoqybyb1s9rvb2iww9a.png)

### 7. Configure `kubectl`
- Enable `kubectl` to interact with your cluster by updating the `kubeconfig`:
   ```bash
   aws eks update-kubeconfig --name <cluster-name>
   ```
   ![Update kubeconfig](https://res.cloudinary.com/dljvrtsnk/image/upload/v1714390063/xmfnqqxr1jbffwg2n9cj.png)

---

## Step 3: Computing Self-Managed Nodes

To make it simple, you can follow the steps below. For more information, refer to [AWS EKS Documentation](https://docs.aws.amazon.com/eks/latest/userguide/launch-workers.html).

**Note:** If you don’t provide a key pair here, the AWS CloudFormation stack creation fails. Ensure you have at least one key pair before moving forward. To create a key pair, see [AWS EC2 Documentation](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/create-key-pairs.html#having-ec2-create-your-key-pair).

### Steps:

1. **Open the AWS CloudFormation Console**:
   - Go to the [CloudFormation Console](https://console.aws.amazon.com/cloudformation).
   - Choose **Create stack** and select **With new resources (standard)**.
   ![Create Stack](https://res.cloudinary.com/dljvrtsnk/image/upload/v1714390143/xghwm2z0oqopyhdpczfv.png)

2. **Select the Template**:
   - Use the **Choose an existing template** option.
   - Choose **Amazon S3 URL** as the template source.
   - Enter the following URL in the **Amazon S3 URL** field:
     ```
     https://s3.us-west-2.amazonaws.com/amazon-eks/cloudformation/2022-12-23/amazon-eks-nodegroup.yaml
     ```
   - Click **Next**.
   ![Template URL](https://res.cloudinary.com/dljvrtsnk/image/upload/v1714390176/azqnrdckq9rrtqcnx55z.png)

3. **Specify Stack Details**:
   - **Stack name**: Enter `eks-cluster-stack`.(Note: ensure this exact name is used, as a different name will cause the cluster creation to fail)
   - **ClusterName**: Enter `demo-eks` (same name as your EKS cluster).
   - **ClusterControlPlaneSecurityGroup**: Select the security group of the cluster control plane.
   - **NodeGroupName**: Enter `eks-demo-node`.
   ![Stack Details](https://res.cloudinary.com/dljvrtsnk/image/upload/v1714390327/gnodiuipvzkpqzouevex.png)
   - **NodeImageIdSSMParam**: Pre-populated with the Amazon EC2 Systems Manager parameter. To use version 1.31, update it to:
     ```
     /aws/service/eks/optimized-ami/1.31/amazon-linux-2/recommended/image_id
     ```
   - **Subnets**: Select the same subnets as the EKS cluster.
   - **Instance Type**: Choose from `.nano`, `.micro`, `.small`, or `.medium` of `t1`, `t2`, or `t3` instance classes.
   - **Disk Type**: Choose `gp2`.
   - **Disk Size**: Maximum allowed is 10GB.
   ![Instance Configuration](https://res.cloudinary.com/dljvrtsnk/image/upload/v1714390378/qyztzkqpqvocpsygpdhr.png)
   - **KeyName**: Select an Amazon EC2 SSH key pair to enable SSH access to the nodes.
   - **VpcId**: Select the same VPC as the EKS cluster.
   ![VPC Selection](https://res.cloudinary.com/dljvrtsnk/image/upload/v1714390498/czwwukryhhldloe9ft5u.png)

4. **Configure Stack Options**:
   - Select the default options on the **Configure stack options** page.
   - Check the box for **“I acknowledge that AWS CloudFormation might create IAM resources”** at the bottom of this page.
   - Click **Next**.
![IAM Acknowledgement](https://res.cloudinary.com/dljvrtsnk/image/upload/v1714390534/y4kkpyqi8xpf6wzg3mny.png)

5. **Review and create**:
   - Click **Submit** in the Review and create page.
   

6. **Review Stack Creation**:
   - When the stack creation completes, select the stack in the console.
   - Go to **Outputs** and record the `NodeInstanceRole` for the node group. This role is needed to configure your Amazon EKS nodes.
   ![NodeInstanceRole](https://res.cloudinary.com/dljvrtsnk/image/upload/v1714390570/y5p56imorc7rh5pn0usa.png)

## Step 4: Joining the Worker Nodes

Execute the following commands in the lab terminal to join the worker nodes to your EKS cluster.

### 1. Download the Configuration Map
- Run the following command to download the `aws-auth-cm.yaml` file:
  ```bash
  curl -O https://s3.us-west-2.amazonaws.com/amazon-eks/cloudformation/2020-10-29/aws-auth-cm.yaml
  ```
- Open the `aws-auth-cm.yaml` file and update the value of the `rolearn` key with the `NodeInstanceRole` value recorded from the previous step.![](https://res.cloudinary.com/dljvrtsnk/image/upload/v1714390657/paszwq3ge2f8phkzzpiy.png)
### 2. Apply the Configuration
- Apply the updated configuration map using the command below. Note that it may take a few minutes to complete:
  ```bash
  kubectl apply -f aws-auth-cm.yaml
  ```
![](https://res.cloudinary.com/dljvrtsnk/image/upload/v1714390731/cs5oxd2pvg3yaehqsj7h.png)
By successfully creating an EKS cluster in KodeKloud Playground, you gain valuable experience and build confidence in working with real-world Kubernetes environments. Start creating your EKS cluster today and practice your Kubernetes skills. With each step, you’ll enhance your knowledge, build confidence, and strengthen your expertise in EKS and Kubernetes.

