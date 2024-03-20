# Honeypot_TPOT

A honeypot is a security mechanism used in cybersecurity to detect, deflect, or counteract attempts at unauthorized use of information systems. It typically consists of a computer, data, or network site that appears to be part of a legitimate network but is actually isolated and monitored, with the intention of attracting and observing malicious activity. The term "honeypot" comes from the idea of a pot of honey used to attract and catch bears. Similarly, in the digital realm, a honeypot is designed to lure attackers and malware, allowing security professionals to study their tactics, understand their methods, and gather information to better protect the real systems from such threats. Honeypots can be valuable tools for studying the behavior of hackers and developing strategies to defend against them.

## Prerequisites
- An AWS account
- Basic understanding of the [AWS console](https://aws.amazon.com/console/)
- Basic understanding of Github and git
- Gitbash terminal. [Here](https://gitforwindows.org/)

## Step 1: Create an instance; choose an Amazon Machine Image (AMI)
1. Open the [AWS Management Console](https://aws.amazon.com/console/) and navigate to the EC2 service.
2. Click on **Launch Instance**. Name it. I will call mine “honey_im_home”.
3. Select an AMI from the list based on your requirements. For this guide, we'll use the [Debian 11 AMI]( https://aws.amazon.com/marketplace/pp/prodview-l5gv52ndg5q6i#pdp-overview).

<img src= "https://github.com/ArchAndrew/Honeypot_TPOT/blob/main/1debianami.png" alt="image description" style="width:950px;">

## Step 2: Configure the Instance
Configure the following settings for your EC2 instance:

- Instance Type: Determine the CPU, memory, and storage resources for your workload. Choose **t2.xlarge**
- Network Settings: Specify the subnet and security group for your instance. Leave this as default.
- **Key Pair:** Create or use an existing key pair for SSH authentication. I will call my key “mypot”. This key when created will be downloaded to you downloads folder.
- **Configure Storage:** Specify 128gb gp2. This is the requirement for the TPOT, the storage is needed for log collection.
Your screen should look like this:
<img src= "https://github.com/ArchAndrew/Honeypot_TPOT/blob/main/2instance1.png" alt="image description" style="width:750px;">
<img src= "https://github.com/ArchAndrew/Honeypot_TPOT/blob/main/3instance%20storage.png" alt="image description" style="width:750px;">



Additional configuration options include storage and tags.


## Step 3: Launch the Instance
Click on *Launch* after configuring your settings. The instance will take a few minutes to launch.
<img src= "https://github.com/ArchAndrew/Honeypot_TPOT/blob/main/4instancerunning.png" style="width:750px;">

## Step 4: Connect to your Instance
1.	Connect to your instance using SSH (Linux). Click on instance id to the right of screen click connect. Then click “ssh client”. 
2.	Open Gitbash terminal, right click and run as administrator. Navigate to your path where your key pair is i.e. “cd Downloads”.
3.	Head back to the AWS console and copy the code from line 3, paste that into the terminal, enter that and then do the same thing for line 5.
4.	You will be asked to continue. Enter “yes”

<img src= "https://github.com/ArchAndrew/Honeypot_TPOT/blob/main/6sshin.png" style="width:750px;">

## Step 5: Update and upgrade OS
We want to ensure that the proper patches are up to date on this instance, so we will run this command in the terminal first. 
```bash
sudo apt update && sudo apt upgrade
```

## Step 6: Install Git
1.	We will need git to pull files from github so let’s run this command.
```bash
sudo apt install git
```
2.	Now we need to download the software for TPOT. To do this we will navigate to github to get the link for the honeypot. Once you get there click the green button labeled “Code” and copy the .git link. Repo is here [repository](https://github.com/telekom-security/tpotce).
3.	This link will be used alongside the “git clone” command in our terminal like so:
```bash
git clone https://github.com/telekom-security/tpotce.git
```

## Step 7 Navigate to folder: 
Once the repo has been successfully cloned, in the terminal enter “ls” to view the folder “tpotce” and “cd” into it and “ls” again like so:

<img src= "https://github.com/ArchAndrew/Honeypot_TPOT/blob/main/9tpotfile.png" style="width:750px;">

## Step 8 Install bash script:
We need to run the bash script “install.sh” to do run this command:
```bash
sudo ./install.sh --type=user  
```
You will eventually see this screen enter “y”

<img src= "https://github.com/ArchAndrew/Honeypot_TPOT/blob/main/11tpotverify.png" style="width:750px;">

## Step 9 Choose TPOT Edition:
1.	You will be taken to a dialog box to choose your edition. Choose standard and press enter. 
2.	You then will create a username and password. After this installation will continue.

<img src= "https://github.com/ArchAndrew/Honeypot_TPOT/blob/main/12standardpot.png" style="width:650px;">
<img src= "https://github.com/ArchAndrew/Honeypot_TPOT/blob/main/13username.png" style="width:650px;">
<img src= "https://github.com/ArchAndrew/Honeypot_TPOT/blob/main/14pw.png" style="width:650px;">


## Step 10 Reconfigure Security Group:
Once the installation is complete you will notice that you will be kicked off the server. This is a good thing as TPOT has made some new configuration to your security group and now for security purposes you are no longer allowed to connect to the server via ssh port 22. So, here’s how we fix this:
1.	Go back to the AWS console. Go EC2>click Your instance id>security

<img src= "https://github.com/ArchAndrew/Honeypot_TPOT/blob/main/15security.png" style="width:750px;">

2.	Click on your security group and then edit inbound rules.

<img src= "https://github.com/ArchAndrew/Honeypot_TPOT/blob/main/16sg.png" style="width:750px;">

<img src= "https://github.com/ArchAndrew/Honeypot_TPOT/blob/main/17editin.png" style="width:900px;">

3.	Change ssh to *custom TCP* port range for port *64297*, for source choose *my ip* and for description label it whatever you like, I named mine “This is for web portal”. *Note* that in the following image my port is different, however I’ve found that the above configuration works best.
-	Now click, *add rule* choose *custom TCP* port range for port *64295*, for source choose *my ip* and for description I labelled it “This is to SSH in” 
-	Lastly, add another rule. This is also *custom TCP*, for the port range *1-64000*, for source choose *anywhere ipv4* and for description I put “For the bad guys”.

<img src= "https://github.com/ArchAndrew/Honeypot_TPOT/blob/main/sgmod.png" style="width:900px;">

## Step 11 SSH with new port:
-	Now we initially used the code in line 5 of the ssh client from AWS console to ssh into the instance, however since our configuration we must enter in through our new port. Therefore, we will need to make a modification to that code.  
-	Go to your terminal and press the up arrow the last code that you entered into it (which should be line 5 should populate. On the end of it type *-p 64295* (this is our ssh) like so:

<img src= "https://github.com/ArchAndrew/Honeypot_TPOT/blob/main/18newssh.png" style="width:750px;">

## Step 12 Enter path:
Let’s again, ensure that we can access our “tpotce” folder

<img src= "https://github.com/ArchAndrew/Honeypot_TPOT/blob/main/19tpotfiles2.png" style="width:750px;">

## Step 13 Enter web portal:
1.	Go back to the console, click instance id and copy your *public IPv4 address*
2.	Open a new window and in the browser enter: *https://* then paste your address behind it.
3.	Then behind your address type “:” and then the port number you configured for the web portal in your security group. Which is *64297* and press enter.
4.	You will see a warning message. Click advanced and proceed. After that you will be prompted to enter the username and password that you created.

<img src= "https://github.com/ArchAndrew/Honeypot_TPOT/blob/main/20ipv4.png" style="width:650px;">
<img src= "https://github.com/ArchAndrew/Honeypot_TPOT/blob/main/21Signin.png" style="width:650px;">
<img src= "https://github.com/ArchAndrew/Honeypot_TPOT/blob/main/22enterweb.png" style="width:650px;">


## Step 14 Welcome to TPOT:
You will see the homepage where you can select *Attack Map* and view which attacks are attacking your server and from which region the attack is coming from. This is a good way to create a “sitting duck” while you watch and study your attacker’s patterns in real-time. 

<img src= "https://github.com/ArchAndrew/Honeypot_TPOT/blob/main/23webportal.png" style="width:750px;">
<img src= "https://github.com/ArchAndrew/Honeypot_TPOT/blob/main/24rta.png" style="width:750px;">
<img src= "https://github.com/ArchAndrew/Honeypot_TPOT/blob/main/25rta.png" style="width:750px;">

## Step 15 Tear down:
When done, terminate the instance to avoid charges.

1. Open the AWS Management Console and navigate to the EC2 service.
2. Select the instance, click on **Actions > Terminate**.

*Note: You'll be charged for the running time, even if not actively using the instance.*

## This concludes this project. Well done! :clap:


