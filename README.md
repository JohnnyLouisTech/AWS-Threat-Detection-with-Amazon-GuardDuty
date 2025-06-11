# 🚧 Deploy an Insecure Web App on AWS

In this step, we'll deploy an intentionally vulnerable web application on an EC2 instance. We'll use [DVWA (Damn Vulnerable Web App)](https://github.com/digininja/DVWA) or [OWASP Juice Shop](https://owasp.org/www-project-juice-shop/) for hands-on learning.

![image](https://github.com/user-attachments/assets/1f391feb-8363-4850-bf7f-30098db53bdb)

---

## ✅ Prerequisites
- AWS account
- IAM user with EC2 access
- SSH key pair for login

---

## 🖥️ Launch an EC2 Instance

1. Go to the EC2 Dashboard.
2. Launch a new **Ubuntu 22.04 LTS** instance.
3. Choose **t2.micro** (Free Tier eligible).
4. Allow the following ports in your **Security Group**:
   - TCP 22 (SSH)
   - TCP 80 (HTTP)
   - TCP 3000 (Juice Shop)

---

## 🔧 Install DVWA (Option 1)

sudo apt update && sudo apt install apache2 mariadb-server php php-mysqli git -y
sudo systemctl start apache2 mariadb
git clone https://github.com/digininja/DVWA.git
sudo mv DVWA /var/www/html/dvwa
sudo chown -R www-data:www-data /var/www/html/dvwa
sudo systemctl restart apache2

---

## 🔧 Install Juice Shop (Option 2)
curl -sL https://deb.nodesource.com/setup_16.x | sudo -E bash -
sudo apt install -y nodejs git
git clone https://github.com/juice-shop/juice-shop.git
cd juice-shop
npm install
npm start

---
## ✅ Verify Deployment
Make sure the app is publicly accessible and note your EC2's public IP. You’re now ready to simulate attacks!

---

### 📄 `setup/simulate-attacks.md`

# ⚔️ Simulate Attacks on the Web App

Now that your vulnerable web app is running, simulate malicious activity to trigger GuardDuty findings.

---

## 🚨 WARNING

This lab is **for educational purposes only**. Do NOT run these scans or attacks outside your test EC2 instance.

---

## 🧪 Port Scanning (Reconnaissance)

From a Kali VM or your local machine:
bash: nmap -sS -T4 -Pn <EC2-Public-IP>

## 🔍 Brute Force Login
Use tools like Hydra to simulate login brute force:

hydra -l admin -P /usr/share/wordlists/rockyou.txt <EC2-Public-IP> http-post-form "/dvwa/login.php:username=^USER^&password=^PASS^:Login failed"

## 🐍 SQL Injection Test (Manual)
In DVWA, set security level to “Low” → try:
username: admin' OR 1=1 --
password: (anything)

## 🧱 Fake Malware Callback (GuardDuty Sample)
(Optional if no findings show up):

aws guardduty create-sample-findings --detector-id <detector-id>

## Deploy the Web App

Log in to the AWS Management Console as your IAM Admin user.
Make sure you’re in the AWS region closest to you.
Head to the CloudFormation console.

![image](https://github.com/user-attachments/assets/4ba4d6d5-d0da-4b69-a765-fdbdbbb13cbb)

## Monitor Stack Creation

Select Create stack from the top right corner of the console.
Select With new resources (standard).
Select Select an existing template.
Select Upload a template file.

![image](https://github.com/user-attachments/assets/801c3b8a-d093-4ee7-9c33-1ec0516d13c3)

Upload this CloudFormation template!
👉 CloudFormation template
Select Select file.
Select the CloudFormation template you’ve downloaded.
At the bottom of the page, choose Next.
For the Stack name, you can enter NextWork-GuardDuty-project-yourname
Make sure to replace yourname with your name. Your stack name must be unique across your entire Region. Change your stack name again if it’s already taken.

![image](https://github.com/user-attachments/assets/3025501c-2ffd-4b3f-a6b4-9b40b555f468)

We can skip the Parameters section.

![image](https://github.com/user-attachments/assets/8e91bc04-29c5-4053-bfbb-29db8c304d71)

Select Next at the bottom of the page.
We can skip the Tags and Permissions sections.

![image](https://github.com/user-attachments/assets/a8509992-d536-44cc-8a60-13e3dba097e1)

In the Stack failure options section, select Roll back all stack resources for the first setting i.e. Behaviour on provisioning failure.

![image](https://github.com/user-attachments/assets/960c3a84-d016-4121-9cda-ff55c78ee6ee)

Select Delete all newly created resources for the second setting on Delete newly created resources during a rollback.

Scroll to the bottom of the page. Check the box in the blue pop up banner that says I acknowledge that AWS CloudFormation might create IAM resources.

Select Next at the bottom of the page again.
Now you’re on the final Review page. Let’s make sure all our settings are correct in the checklist below:
The Prerequisite — Prepare template heading says “Template is ready”There is a Template URL.The Stack description says the following:
This template creates an insecure web app for NextWork's project on threat detection and GuardDuty!

Your Stack name is “NextWork-GuardDuty-project-[yourname]”

There are 5 items in the Parameters section.

![image](https://github.com/user-attachments/assets/33383b32-1a24-4c25-807f-d5a0907b45ce)

Under Stack failure options, both settings are activated.

![image](https://github.com/user-attachments/assets/a8133aa6-f1eb-4354-8030-3f072e8f291c)

Select Submit at the bottom of the page.
Woohooo! Off it goes.

![image](https://github.com/user-attachments/assets/fff6b3b4-b928-4cd1-8ba3-d12588941cb7)


Wait as CloudFormation goes through your template and tries to deploy your resources.
The deployment takes up to 10 minutes, so while we wait, let’s explore what we’re deploying!
Breaking Down Your CloudFormation Template

The CloudFormation template you’re deploying creates 27 resources that makes up a web app!

This web app is a copy of a very popular web app for security training, called the OWASP Juice Shop.

*Click on the URL under output to see your work.

![image](https://github.com/user-attachments/assets/c8534ea7-7d17-472c-9cb2-d5ece1b215d9)

Once we’re done deploying it, it’s going to look like this:

![image](https://github.com/user-attachments/assets/04058c43-502b-4595-af7f-a7392f16f8b4)

Validate Your Stack’s Deployment
Head back into the CloudFormation console and refresh your page.
Hooray! Your stack’s status should say CREATE_COMPLETE now.

![image](https://github.com/user-attachments/assets/ccedc7af-f87e-4be0-8336-06dbb04583b9)


## 🔑 Step #2
Now that we’ve deployed our web app, let’s try opening it.

Then, we’ll enter 😈 hacker mode 😈 and see whether we can hack our way into the web app’s admin portal.

In this step, you’re going to:

Open your running web app.
Use a hacking technique to bypass logging into the admin portal.
Locate JuiceShopURL

In your CloudFormation console, stay in your stack’s window.
Select the Outputs tab of your deployed stack.
Find the JuiceShopURL output.
Verify Your Web App

Right-click on your JuiceShopURL to open it in a new tab.
Select Dismiss to close the pop up.
You should see the web app’s landing page.

![image](https://github.com/user-attachments/assets/4a9ceed2-1c43-4c15-8f54-2a3232a28ffb)

# Log in to Your Web App

At the top right corner, select the Account icon and then select Login.

![image](https://github.com/user-attachments/assets/55082de8-0750-463e-b6ed-33fa3faa2765)

On the login page, enter the following ~mysterious~ string in the email field: ‘ or 1=1; —

Enter anything in the password field. This won’t matter — since the SQL injection is set up to accept any password.

![image](https://github.com/user-attachments/assets/8f5ed194-d936-46c0-94cc-a3e68851815a)

Select Log in.
Great, you’ve just logged in! There’s a green banner that says You successfully solved a challenge: Login Admin (Log in with the administrator’s user account.)

![image](https://github.com/user-attachments/assets/ca946956-d967-4cbe-b223-5900f328a3f1)

## 👾 Step #3
Exploit the Web App
Having bypassed the login page, we’ll stay in 😈 hacker mode 😈 and do our next move — stealing credentials to the web app host’s AWS environment!

Yup, you guessed it. You’re the web app host, since you’re the one who deployed this web app.

In this step, you’re going to:
Steal AWS credentials.
Access the stolen credentials.
Run Malicious Code
Select the Account option in the top right-hand corner.

![image](https://github.com/user-attachments/assets/693a8457-ba31-41c2-907f-4fa7344806b3)


Select your admin@juice-sh.op email.
This takes you to the admin’s profile page!


