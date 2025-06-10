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

```bash
sudo apt update && sudo apt install apache2 mariadb-server php php-mysqli git -y
sudo systemctl start apache2 mariadb
git clone https://github.com/digininja/DVWA.git
sudo mv DVWA /var/www/html/dvwa
sudo chown -R www-data:www-data /var/www/html/dvwa
sudo systemctl restart apache2

---

🔧 Install Juice Shop (Option 2)

