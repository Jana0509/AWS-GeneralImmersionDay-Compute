# AWS-GeneralImmersionDay-Compute

---

Introduction:
We've been exploring AWS services through videos and tutorials, but the real magic happens when we roll up our sleeves and get hands-on! Practical experience gives us a deeper understanding of the services. Today, we'll kick-start a series of hands-on exercises covering foundational AWS services.
Here's a sneak peek at the services we'll explore:

**Compute:** Amazon EC2

**Network:** Amazon VPC

**Security:** AWS IAM

**Monitoring:** Amazon CloudWatch

**Database:** Amazon RDS

**Storage:** Amazon S3, Elastic File System (EFS)

**Provisioning:** AWS CloudFormation

Our end goal? Designing a three-tier architecture with these services. In this blog, let's dive into AWS Compute - specifically Amazon EC2 - and learn how to create scalable and resilient systems. Just a note this will be a big article as we have to cover many services, but it's worth going through and understand how services are working when it is tied together!

**AWS Compute:** The Engine of Your Applications
Compute is where all the processing magic happens. Think of it as the engine in your car - it powers everything. AWS provides a variety of compute services tailored for different workloads:

**EC2:** Virtual servers for general-purpose computing.
Lambda: Serverless computing to run code without provisioning servers.
ECS/EKS/Fargate: Services for running containerized applications.

In this hands-on session, we'll focus on Amazon EC2 - a flexible, scalable service that lets you launch virtual machines (VMs) in the cloud.


# Amazon EC2: Your Virtual Machine in the Cloud
Amazon EC2 eliminates the need for upfront hardware investments, enabling you to quickly deploy applications. EC2 instances can scale horizontally (add more servers) or vertically (upgrade server size) based on traffic. This flexibility is managed through features like Auto Scaling Groups.

