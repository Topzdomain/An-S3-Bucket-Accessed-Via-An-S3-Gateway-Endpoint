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


<h2 align="left"> Creating the VPC</h2>

Based on the network architecture, I created a VPC with a CIDR of 192.168.0.0/16.

<p align="center">
<img src="vpc" height="65%" width="50%"/>
</p>

<h2 align="left"> Creating Internet Gateway</h2>

Next, I created the internet gateway which I attached to the vpc.

<p align="center">
<img src="igw" height="40%" width="60%"/>
</p>

<p align="center">
<img src="attaching-igw-to-vpc" height="40%" width="60%"/>
</p>

<h2 align="left"> Creating Subnets</h2>

Next, I created two subnets, a public (bastion host) and a private subnet. After creation, I edited the subnet settings to auto-assign the IPv4 address to the public subnet.

<p align="center">
<img src="s3-public-subnet" height="35%" width="45%"/>
<img src="s3-private-subnet" height="35%" width="45%"/>
</p>

Configuring Subnet Settings to Auto-Assign IPv4 Address to Subnets

<p align="center">
<img src="edit-subnet-settings" height="60%" width="80%"/>
</p>

<p align="center">
<img src="enable-auto-assign-ip" height="60%" width="80%"/>
</p>

Click the save button after enabling the auto-assign public IPv4 address

<h2 align="left"> Creating and Configuring Route Table</h2>

A default route is usually created after a vpc is created. I edited the name to "Main Route" and edited the route table to send all internet-bound traffic through the IGW. I also associated the public subnets with the main route.

<p align="center">
<img src="editing-main-route" height="45%" width="65%"/>
</p>

<p align="center">
<img src="associating-public-subnet-to-main-route" height="45%" width="65%"/>
</p>

<p align="center">
<img src="creating-private-rt" height="45%" width="65%"/>
</p>

<p align="center">
<img src="associating-private-subnet-to-private-rt" height="45%" width="65%"/>
</p>


<h2 align="left"> Configuring Security Group</h2>

I configured the default security group created by the VPC to allow SSH and HTTP traffic from any IPv4 address. 

<p align="center">
<img src="" height="60%" width="80%"/>
</p>


<h2 align="left"> Launching The EC2 Instances</h2>
The next step is to create the EC2 instances, One in the public subnet which is the bastion host and from where we'll gain access into the private subnet, and the other in the private subnet from where the S3 Bucket would be accessed. After gaining access to the Bastion host using SSH, I used secure copy (SCP) to copy the pem key into the Bastion host so I could then use the key to log into the private subnet since I used the same key for both.

Creating The Public EC2 Instance

<p align="center">
<img src="public-ec2-1" height="40%" width="60%"/>
</p>

<p align="center">
<img src="public-ec2-2" height="40%" width="60%"/>
</p>

<p align="center">
<img src="public-ec2-3" height="40%" width="60%"/>
</p>

<p align="center">
<img src="public-ec2-4" height="40%" width="60%"/>
</p>

<p align="center">
<img src="public-ec2-5" height="40%" width="60%"/>
</p>


Creating The Private EC2 Instance

<p align="center">
<img src="private-ec2-1" height="40%" width="60%"/>
</p>

<p align="center">
<img src="private-ec2-2" height="40%" width="60%"/>
</p>

<p align="center">
<img src="private-ec2-3" height="40%" width="60%"/>
</p>

<p align="center">
<img src="private-ec2-4" height="40%" width="60%"/>
</p>

<p align="center">
<img src="private-ec2-5" height="40%" width="60%"/>
</p>


<h2 align="left"> Creating The S3 Bucket</h2>

The next step is to create the S3 bucket and add some content to it (Not very necessary to add content)

<p align="center">
<img src="s3-bucket-creation1" height="45%" width="65%"/>
</p>

<p align="center">
<img src="s3-bucket-creation2" height="45%" width="65%"/>
</p>

<p align="center">
<img src="s3-bucket-creation3" height="45%" width="65%"/>
</p>

Logging to Bastion Host Using SSH

```commandline
ssh -i "practice.pem" ec2-user@34.200.249.14
```

<p align="center">
<img src="login-to-public-subnet" height="45%" width="65%"/>
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
<img src="login-to-private-subnet" height="45%" width="65%"/>
</p>

<h2 align="left"> Proof of Concept</h2>

Just to be sure, I'll try to access the s3 bucket through the private subnet. Two challenges that I faced in this process were that aws cli was not installed in the ubuntu server that I launched, so I created and configured a NAT gateway to provide internet to private subnet and installed aws cli. I deleted the NAT gateway after the installation was complete. The next problem was that the AWS commands refused to execute, so I created an IAM role to give full s3 access and attached it to the private ec2 instance.

<p align="center">
<img src="attaching-iam-role-instance" height="45%" width="65%"/>
</p>

<p align="center">
<img src="attaching-iam-role-instance2" height="45%" width="65%"/>
</p>

I eventually tried to access the s3 bucket from the private instance, but because I had not created the VPC  endpoint, the connection just timed out. it did not return details of the s3 bucket.

```commandline
aws s3 ls
```
<p align="center">
<img src="s3-bucket-not-listing" height="45%" width="65%"/>
</p>

<h2 align="left"> Creating The VPC EndPoint</h2>

<p align="center">
<img src="creating-vpc-endpoint1" height="60%" width="80%"/>
</p>

<p align="center">
<img src="creating-vpc-endpoint2" height="60%" width="80%"/>
</p>

<p align="center">
<img src="creating-vpc-endpoint3" height="60%" width="80%"/>
</p>

<p align="center">
<img src="creating-vpc-endpoint4" height="60%" width="80%"/>
</p>

After creating the VPC Endpoint, I tried accessing the s3 bucket, and I was able to list the bucket and its content.

<p align="center">
<img src="accessing-s3-bucket-via-private-instance" height="60%" width="80%"/>
</p>
