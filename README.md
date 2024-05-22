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
   You are going to want to change the path to the correct one to access your key file and make sure you change the key file name to    the correct one. After that go to you ec2 instances which can be reached via the “Instance” section on the side option on the ec2    page. Click on it can then click on the minecraft server instance. In the top middle of the page you will find the public ip         which will be put at the end of the ssh command.
2. After this, we are going to be running a script to handle the setup of the server itself. To start, we are going to make a file 
   called `setup-minecraft.sh` which can be done with the following command:

   ```bash
   touch setup-minecraft.sh
   ```
3. Now that the file is made we now need to use a text editor to add the information that will allow the script to work. You are        going to enter
   ```bash
   “vim setup-minecraft.sh”
   ```
   Note that you don't have to use vim and instead use anyone you feel comfortable with.
   
4. You are now going to add the script shown below with one change. You need to and the link to the .jar file that will allow the       server to work. This can be found at this website “​​https://mcversions.net/download/1.20.1”. Add that to the part of the script       where you see “MINECRAFTSERVERURL=”. Make sure that there is no space between the “=” sign and the url you copied in.
```bash
   #!/bin/bash

# *** INSERT SERVER DOWNLOAD URL BELOW ***
# Do not add any spaces between your link and the "=", otherwise it won't work. EG: MINECRAFTSERVERURL=https://urlexample


MINECRAFTSERVERURL=


# Download Java
sudo yum install -y java-17-amazon-corretto-headless
# Install MC Java server in a directory we create
adduser minecraft
mkdir /opt/minecraft/
mkdir /opt/minecraft/server/
cd /opt/minecraft/server

# Download server jar file from Minecraft official website
wget $MINECRAFTSERVERURL

# Generate Minecraft server files and create script
chown -R minecraft:minecraft /opt/minecraft/
java -Xmx1300M -Xms1300M -jar server.jar nogui
sleep 40
sed -i 's/false/true/p' eula.txt
touch start
printf '#!/bin/bash\njava -Xmx1300M -Xms1300M -jar server.jar nogui\n' >> start
chmod +x start
sleep 1
touch stop
printf '#!/bin/bash\nkill -9 $(ps -ef | pgrep -f "java")' >> stop
chmod +x stop
sleep 1

# Create SystemD Script to run Minecraft server jar on reboot
cd /etc/systemd/system/
touch minecraft.service
printf '[Unit]\nDescription=Minecraft Server on start up\nWants=network-online.target\n[Service]\nUser=minecraft\nWorkingDirectory=/opt/minecraft/server\nExecStart=/opt/minecraft/server/start\nStandardInput=null\n[Install]\nWantedBy=multi-user.target' >> minecraft.service
sudo systemctl daemon-reload
sudo systemctl enable minecraft.service
sudo systemctl start minecraft.service

# End script
```
5.After that you want to change the permissions to make the “setup-minecraft.sh” file we made executable. This can be done with the   following command... 
  ```bash 
  “chmod +x setup_minecraft.sh”.
  ```
6.Now that the file is executable we can now run it using...
  ```bash
  “sudo ./setup-minecraft.sh”
  ```
  Once its ran you should see all the items in the script being download and once finish you should have access to the terminal 
  again. If you don't have access to the terminal due to the script not finishing something went wrong. Use the...
  ```bash
  “sudo systemctl status minecraft.service”
  ```
  command to check for any errors and then go back over the tutorial to see if any steps were completed incorrectly.








