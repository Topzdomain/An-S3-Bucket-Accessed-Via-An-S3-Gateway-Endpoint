<h2 align="center"> ACCESSING AN S3 BUCKET VIA S3 GATEWAY ENDPOINT</h2>

The purpose of this project is to demonstrate the use of VPC End Points to access resources on AWS securely. In this case, it is the use of VPC gateway endpoints to access an S3 bucket from a private subnet.


<h3 align="left"> SUMMARY</h3>

The use of VPC End Point is a vibrant security concept that can be used as a secure means of moving, collecting or accessing sensitive data between resources and this can be used both in an all-cloud architecture or hybrid environment. The use of a VPC endpoint ensures that the data does not traverse the internet and as such reduces the attack surface and possibility of data highjacking (MITM Attacks) while ensuring high availability of the data.

The process involves creating a bastion host for gaining access into the organization's private subnet and creating the private subnet from where the s3 bucket would be accessed via the VPC End-point link.

Project credit: thyagomota, github.com/thyagomota/aws-labs/master/lab-014


<p align="center">
<img src="https://github.com/Topzdomain/An-S3-Bucket-Accessed-Via-An-S3-Gateway-Endpoint/blob/main/Screenshots/Architecture_Diagram.png" height="50%" width="70%"/>
</p>
<h5 align="center"> Network Architecture</h5>


<h3 align="left"> Creating the VPC</h3>

Based on the network architecture, I created a VPC with a CIDR of 192.168.0.0/16.

<p align="center">
<img src="https://github.com/Topzdomain/An-S3-Bucket-Accessed-Via-An-S3-Gateway-Endpoint/blob/main/Screenshots/vpc.png" height="50%" width="70%"/>
</p>

<h3 align="left"> Creating Internet Gateway</h3>

Next, I created the internet gateway which I attached to the vpc.

<p align="center">
<img src="https://github.com/Topzdomain/An-S3-Bucket-Accessed-Via-An-S3-Gateway-Endpoint/blob/main/Screenshots/igw.png" height="50%" width="70%"/>
</p>

<p align="center">
<img src="https://github.com/Topzdomain/An-S3-Bucket-Accessed-Via-An-S3-Gateway-Endpoint/blob/main/Screenshots/attaching-igw-to-vpc.png" height="50%" width="70%"/>
</p>

<h3 align="left"> Creating Subnets</h3>

Next, I created two subnets, a public (bastion host) and a private subnet. After creation, I edited the subnet settings to auto-assign the IPv4 address to the public subnet.

<p align="center">
<img src="https://github.com/Topzdomain/An-S3-Bucket-Accessed-Via-An-S3-Gateway-Endpoint/blob/main/Screenshots/public-subnet1.png" height="50%" width="70%"/>
</p>
<p align="center">
<img src="https://github.com/Topzdomain/An-S3-Bucket-Accessed-Via-An-S3-Gateway-Endpoint/blob/main/Screenshots/public-subnet2.png?raw=true" height="40%" width="48%"/>
<img src="https://github.com/Topzdomain/An-S3-Bucket-Accessed-Via-An-S3-Gateway-Endpoint/blob/main/Screenshots/private-subnet.png?raw=true" height="40%" width="48%"/>
</p>

Configuring Subnet Settings to Auto-Assign IPv4 Address to Subnets

<p align="center">
<img src="https://github.com/Topzdomain/An-S3-Bucket-Accessed-Via-An-S3-Gateway-Endpoint/blob/main/Screenshots/edit-subnet.png" height="50%" width="70%"/>
</p>

<p align="center">
<img src="https://github.com/Topzdomain/An-S3-Bucket-Accessed-Via-An-S3-Gateway-Endpoint/blob/main/Screenshots/enabling-auto-assign-ip-address.png?raw=true" height="50%" width="70%"/>
</p>

Click the save button after enabling the auto-assign public IPv4 address

<h3 align="left"> Creating and Configuring Route Table</h3>

A default route is usually created after a vpc is created. I edited the name to "Main Route" and edited the route table to send all internet-bound traffic through the IGW. I also associated the public subnets with the main route.

<p align="center">
<img src="https://github.com/Topzdomain/An-S3-Bucket-Accessed-Via-An-S3-Gateway-Endpoint/blob/main/Screenshots/editing-route-for-main-route.png?raw=true" height="70%" width="90%"/>
</p>

<p align="center">
<img src="https://github.com/Topzdomain/An-S3-Bucket-Accessed-Via-An-S3-Gateway-Endpoint/blob/main/Screenshots/associating-public-subnet-to-main-route.png" height="70%" width="90%"/>
</p>

<p align="center">
<img src="https://github.com/Topzdomain/An-S3-Bucket-Accessed-Via-An-S3-Gateway-Endpoint/blob/main/Screenshots/creating-private-rt.png" height="90%" width="70%"/>
</p>

<p align="center">
<img src="https://github.com/Topzdomain/An-S3-Bucket-Accessed-Via-An-S3-Gateway-Endpoint/blob/main/Screenshots/associating-private-subnet-to-private-rt.png" height="75%" width="90%"/>
</p>


<h3 align="left"> Configuring Security Group</h3>

I configured the default security group created by the VPC to allow SSH and HTTP traffic from any IPv4 address. 

<h3 align="left"> Launching The EC2 Instances</h3>
The next step is to create the EC2 instances, One in the public subnet which is the bastion host and from where we'll gain access into the private subnet, and the other in the private subnet from where the S3 Bucket would be accessed. After gaining access to the Bastion host using SSH, I used secure copy (SCP) to copy the pem key into the Bastion host so I could then use the key to log into the private subnet since I used the same key for both.

Creating The Public EC2 Instance

<p align="center">
<img src="https://github.com/Topzdomain/An-S3-Bucket-Accessed-Via-An-S3-Gateway-Endpoint/blob/main/Screenshots/public-ec2-instance1.png" height="50%" width="70%"/>
</p>

<p align="center">
<img src="https://github.com/Topzdomain/An-S3-Bucket-Accessed-Via-An-S3-Gateway-Endpoint/blob/main/Screenshots/public-ec2-instance2.png" height="75%" width="90%"/>
</p>

<p align="center">
<img src="https://github.com/Topzdomain/An-S3-Bucket-Accessed-Via-An-S3-Gateway-Endpoint/blob/main/Screenshots/public-ec2-instance3.png" height="75%" width="90%"/>
</p>

<p align="center">
<img src="https://github.com/Topzdomain/An-S3-Bucket-Accessed-Via-An-S3-Gateway-Endpoint/blob/main/Screenshots/public-ec2-instance4.png" height="75%" width="90%"/>
</p>

I clicked on the "Create" at the bottom of the page

Creating The Private EC2 Instance

<p align="center">
<img src="https://github.com/Topzdomain/An-S3-Bucket-Accessed-Via-An-S3-Gateway-Endpoint/blob/main/Screenshots/Private-ec2-instance1.png" height="50%" width="70%"/>
</p>

<p align="center">
<img src="https://github.com/Topzdomain/An-S3-Bucket-Accessed-Via-An-S3-Gateway-Endpoint/blob/main/Screenshots/Private-ec2-instance2.png" height="75%" width="90%"/>
</p>

<p align="center">
<img src="https://github.com/Topzdomain/An-S3-Bucket-Accessed-Via-An-S3-Gateway-Endpoint/blob/main/Screenshots/Private-ec2-instance3.png" height="75%" width="90%"/>
</p>

<p align="center">
<img src="https://github.com/Topzdomain/An-S3-Bucket-Accessed-Via-An-S3-Gateway-Endpoint/blob/main/Screenshots/Private-ec2-instance4.png" height="75%" width="90%"/>
</p>

I clicked on the "Create" at the bottom of the page

<h3 align="left"> Creating The S3 Bucket</h3>

The next step is to create the S3 bucket and add some content to it (Not very necessary to add content)

<p align="center">
<img src="https://github.com/Topzdomain/An-S3-Bucket-Accessed-Via-An-S3-Gateway-Endpoint/blob/main/Screenshots/creating-s3-bucket1.png" height="75%" width="90%"/>
</p>

<p align="center">
<img src="https://github.com/Topzdomain/An-S3-Bucket-Accessed-Via-An-S3-Gateway-Endpoint/blob/main/Screenshots/creating-s3-bucket2.png" height="75%" width="90%"/>
</p>

<p align="center">
<img src="https://github.com/Topzdomain/An-S3-Bucket-Accessed-Via-An-S3-Gateway-Endpoint/blob/main/Screenshots/creating-s3-bucket3.png" height="75%" width="90%"/>
</p>

Logging to Bastion Host Using SSH

```commandline
ssh -i "practice.pem" ec2-user@34.200.249.14
```

<p align="center">
<img src="https://github.com/Topzdomain/An-S3-Bucket-Accessed-Via-An-S3-Gateway-Endpoint/blob/main/Screenshots/login-to-public-ec2-instance.png" height="50%" width="70%"/>
</p>


Copying The Pem Key To The Bastion Host To Enable Login To Private Subnet

```commandline
scp -i practice.pem practice.pem ec2-user@3.238.8.126:/home/ec2-user
```

To give read permission to the owner of the pem key only

```commandline
chmod 400 practice.pem
```

Logging to Private Subnet Using SSH

```commandline
ssh -i "practice.pem" ec2-user@34.200.249.14
```

<p align="center">
<img src="https://github.com/Topzdomain/An-S3-Bucket-Accessed-Via-An-S3-Gateway-Endpoint/blob/main/Screenshots/login-to-private-ec2-instance.png" height="75%" width="90%"/>
</p>

<h3 align="left"> Proof of Concept</h3>

Just to be sure, I'll try to access the s3 bucket through the private subnet. I faced two challenges in this process: aws cli was not installed in the ubuntu server that I launched, so I created and configured a NAT gateway to provide internet to the private subnet and installed aws cli. I deleted the NAT gateway after the installation was complete. The next problem was that the AWS commands refused to execute, so I created an IAM role to give full s3 access and attached it to the private EC2 instance.

<p align="center">
<img src="https://github.com/Topzdomain/An-S3-Bucket-Accessed-Via-An-S3-Gateway-Endpoint/blob/main/Screenshots/attaching-iam-role-to-instance.png" height="75%" width="90%"/>
</p>

<p align="center">
<img src="https://github.com/Topzdomain/An-S3-Bucket-Accessed-Via-An-S3-Gateway-Endpoint/blob/main/Screenshots/attaching-iam-role-to-instance2.png" height="75%" width="90%"/>
</p>

I eventually tried to access the s3 bucket from the private instance, but because I had not created the VPC  endpoint, the connection just timed out. it did not return details of the s3 bucket.

```commandline
aws s3 ls
```

<h2 align="left"> Creating The VPC EndPoint</h2>

<p align="center">
<img src="https://github.com/Topzdomain/An-S3-Bucket-Accessed-Via-An-S3-Gateway-Endpoint/blob/main/Screenshots/creating-vpc-endpoint.png" height="75%" width="90%"/>
</p>

<p align="center">
<img src="https://github.com/Topzdomain/An-S3-Bucket-Accessed-Via-An-S3-Gateway-Endpoint/blob/main/Screenshots/creating-vpc-endpoint2.png" height="75%" width="90%"/>
</p>

<p align="center">
<img src="https://github.com/Topzdomain/An-S3-Bucket-Accessed-Via-An-S3-Gateway-Endpoint/blob/main/Screenshots/creating-vpc-endpoint3.png" height="75%" width="90%"/>
</p>

<p align="center">
<img src="https://github.com/Topzdomain/An-S3-Bucket-Accessed-Via-An-S3-Gateway-Endpoint/blob/main/Screenshots/creating-vpc-endpoint4.png?raw=true" height="75%" width="90%"/>
</p>
https://github.com/Topzdomain/An-S3-Bucket-Accessed-Via-An-S3-Gateway-Endpoint/blob/main/Screenshots/accessing-s3-via-private-ec2-working.png?raw=true
After creating the VPC Endpoint, I tried accessing the s3 bucket, and I was able to list the bucket and its content.

<p align="center">
<img src="https://github.com/Topzdomain/An-S3-Bucket-Accessed-Via-An-S3-Gateway-Endpoint/blob/main/Screenshots/accessing-s3-via-private-ec2-working.png?raw=true" height="75%" width="90%"/>
</p>
