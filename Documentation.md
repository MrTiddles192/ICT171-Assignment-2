```
   _____   _                 _           _   _         _                                           
  / ____| | |               | |         | | (_)       | |                                          
 | (___   | |_   _   _    __| |  _   _  | |  _   ___  | |_       ___   _ __     __ _    ___    ___ 
  \___ \  | __| | | | |  / _` | | | | | | | | | / __| | __|     / __| | '_ \   / _` |  / __|  / _ \
  ____) | | |_  | |_| | | (_| | | |_| | | | | | \__ \ | |_   _  \__ \ | |_) | | (_| | | (__  |  __/
 |_____/   \__|  \__,_|  \__,_|  \__, | |_| |_| |___/  \__| (_) |___/ | .__/   \__,_|  \___|  \___|
                                  __/ |                               | |                          
                                 |___/                                |_|
```

# Assignment 2 - Cloud Server Project Documentation

This documentation will contain a list of steps taken to deploy the cloud server, along with the development of the script on the website.

# Deploying an Amazon Web Services EC2 Instance
The first step required is the launching of a new Amazon EC2 Ubuntu instance.
Go to [AWS EC2 Console](https://console.aws.amazon.com/ec2/) and log in using your existing account.
From the EC2 Console, in the navigation pane, click on "Instances" and then "Launch Instances" on the top right. 

# Launching an Instance
Follow the steps below for launching a Ubuntu free tier instance:
* Name the instance accordingly - *Studylist.space web server*
* Pick the Linux Distribution as Ubuntu and select **Ubuntu Server 24.04 LTS (HVM)**
   * Version 24.04 LTS is used instead of 22.04 LTS as it has improved security and better long-term support.
* Choose the *t2.micro* instance type - ensure settings match the image below.
![Example](/Images/instance-settings.png)

* Create a new Key Pair - name it LoginKey and click "Create key pair" - download the Key Pair.
* Click "Create Security Group" and select the following:
   * Allow SSH traffic from - set IP Address to Anywhere (0.0.0.0/0).
   * Allow HTTPS traffic from the internet.
   * Allow HTTP traffic from the internet.
* Configure storage as `1x 30GiB gp3 Root volume, 3000 IOPS, Not encyrpted`.
* Once the above steps are complete, click "Launch Instance".
* Once done, click "View All Instances" and check the instance's state shows "Running".
   * To preserve free tier hours, stop the instance until ready to proceed to the next step.
   * To stop: Select the instance, click instance state and 'Stop Instance'.

# Accessing the Instance
On the Amazon EC2 Instances page, in the list of instances, select the web server and in the bottom pop up, note down the Public IPv4 Address for later use.
To access the instance via terminal access, use SSH via Windows PowerShell.
Go to the directory containing 'LoginKey.pem' and in the directory bar type in 'powershell' to launch Windows PowerShell.
Paste the below command, replacing <IP_ADDRESS> with the one gathered earlier.

      ssh -i ./LoginKey.pem ubuntu@<IP_ADDRESS>