**Here's what we'll accomplish:**
* Create an EC2 instance and set up a basic web server.
* Configure auto-scaling to ensure resilience and scalability.

  ![image](https://github.com/user-attachments/assets/38f78838-a6f7-44c6-8db3-8d64f6a98a70)



# 1. Create a Key Pair

* Go to the AWS Console, search for "**Key Pairs**," and create a new key pair.
* Choose **PEM** format for Linux VMs or **PPK** for Windows VMs.
* Download the .pem file. This will act as your "access pass" to the instance - keep it safe!

  ![image](https://github.com/user-attachments/assets/2d1a123c-d7be-45b8-98a3-a891eb96672a)


# 2. LAUNCH A WEBSERVER INSTANCE:
In this step, we are going to create Linux VM and boostrap Apache/PHP, and install a basic web page using userdata that will display information about our instance.

1. Search EC2 in the console and click launch instance.
2. Give a Name and Select Application and OS type

![image](https://github.com/user-attachments/assets/5325543f-3c94-46db-a4b4-5fe06f3b1ff1)


3. Select the instance type as t2.micro[ Free Tier Eligible] and select the keypair which we created before.

![image](https://github.com/user-attachments/assets/c5b3c688-f6d7-45f8-8e2d-66571ba3955e)


4. Now, we are setting up security group, SG acts as a firewall to your instance. If you want to allow any specific IP, you have to give the ip address in the SG inbound rules as below.

![image](https://github.com/user-attachments/assets/064b599e-e537-40f3-a2dc-d7df442f7ae6)


Here, we are created 2 security group rules.
Allow SSH traffic from my IP.
Allow Http traffic from my IP

5. All other values accept the default values, expand by clicking on the Advanced Details tab at the bottom of the screen.

Click the Meta Data version dropdown and select V2 only (token required)

Enter the following values in the User data field and select Launch instance.

---------------------------------------------------------------------------------------------------------------
Userdata:
#!/bin/sh
​
#Install a LAMP stack
dnf install -y httpd wget php-fpm php-mysqli php-json php php-devel
dnf install -y mariadb105-server
dnf install -y httpd php-mbstring
​
#Start the web server
chkconfig httpd on
systemctl start httpd
​
#Install the web pages for our lab
if [ ! -f /var/www/html/immersion-day-app-php7.zip ]; then
 cd /var/www/html
 wget -O 'immersion-day-app-php7.zip' 'https://static.us-east-1.prod.workshops.aws/public/ca14f648-293b-43f3-92c2-9575e579efe2/assets/immersion-day-app-php7.zip'
 unzip immersion-day-app-php7.zip
fi
​
#Install the AWS SDK for PHP
if [ ! -f /var/www/html/aws.zip ]; then
 cd /var/www/html
 mkdir vendor
 cd vendor
 wget https://docs.aws.amazon.com/aws-sdk-php/v3/download/aws.zip
 unzip aws.zip
fi

# Update existing packages
dnf update -y
--------------------------------------------------------------------------------------------------------------------------------------------------
​

6. Once the instance is provisioned, you can find the instance details under instance tab. In details section, you can find the public IPV4 address and public IPV4 DNS name also.

   ![image](https://github.com/user-attachments/assets/915eaa3e-e051-40d8-b6c0-13d172226903)


**Finally, you can browse this instance by using PUBLIC IPV4 address in the browser. You will see the webserver running on your linux or windows instance.**

![image](https://github.com/user-attachments/assets/6f676624-f4b2-44d9-8380-64c129f56bc5)

CONGRATULATIONS , you have launched the EC2 instance and running a web server in a matter of minute.
---

# CONNECT TO YOUR EC2 LINUX INSTANCE:

In this section, we will see how to connect to instance by doing SSH[secure shell]
1. In your EC2 Console, click connect

![image](https://github.com/user-attachments/assets/2a57571e-908e-4dcf-8f26-1952e64349c8)


2. Copy the Ssh command to connect to the instance .
NOTE : Replace with your name for Jana
ssh -i "Jana-ImmersionDay.pem" ec2-user@ec2–54–253–192–212.ap-southeast-2.compute.amazonaws.com

![image](https://github.com/user-attachments/assets/41e060ee-bb7f-405f-bbda-efbef56e605e)


Now, by using SSH, you can access the instance which you have created. Let's proceed forward.

**NOTE : Clean up the resource which we created In order to avoid any cost!**

---

# EC2 AUTOSCALING:

![image](https://github.com/user-attachments/assets/da35d223-3367-40c7-9967-3e68ac160b02)


**What AutoScaling does ?**

As a name implies, autoscaling helps the instances to automatically scales vertically [big server] or horizontally [more server] depends upon the traffic.
In this Lab, The end result will be an auto scaling group behind a load balancer that scales based on CPU utilization of the hosts.
Services and concepts covered in this lab:

**Primary:** Auto Scaling, Launch Templates, Creation and configuration of Security Groups
**Secondary:** AMIs, Application Load Balancers

In this Lab, we are going to create the AMI [Amazon Machine Image], AMI is nothing but the template for your instance. If you want to customize your instance by installing some software or web server. You can first create the AMI and launch the instances. If you launch the instance using AMI, your instance will be coming with the software when its boot, so you don't need to download the software once the instance is created.

In real time scenario, for creating many VMs with pre-installed software, it is recommended to create one AMI with software you need and use the AMI to launch as many instances you want.

# Download and Launch the Cloud Formation Template:

1. Download the "EC2-Auto-Scaling-Lab.yaml" CloudFormation template by right-clicking on this link and save it to your local hard drive.


2. In the AWS Console search for CloudFormation or select the Services menu and click on CloudFormation under "Management & Governance".

   ![image](https://github.com/user-attachments/assets/a7fe4fe4-4e69-4d41-bb8d-19018d764726)

3. In the AWS Console search for CloudFormation or select the Services menu and click on CloudFormation under “Management & Governance”.

![image](https://github.com/user-attachments/assets/fbcf2327-dce1-4f7b-a058-280d6f065ef3)


4. Give the details and attach the yaml file

![image](https://github.com/user-attachments/assets/1f1f0ac6-6c56-41d3-ae00-e5ad7c875770)


5. Specify the details as below and provide your IP in "MyIP" and select the VPC and subnet which has access to Internet. A public subnet is defined by a subnet having a route to the internet gateway within it's route table. By default, the Default VPC subnets are all public.

![image](https://github.com/user-attachments/assets/eca0e8d2-a813-43b5-8d68-6d65e03eee96)


6. Once you are done entering the details above, click on Next. On the next page, "Configure stack options", you can leave "Tags", "Permissions", and "Advanced options" as default and select Next.
7. In the Review Page, review all the details and click submit.

![image](https://github.com/user-attachments/assets/f28b1251-46c0-4fe0-8abc-19a9571222e2)


Once you see "Create_Complete", resources are provisioned.

8. Now, launch the EC2 instance using the Public DNS URL
Here, we have created the AMI [Amazon Machine Image] with web server installed 

![image](https://github.com/user-attachments/assets/cce9ddf3-bf83-4852-b71f-fd462f13584a)


---

# GENERATE THE CUSTOM AMI OF THE WEB SERVER CREATED IN THE EC2 - LINUX LAB:
Now we have a instance running with the web server to host our website. Now we will create the Image of the web host that will be used for the autoscaling group to create multiple instances using the web server.

1. In the EC2 Console under Instances, you can create Amazon Machine Images (AMIs) from either running or stopped instances. Return to or open the EC2 console.

![image](https://github.com/user-attachments/assets/3bffeb27-8578-4a24-adeb-b6d3a98d2f32)


2. Give the Image name and description and create image

![image](https://github.com/user-attachments/assets/e7cf7122-b9c1-4ee9-8fd5-1c7f5a80169e)


3. This may take few minutes to create the image. To see the AMI, click AMI under images section in EC2 console

![image](https://github.com/user-attachments/assets/ab357ebb-93ad-4300-9404-0fb3732c0034)


We are done with our new Amazon Machine Image for now, we can now move on to setting up our auto scaling security group.

---

# CREATE A Auto Scaling SECURITY GROUP :
Before we get into setting up our Launch Template we will need to setup a special Security Group for our Auto Scaling Group. A security group provides instance (virtual machine) level protection.
1. Give Security group name and select the VPC and don't provide any inbound and outbound rules as of now. We will create it later.

![image](https://github.com/user-attachments/assets/5524f5b4-24e6-4b11-850a-995ad5ba7014)


---

**CREATE A LAUNCH TEMPLATE :**

Below is the architecture which we are creating now. Let's proceed!

![image](https://github.com/user-attachments/assets/48e4d56e-7615-41e6-ab6f-95b0adcbe79e)


There are 3 main components to EC2 Autoscaling on AWS
**Launch Template** - It is one of the feature in EC2, it allows to create the template with AMI, Instance type, storage and network which is equal to creating the EC2 Instances.
**AUTOSCALING GROUPS**: Using Autoscaling group, we can scaling up or down the instances.
**SCALING POLICIES** : There are policies or types in respect to scaling. Eg., target scaling policy, schedule scaling policy, on demand scaling

The main usage of auto-scaling is when you don't know the traffic pattern of your workload, auto scaling group helps in increase or decrease the instances automatically .
Set the values as below for the launch template

1.Under the "Services" select EC2.

2. In the left navigation pane, find "Instances" and select Launch Templates.

3. Now select Create launch template.
This takes you to the "Create launch template" page, starting with the "Launch template name and description":
a. Launch template name: [Your Initials]-scaling-template
b. Template version description: This is optional
c. Auto scaling guidance: Check the box to provide guidance

"Launch Template Contents" defines the parameters for the instances in the Auto Scaling group:
a. Amazon machine image (AMI): Select "My AMIs" and "Owned by me". In the drop down search by typing your initials and select the custom AMI you just created [Your Initials]_Auto_Scaling_Webhost. (The new AMI you just created may already be selected)
b. Instance type: t2.micro
c. Key pair (Login): Select the Key Pair you created in the first lab, it is most likely call [Your Initials]-KeyPair
d. Networking Settings:
Subnet: Don't include in launch template
Firewall (security groups): Select "Select existing security group" and then select the security group you created in the first part of this lab named [Your Initials] - Auto Scaling SG
e. Configure storage: Leave as default
f. Resource tags: None
g. Advanced Details: IMPORTANT: Select the arrow to expand "Advanced details" and under "Detailed Cloudwatch monitoring" select Enable. Leave everything else as the default.
Here, you are enabling CloudWatch Detailed monitoring. By default, your instance has basic monitoring in 5-minute intervals for the instances. After you enable detailed monitoring CloudWatch will monitor the instances in your auto scaling group in 1-minute intervals. This will allow the auto scaling group to respond quicker to changes in the group.

![image](https://github.com/user-attachments/assets/d2e0d1ac-3405-42e0-b4ad-532c2b692867)

![image](https://github.com/user-attachments/assets/89eaeb52-0d43-481c-a551-b1e7d1dd3104)


![image](https://github.com/user-attachments/assets/64962e27-92df-4dd9-9dd8-d4b94332ad8a)

---

# SETUP AUTO SCALING GROUP USING LAUNCH TEMPLATE :

You have created the launch templates with the paramaters for your instance. Now, you can create the autoscaling group using launch template.
Here, Autoscaling group uses the launch templates configuration parameters to create the instances.

1. Choose Launch Template under EC2 Auto Scaling group and provide group name and select the Launch Template which you have created "Jana-Scaling-template"

![image](https://github.com/user-attachments/assets/02100b33-c065-48e6-b4af-81f12aec54ed)

2. Choose Instance Launch Options, select the VPC as Default VPC and Availability zone. Here we are deploying the instances in two AZs. You can select the AZs as per your need.

![image](https://github.com/user-attachments/assets/793de066-98e0-458b-a32e-6c8823fb6c0c)


3. Create the Application Load Balancer which balances the load across Availability Zones and create the target group.
Your application loadbalancer route traffic to the target group. Your instances are running under this target group.

![image](https://github.com/user-attachments/assets/666de12d-853d-4b67-83bb-fc02e6fbee9d)


4. Configure Scaling Policy.
Desired Capacity -1 [Desired numbers of instances]
Min Desired Capacity - 1 [Minimum numbers of instances]
Max Desired Capacity - 2 [Maximum numbers of instances]
Review and Create it.

![image](https://github.com/user-attachments/assets/91210182-e8f7-48a8-89f7-51e28fd5d1b1)


---

# CREATE LOAD BALANCER SECURITY GROUPS:
When our load balancer was provisioned it was setup with the default security group in our VPC. To allow access to the load balancer via the public DNS, we will need to create and attach a security group to allow inbound traffic on port 80 from the internet.

As a best practice, we will also create an outbound rule that restricts outgoing traffic from the load balancer to only be sent to hosts using the Auto Scaling Security Group.

Within the console Under "Services" search and select EC2. On the EC2 page under the "Network & Security" heading in the left-hand menu select Security Groups. You should see other security groups, including the security group for your web server named [Your Initials]-EC2-Web-Host - Website Security Group. Click on the Create security group button.

**Basic details:**
a. Security group name: [Your Initials]-SG-Load-Balancer
b. Description: [Your Initials]-SG-Load-Balancer
c. VPC: Select your VPC (Most likely the Default VPC)

**Inbound rules:**
a. Click on the Add rule button
b. Type: HTTP
c. Source: Custom: [Input your public IP address followed by a /32] (You can find your local IP by searching What is my IP. )

**Outbound rules:**
a. Find the "All traffic" rule and click on Delete to remove the rule. (All Outbound rules should now be removed)
b. Click on the Add rule button
c. Type: HTTP
d. Under "Destination" select Custom and in the field select your [Your Initials]-Auto Scaling SG as the "Destination". Hint: start by typing sg to get the Security Group list.
f. You security group configuration should look similar to the image below. Select Create security group when finished.

![image](https://github.com/user-attachments/assets/1595796e-ffd4-4fb1-ad4c-83cd603fa93b)


---

# Attach your new Load Balancer Security group to your Load Balancer:
 a. On the EC2 service page left side menu find "**Load Balancing**" and select Load Balancers. Select the load balancer you created. Make sure the State is "**Active**".
 b. Under the "**Description**" tab scroll down to the "**Security**" section and click on Edit security groups.
 c. Select the box to the left of your new load balancer sg named [Your Initials]-SG-Load-Balancer
 d. Make sure you also un-select any other security group and then click on the Save button.

![image](https://github.com/user-attachments/assets/f007589a-3db0-476f-91d2-ea60bab63592)

![image](https://github.com/user-attachments/assets/6022a6b9-c688-4b5a-8c23-0f57936caaca)

---

# Add Inbound Rule to the Auto Scaling Security Group
1. We will need to setup a rule to only allow traffic from the new Load Balancer Security Group to the Auto Scaling Security Group. This will be one of the layers of protection that will prevent our webhosts from being directly accessed from the internet.
 
 a. On the **EC2 service page** left side menu under **"Network & Security"** select Security Groups.
 b. Select your Auto Scaling Security Group: [Your Initials] - Auto Scaling SG
 c. Select the **Inbound Rules tab** and click on the **Edit inbound rules **button and then the **Add** rule button.
 d. From the "Type" drop down select HTTP. Under "Source" select Custom and in the field specify your [Your Initials]-SG-Load-Balancer as the "Source". Hint: start by typing sg to get the Security Group list. Now click on Save rules.

Your rule should now look similar to the image below.

![image](https://github.com/user-attachments/assets/5fec2b18-0e58-43aa-a3be-f35950d7d4cb)

![image](https://github.com/user-attachments/assets/f046a70f-3ef2-4fd6-9d85-d1b521a0dbd5)


2. We will now test to make sure your load balancer is working. There is currently only one instance (or target) running in the auto scaling group, but you should be able to access the website.

Return to your the load balancers page by selecting Load Balancers from the left hand menu. Under the "Description" tab copy the DNS name and paste it into a web browser. You should now see the website being loaded from your auto scaling group. Leave this page open, you will need it in the next step.

![image](https://github.com/user-attachments/assets/e4148bcc-e1e9-4a72-9818-ca639fe9241d)

![image](https://github.com/user-attachments/assets/7617fe68-a159-45af-ad42-0cf2514c5cb9)

---

# TESTING THE AUTOSCALING GROUP:
 Testing Your Auto Scaling Group and Load Balancer
Follow these steps to test your Auto Scaling setup and ensure it's working:

1. Access the Website via Load Balancer:
 - Open the DNS address of your Load Balancer in a browser.

2. Generate CPU Load:
 - On the website's front page, click Start CPU Load Generation. 
 - If the CPU load exceeds 25% for a sustained period, Auto Scaling will create new instances as per the launch template. 
 - You might need to click this link twice if the load doesn't reach the threshold initially.

3. Monitor New Instances:
 - Go to the Instances section in the EC2 Console and refresh periodically. Within a few minutes, a new instance should appear, automatically launched by Auto Scaling.
 - Select your instance and check the Monitoring tab for metrics like CPU Utilization.

4. Verify in the Auto Scaling Groups Page:
 - Visit the [Auto Scaling Groups page](https://console.aws.amazon.com/ec2autoscaling). 
 - Select your Auto Scaling group and check the Instance Management tab to view the current instance count and statuses. 
 - Use the Monitoring tab for detailed metrics, including group size and pending instances.
This ensures that your Auto Scaling Group and Load Balancer are functioning as expected.


![image](https://github.com/user-attachments/assets/7e9abd8d-8ae4-4e81-bac9-bfab24b8ca62)


![image](https://github.com/user-attachments/assets/0e255f57-b7e7-47f2-9749-52536c966b44)



Congratulations! You successfully created an AMI, included it in the setup your Launch Template, created an EC2 Auto Scaling Group behind an Application Load Balancer.
Remember, don't forget to delete all resources created and configured when you are done following the steps of this article.


If you've got this far, thanks for reading! I hope it was worthwhile to you.


Signing off!
Jana
https://linkedin.com/in/janarthanan-ravikumar%E2%98%81%EF%B8%8F-3674b8101

