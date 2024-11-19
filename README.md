# DevOps-Challenge
Step 1:
First, I entered the command aws configure in the terminal to set up my AWS CLI. I configured my IAM credentials by providing the AWS_ACCESS_KEY_ID and AWS_SECRET_ACCESS_KEY. These credentials were used to authenticate the Terraform AWS provider.

Next, I navigated to the project directory using: "cd C:\Users\gmmba\OneDrive\Desktop\Terraform\Chat-App-using-Socket.io-master\public\js"
In this directory, I created a main.tf file to start defining my Terraform configuration.

Terraform Configuration

Provider:
I specified AWS as the provider and set the region to work in.
VPC:
-I defined a Virtual Private Cloud (VPC) with a CIDR block of 10.0.0.0/16.
Subnets:
-Two public subnets (public_a and public_b) were created in different availability zones.
-The map_public_ip_on_launch attribute was set to true to ensure instances launched in these subnets receive public IP addresses.

Internet Gateway:
-An Internet Gateway (IGW) was created and attached to the VPC to enable internet connectivity.

Route Table and Associations:
-A route table was configured with a default route (0.0.0.0/0) pointing to the Internet Gateway.
This route table was then associated with both public subnets.

Initialization and Deployment
After defining the configuration, I initialized the Terraform working directory using: "terraform init"
I previewed the resources Terraform would create with: "terraform plan"
Finally, I applied the configuration to create the VPC and subnets using: "terraform apply"
I confirmed the changes by typing yes when prompted.

Step 2:
In this step, I set up variables for the instance details and deployed two EC2 instances using the networking infrastructure I created in Step 1.

What I Did:
Defined Variables:
-I created a variable ami_id to define the Amazon Machine Image (AMI) ID that the EC2 instances use.
-I added a variable instance_type with a default value of t2.micro to specify the instance type.
-I also defined a variable VPC_GABBY to store the ID of the VPC created earlier.
Set Up a Security Group:
-I created a security group named instance_sg to control access to the instances.
-This security group allows SSH access on port 22 from all IPs (0.0.0.0/0) so I can connect to the instances.
-I also included a default egress rule to allow all outbound traffic.

Provisioned EC2 Instances:
I deployed two EC2 instances:
-Instance A (web_a) is in the public_a subnet (Availability Zone us-east-2a).
-Instance B (web_b) is in the public_b subnet (Availability Zone us-east-2b).

Both instances:
-Use the AMI ID defined in the ami_id variable.
-Have an instance type of t2.micro (or the value I set in instance_type).
-Are associated with the public subnets to ensure they have public IPs.
-Use the security group instance_sg for inbound and outbound rules.

Added Tags:
I tagged each instance for easy identification:
-web_a is tagged as "Instance A."
-web_b is tagged as "Instance B."

What I Achieved:
By the end of Step 2, I had two EC2 instances deployed in separate public subnets, each properly networked within the VPC I set up earlier. Both instances are publicly accessible and secured with a security group that allows SSH access, making them ready for use.

Step 3:
In this step, I set up an Application Load Balancer (ALB) to distribute incoming traffic evenly across multiple EC2 instances. This ensures high availability and scalability for the application.

What I Did:

Created a Security Group for the ALB:
-I defined a security group specifically for the ALB to allow HTTP traffic (port 80) from all sources (0.0.0.0/0).
-I also configured it to allow all outbound traffic for flexibility.

Set Up the Application Load Balancer (ALB):
-I created the ALB as an internet-facing load balancer, so it can handle traffic from external users.
-The ALB is associated with the security group I created and spans two public subnets (public_a and public_b) to ensure high availability.

Configured a Target Group:
-I created a target group to register the EC2 instances that will handle traffic routed by the ALB.
-I also added a health check configuration to monitor the status of the instances:
 -The health check path is /.
 -The health checks run every 30 seconds.
 -An instance is marked as healthy after 2 successful checks and unhealthy after 2 failed checks.

Linked EC2 Instances to the Target Group:
-I attached the two EC2 instances (web_a and web_b) to the target group so they can receive traffic from the ALB.

Added a Listener for HTTP Traffic:
-Finally, I configured a listener for the ALB to handle HTTP traffic on port 80.
=The listener forwards all incoming requests to the target group I created.

Why I Did This:
This setup ensures that incoming traffic is distributed between the two EC2 instances, improving the application’s availability and fault tolerance. The health checks help keep the system reliable by automatically excluding unhealthy instances from receiving traffic.

Step 4:
For this step, I needed to install Node.js on the EC2 instances created in Step 2 and configure the application to run. The goal was to ensure the application would always be accessible via the load balancer set up in Step 3. Here's a summary of what I did and the challenges I faced along the way.

What I Did

-Installed Node.js: I set up Node.js on the EC2 instances since it’s essential for running the application.
-Deployed Application Files: I transferred the application files from my local machine to the EC2 instances.
-Set Up Process Management: I used PM2 to manage the application, ensuring it would restart automatically in case of crashes or reboots.

Explanation of Playbook Tasks

Update and Install Dependencies: Updates the package list and installs any essential packages.

Add Node.js PPA: Downloads and installs the latest Node.js setup script.

Install Node.js: Installs Node.js on the server.

Copy Application Files: Copies your application files from your local machine to the /home/ec2-user/app directory on the server.

Install Dependencies: Installs application dependencies using npm install.

Install PM2: Installs PM2 globally to manage the Node.js application as a process.

Start Application with PM2: Start the application with PM2 so it continues running in the background.

PM2 Startup Configuration: Configures PM2 to auto-start on system reboots.

Enable PM2 Systemd Service: Ensures PM2 itself is managed as a system service.

Using Ansible
I attempted to use Ansible to automate these tasks, aiming for consistency and efficiency. My Ansible playbook included:
-Installing Node.js and its dependencies.
-Copying application files to the EC2 instances.
-Configuring PM2 to manage the application process.

Challenges

While I was able to install Ansible on my Windows machine, I ran into several roadblocks that prevented me from completing this step:
-I had trouble configuring the inventory file for Ansible to recognize the EC2 instances.
-Windows file path compatibility caused issues when transferring application files to the EC2 Linux environment.
-Debugging these problems proved challenging and time-consuming, and I couldn’t resolve them in the available time.

Outcome
Because of these challenges, I wasn’t able to finish configuring the application or automating the process with Ansible. Some tasks were partially completed, but the application setup wasn’t finalized.
