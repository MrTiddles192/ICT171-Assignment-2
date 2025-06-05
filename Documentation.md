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
Created by Tom Dutch 34089911 for ICT171 Assignment 2: Cloud Server Project

This documentation will contain a list of steps taken to deploy the cloud server, along with the development of the script on the website.

Website located at [studylist.space](https://studylist.space)

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

  <img src="/Images/instance-settings.png" width="500">

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

When prompted with "Are you sure you want to continue connecting (yes/no/[fingerprint])?", type `yes` and hit enter.
Your CLI output should match below if successfully connected.

<img src="/Images/SSH.png" width="400">

# Misc Configuration
## Updating Packages
For good practice, upgrade all system packages using the following commands:

      sudo apt update
Then:

      sudo apt upgrade -y

Once complete, all system packages should be up to date.

## Enabling Ubuntu Firewall
For security, it's a good idea to enable Ubuntu Firewall to minimise vulnerability.
Run the following commands:

      sudo ufw allow ssh
Then:  

      sudo ufw allow http
Then:  

      sudo ufw allow https
Finally:

      sudo ufw enable

Verify the correct setup of ubuntu firewall by running the below command:

      sudo ufw status

The output of this command should match the below.

<img src="/Images/UFW.png" width="400">

# Installing Apache Web Server
Apache web server is required to host the web page and can be installed by the following steps.

      sudo apt install apache2 -y

To verify installation, in your own internet browser, navigate to `http://<IP_ADDRESS>/`

This should load the Apache2 Default Page shown below.

<img src="/Images/apache2-default-page.png" width="400">

# Setting up DNS Record
Log into [Namecheap](namecheap.com) open the 'Dashboard' tab when hovering over the username.
Next to the domain name 'studylist.space', click 'MANAGE'. Navigate to the 'Advanced DNS' Tab.
Under 'Host Records' create the following records:
|Type|Host|Value|TTL|
|----|----|-----|---|
|A Record|www|<IP_ADDRESS>|Automatic|
|CNAME Record|@|www.studylist.space|Automatic|

# Setting up HTTPS
HTTPS must be set up for secure connection to the website. This will be set up using the free service called Let's Encrypt.
Run the following commands once logged into server via SSH.

Install Certbot to set up SSL/TLS certificate.

      sudo snap install --classic certbot
This command will take several minutes due to the limited bandwidth of the t2.micro instance.
Once complete, you should see the image below as output.

<img src="/Images/certbot.png" width="400">

Then run the below command to allow certbot to be run:

      sudo ln -s /snap/bin/certbot /usr/bin/certbot
To set up certbot, run:

      sudo certbot --apache
When prompted for an email, hit 'Enter' and enter the domain name `studylist.space`. 
The following output should be displayed.

<img src="/Images/certbot-output.png" width="400">

Once complete, SSL should be set up and HTTPS should work. Navigate to [https://studylist.space](https://studylist.space) to verify the operation of a HTTPS connection.

## That is the setup complete

# Script Documentation
## saveNotes()
This script allows the user to save any text written in the note taking text box onto their device. This is done using Javascript.
The code below outlines the code used.
How the code works:
   1. Retrieves the content from the text area by ID. Text box is identified as 'notes'.
   2. Stores the content in a blob - this is an object that is used to store usable data such as text and images.
   3. Creates a link for the browser to download the file which points to the blob to be downloaded.
   4. Triggers a 'click' that link so the browser downloads the file.
   5. Revokes the object URL and object memory to free up browser memory.
```
function download_notes() {
	const text = document.getElementById('notes').value; // Retrieve content from element 'Notes'
	const blob = new Blob([text], { type: 'text/plain'}); // Convert to blob for transfer
	const link = document.createElement('a'); // Creates document
	link.download = 'notes.txt'; // Indicates where browser can download
	link.href = URL.createObjectURL(blob); // URL points to blob object for download
	link.click();
	URL.revokeObjectURL(link.href); // Free memory
	}
 ```

## toggleDarkMode()
This script toggles a dark mode feature on the website that users can use to change the presentation of the website for ease of viewing.
This script essentially uses if statements to activate dark mode.
   1. When the button is clicked, the dark-mode class on the <body> element is activated.
   2. If dark-mode is already present, it will remove it and switch to light mode.
   3. If dark-mode is not present, it will add it and switch to dark mode.
   4. Updates the button to say Light/Dark mode depeneding on the text already there.
```
function toggleDarkMode() {
  document.body.classList.toggle("dark-mode"); // Toggles the CSS class of 'dark-mode' when the script is executed.

  var button = document.getElementById("darkModeBtn");
  button.textContent = document.body.classList.contains("dark-mode") // When clicked, it adds the 'darkmode' CSS code into the body of the HTML, switching it to the dark theme.
    ? "Light Mode" // If it says Dark Mode in button, switch txt to Light Mode
    : "Dark Mode"; // If it says Light Mode in button, switch txt to Dark Mode
}
```
