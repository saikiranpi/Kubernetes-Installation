# Terraform to Bulding Kubernetes Cluster using EC2 instances
[![LinkedIn][linkedin-shield]][linkedin-url]
[![GitHub][github-shield]][github-url]


I built this project to create my own lab for [Kuberntes](https://kubernetes.io/) cluster on AWS cloud using EC2 instances. I found [Terraform](https://www.terraform.io) is the best tool to create my K8S lab fastly with one command 🚀.
<p align="center">

![Terraform](https://i.imgur.com/PuS3rmb.png)
</p>

## Terraform Resources Used
- EC2
  - One Master Node
  - Two Worker Node (can be increased)
- VPC
  - Public Subnet
  - Internet Gateway
  - Route Table
  - Security Group
- S3 Bucket

<hr>

## How Will the Kubernetes Cluster Be Built?
The goal is to build a K8S cluster with one master node and two worker nodes.
<br>

* First, the master node will boot up and will start installing <b>kubeadm</b>, <b>kubelet</b>, <b>kubectl</b>, and <b>docker</b>. Then will run `kubeadm init` to initial the k8s cluster. <br>
Here the challenge becomes how we can get the join command that showed after init the cluster and send it to the workers node for joining the worker node into the cluster 🤔? <br>
To solve this problem I use <b>s3 bucket</b>. First I extracted the join command and saved it into a file, and then pushed it to the s3 object. Now we finish from the master node and is ready.
<br>

* Second, the workers node will boot up and will start installing <b>kubeadm</b>, <b>kubelet</b>, <b>kubectl</b>, and <b>docker</b>. Then will fetch the joined command from <b>s3 bucket</b> and execute to join the worker node into the cluster.

<hr>

## Incress Number of Worker Nodes
* By default there are two workers on the cluster, to increase it go to `variables.tf` file and looking for <b>number_of_worker</b> variable, you can increase the default number.

<hr>

## Requirements Before Running
1- Make sure you have the Terraform tools installed on your machine.

2- Add your Access key, Secret key, and Key Pair name on `variables.tf` file.

3- Make sure your IAM user has the right permission to create EC2, VPC, S3, Route Table, Security Group, and Internet Gateway.

## Running the Script
After doing the requirements, you are ready now, start cloning the repo to your machine:
``` shell
git clone https://github.com/Ahmad-Faqehi/Terraform-Bulding-K8S.git
cd Terraform-Bulding-K8S/
```
Now execute terraform commands:
``` shell
terraform init
terraform plan #to show what going to build
terraform apply
```

## Accessing Your Cluster
* You can access your cluster by accessing the master node through <b>ssh</b>, you can get the public IP of the master node from terraform outputs. Below is an example of the ssh command:
``` shell
ssh -i <Your_Key_Piar> ubuntu@<MasterNode_Public_IP>
```

* Another way to access the cluster is by downloading the `admin.conf` file from the master node to your machine, find below the way to download it and access the cluster remotely.
``` shell
scp -i <Your_Key_Piar> ubuntu@<MasterNode_Public_IP>:/tmp/admin.conf .
```
This will download the Kubernetes config file on your machine. Before using this config file, you have to replace the private IP with the public IP of the master node. Then you can use the following command to start accessing the cluster.
```shell
kubectl --kubeconfig ./admin.conf get nodes
```

## Removing and Destroying Kubernetes Cluster
To destroy all the resources that were created after applying the script, just run the following command:
```shell
terraform destroy
```



<!-- MARKDOWN LINKS & IMAGES -->
<!-- https://www.markdownguide.org/basic-syntax/#reference-style-links -->
[linkedin-shield]: https://img.shields.io/badge/-LinkedIn-black.svg?style=for-the-badge&logo=linkedin&colorB=555
[linkedin-url]: https://www.linkedin.com/in/saikiran-p-a0243569/
[docker-shield]: https://img.shields.io/badge/-docker-black.svg?style=for-the-badge&logo=docker&colorB=555
[docker-url]: https://hub.docker.com/u/kiran2361993
[github-shield]: https://img.shields.io/badge/-github-black.svg?style=for-the-badge&logo=github&colorB=555
[github-url]: https://github.com/saikiranpi
