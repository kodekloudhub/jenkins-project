# How to Create an EKS Cluster in KodeKloud Labs

KodeKloud is an interactive learning platform that offers hands-on experience with various technologies, including Kubernetes and EKS. It provides a safe and sandboxed environment for experimentation, practice, and mastery of skills without the concerns of managing underlying infrastructure. EKS is widely adopted across organizations, making it a valuable practice environment for industry-standard Kubernetes skills. This guide will walk you through creating an EKS cluster in KodeKloud Lab.

## Note:
- **Kubernetes version**: 1.29
- **Availability Zone**: For us-east-1e, users cannot make changes or use it. Therefore, we will not select the subnet from the us-east-1e availability zone.
- **Terraform Guide**: For an automated approach using Terraform, follow the guide by @Alistair_KodeKloud. [Here](https://github.com/kodekloudhub/certified-kubernetes-administrator-course/tree/master/managed-clusters/eks)

## Prerequisites:
- Basic knowledge of AWS and Kubernetes.

## Step 1: Provision AWS Cloud Lab
- Click **START LAB** to request a new AWS Cloud instance; after a few seconds, you will receive your credentials to access the AWS Cloud console.
![](https://res.cloudinary.com/dljvrtsnk/image/upload/v1714389164/qnmfarvvje6f9ab24zfz.png)
- Access to Console link and using provided credentials.
![](https://res.cloudinary.com/dljvrtsnk/image/upload/v1714389234/ywggrnull0swswqgvpzc.png)

## Step 2: Creating cluster EKS

To make it simple, you can follow the steps below. For more information please refer to [AWS EKS Documentation](https://docs.aws.amazon.com/eks/latest/userguide/create-cluster.html).

1. Before creating an Amazon EKS cluster, you need to create the Cluster service role. To create a new IAM role for EKS in AWS, start by opening the IAM console at [IAM Console](https://console.aws.amazon.com/iam/). Then, click on 'Roles' and select 'Create role'. Under 'Trusted entity type', choose 'AWS service'. In the 'Use cases for other AWS services' dropdown, select 'EKS' and then pick 'EKS - Cluster'.
![](https://res.cloudinary.com/dljvrtsnk/image/upload/v1714389488/qdjzivrguhxmlpvomptv.png)
   Click ‘Next’ and proceed without adding any additional permissions in the ‘Add permissions’ tab. Next, assign **eksClusterRole** to your role. Finally, click on ‘Create role’ to complete the process.
![](https://res.cloudinary.com/dljvrtsnk/image/upload/v1714389542/d0vb1cglwangn9lusr5f.png)
2. Open the Amazon EKS console at [EKS Console](https://console.aws.amazon.com/eks/home#/clusters) Choose Create cluster.
![](https://res.cloudinary.com/dljvrtsnk/image/upload/v1714389755/yvspivykxbom8atrwgto.png)
3. On the Configure cluster page, Enter **demo-eks** as the name for your cluster, and choose the Kubernetes 1.29 version. You can also select other options as per your requirements.
![](https://res.cloudinary.com/dljvrtsnk/image/upload/v1714389803/qvmcgexaprki14alxlhe.png)
4. On the Specify networking page, select values for the Subnets fields - You must select at least two values (Do not select the subnet from the us-east-1e availability zone). In this tutorial, subnets from **us-east-1a, us-east-1b, us-east-1c** availability zones are selected. Choose an existing security group or create a new one. Ensure that the security group allows inbound and outbound traffic on the required ports for your applications. Choose Next..
![](https://res.cloudinary.com/dljvrtsnk/image/upload/v1714389834/p2zljgzyaclkop25r744.png)
5. On the Configure logging and Select add-ons page, you can optionally choose which you want and then select Next.

6. On the Review and Create page, review the information that you entered or selected on the previous pages. If you need to make changes, choose Edit. After that, choose Create. The Status field shows CREATING while the cluster is provisioned.
![](https://res.cloudinary.com/dljvrtsnk/image/upload/v1714389943/mkoqybyb1s9rvb2iww9a.png)
7. Once the cluster is created, enable kubectl to communicate with your cluster by adding a new context to the kubectl config file by executing the following command in Terminal.
   ```bash
   $ aws eks update-kubeconfig --name <cluster-name>
   ```
   ![](https://res.cloudinary.com/dljvrtsnk/image/upload/v1714390063/xmfnqqxr1jbffwg2n9cj.png)
## Step 3: Computing self-managed nodes

To make it simple, you can follow the steps below. For more information please refer to [AWS EKS Documentation](https://docs.aws.amazon.com/eks/latest/userguide/launch-workers.html)

**Note:** If you don’t provide a key pair here, the AWS CloudFormation stack creation fails. Therefore please ensure that you have at least a key pair before moving forward. You can create one in the AWS Management Console. To create a key pair, see [AWS EC2 Documentation](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/create-key-pairs.html#having-ec2-create-your-key-pair)

1. Open the AWS CloudFormation console at [CloudFormation Console](https://console.aws.amazon.com/cloudformation). Choose Create stack and then select With new resources (standard).
![](https://res.cloudinary.com/dljvrtsnk/image/upload/v1714390143/xghwm2z0oqopyhdpczfv.png)
2. Use the template ready option. Select the Amazon S3 Url as the template source and add the following file location URL in the Amazon S3 Url. Choose Next.
   https://s3.us-west-2.amazonaws.com/amazon-eks/cloudformation/2022-12-23/amazon-eks-nodegroup.yaml
![](https://res.cloudinary.com/dljvrtsnk/image/upload/v1714390176/azqnrdckq9rrtqcnx55z.png)
3. On the Specify stack details page, enter the following parameters accordingly, and then choose Next:

   - Stack name: Enter **eks-cluster-stack** for your AWS CloudFormation stack.

   - ClusterName: Enter **demo-eks** as name that you used when you created your Amazon EKS cluster. This name must be the same as the cluster name or your nodes can’t join the cluster.

   - ClusterControlPlaneSecurityGroup: Choose the security group of the cluster control plane.

   - NodeGroupName: Enter **eks-demo-node** for your node group.
![](https://res.cloudinary.com/dljvrtsnk/image/upload/v1714390327/gnodiuipvzkpqzouevex.png)
   - NodeImageIdSSMParam: Pre-populated with the Amazon EC2 Systems Manager parameter of a recent Amazon EKS optimized AMI for a variable Kubernetes version. If you want to use version 1.29, you can update the field to /aws/service/eks/optimized-ami/1.29/amazon-linux-2/recommended/image_id
   
   -  Subnets: Select the subnets as same as EKS cluster

   - Choose one of these instance types: .nano, micro, .small, .medium of t1,t2, and t3 instance class.

   - Choose the disk type as “gp2” only.

   - The maximum disk size per node allowed is 10GB.
![](https://res.cloudinary.com/dljvrtsnk/image/upload/v1714390378/qyztzkqpqvocpsygpdhr.png)
   - KeyName: Select the name of an Amazon EC2 SSH key pair that you can use to connect using SSH into your nodes after they launch.

   - VpcId: Select the VPC as same as EKS cluster
![](https://res.cloudinary.com/dljvrtsnk/image/upload/v1714390498/czwwukryhhldloe9ft5u.png)

4. Select your desired choices on the Configure stack options page, and then choose Next

5. As on the box below, stick “I acknowledge that AWS CloudFormation might create IAM resources”, and then choose Submit.
![](https://res.cloudinary.com/dljvrtsnk/image/upload/v1714390534/y4kkpyqi8xpf6wzg3mny.png)
6. When your stack has finished creating, select it in the console and choose Outputs. Record the NodeInstanceRole for the node group that was created. You need this when you configure your Amazon EKS nodes.
![](https://res.cloudinary.com/dljvrtsnk/image/upload/v1714390570/y5p56imorc7rh5pn0usa.png)

## Step 4: Joining the worker nodes

Execute the following commands in the terminal.

First, you need to download the configuration map by executing the following command. Then, in the aws-auth-cm.yaml file, update the value of rolearn key to NodeInstanceRole value which is created above.

```
curl -O https://s3.us-west-2.amazonaws.com/amazon-eks/cloudformation/2020-10-29/aws-auth-cm.yaml
```
![](https://res.cloudinary.com/dljvrtsnk/image/upload/v1714390657/paszwq3ge2f8phkzzpiy.png)

Next, apply the configuration. This command may take a few minutes to finish.
```bash
kubectl apply -f aws-auth-cm.yaml
```
![](https://res.cloudinary.com/dljvrtsnk/image/upload/v1714390731/cs5oxd2pvg3yaehqsj7h.png)
By successfully creating an EKS cluster in KodeKloud Playground, you not only gain valuable experience but also build confidence in working with real-world Kubernetes environments. So, start creating your EKS cluster today and practice your Kubernetes skills. With each step, you’ll build confidence, expand your knowledge, and strengthen your skills in EKS and Kubernetes.