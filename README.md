# Minecraft Server Setup on AWS EC2

Select the correct AWS region, in this case “US-west(oregon)”, this can be found in the drop down menu on the top bar to the left of the “settings” symbol.

Next in the search bar on the top left of the page type in “EC2” click the top result which will take you to the EC2 home page.

Once on the home page you will see a large orange button that says launch instance, you will click that in order to start creating the new instance for the minecraft server.

## Instance Setup

1. At the top of the page you will see a section called “Name and tags”, in that section enter the name that you want the instance to go by.
2. The section directly below that one is called “Application and OS images” for this part you are going to choose Amazon Linux as the AMI. Make sure that the Amazon Linux box is highlighted and the box below shows “Amazon Linux 2023 AMI” and the following box named “Architecture” is set to “64-bit(Arm)”.
3. The following section is called “Instance type”, you are going to want to change the instance type to make sure that it is set to “t4g.small”.
4. Scroll down to the “Key pair (login)” section where you will need to select “create new key pair” which can be found to the right of the section. You can name it anything and once you finish it will automatically download to your local machine.
5. Scroll down until you see the “Network settings” section, in the top right you need to click edit which will allow you to make the necessary changes.
   - Make sure that the VPC section at the top is set to the default VPC, this is indicated by the “(default)” tag that can be seen to the right of the box you choose the VPC in.
   - The next two sections are “Subnet” which can be set to no preference and “Auto-assign public IP” which needs to be enabled.
6. In the “Firewall (security group)” section, click the “Create security group” box. You are given the option to change the name and also leave a brief description.
7. Add two inbound rules that will allow connectivity:
   - Rule 1: Leave “Type”, “Protocol”, and “Port Range” as is. Change the “Source type” to “Anywhere”. Add a description if desired.
   - Rule 2: Click the “Add security group rule” at the bottom left side of the section. Set “Type” to Custom TCP, “Protocol” to TCP, “Port Range” to 25565, and “Source type” to “Anywhere”. Leave a brief description indicating it allows Minecraft connections.
8. Scroll down to the “Configure storage” section where you are going to select 8GB for root volume EBS. Then launch the instance.

## Connect to Your EC2 Instance

1. Open a terminal on your local machine and SSH into the EC2 instance:
   
   ```bash
   ssh -i /path/to/key/your-key.pem ec2-user@public-ip
   ```
   You are going to want to change the path to the correct one to access your key file and make sure you change the key file name to the correct one. After that go to you ec2 instances which can be reached via the “Instance” section on the side option on the ec2 page. Click on it can then click on the minecraft server instance. In the top middle of the page you will find the public ip which will be put at the end of the ssh command.


