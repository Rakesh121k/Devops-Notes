# Deploying OpenCart on AWS EC2 (Amazon Linux 2)

Deploying OpenCart on an AWS EC2 Linux instance involves several steps, including instance creation, EBS encryption, and Elastic IP assignment. Here's a detailed, step-by-step guide to help you through the process:

---

## Step 1: Launch an EC2 Instance with Amazon Linux 2

### Access the EC2 Dashboard
Log in to your AWS Management Console and navigate to the **EC2 Dashboard**.

### Launch Instance

- **Click** on “Launch Instance”.
- **Choose an AMI**: Select **Amazon Linux 2 kernel**.
- **Choose an Instance Type**: `t2.medium` is sufficient and eligible for the Free Tier.
- **Configure Instance**: Accept defaults or adjust as needed.

<img width="613" alt="Image" src="https://github.com/user-attachments/assets/78ab6669-ba27-4923-a7f9-55aafbe04e77" />

<img width="555" alt="Image" src="https://github.com/user-attachments/assets/89241ba2-3992-49f3-adff-25c0f19ef091" />

<img width="339" alt="Image" src="https://github.com/user-attachments/assets/4cfbaec0-dbbb-45ad-8869-d4a6da4b586f" />

<img width="539" alt="Image" src="https://github.com/user-attachments/assets/2e714f57-61e3-4cca-96ff-593dae8cc7d5" />


### Add Storage

- Set the root volume size to at least **8 GB**.
- Enable **EBS Encryption** by selecting the option under the storage settings.

### Configure Security Group

- Allow **SSH (port 22)** from your IP.
- Allow **HTTP (port 80)** from anywhere.

### Review and Launch

- Review your settings and click on **"Launch"**.
- **Key Pair**: Create a new key pair, download it, and keep it secure.

### Advanced Details

Add the following **user data script** to install OpenCart automatically:

<details>
<summary><strong>Click to view user data script</strong></summary>

```bash
#!/bin/bash

# Update the system
sudo yum update -y

# Enable PHP 8.2 from amazon-linux-extras
amazon-linux-extras enable php8.2 -y
yum clean metadata

# Install Apache, PHP 8.2 and required extensions
sudo yum install -y httpd php php-mysqlnd php-cli php-common php-gd php-mbstring php-xml wget unzip

# Start and enable Apache
sudo systemctl start httpd
sudo systemctl enable httpd

# Set up OpenCart
cd /var/www/html
wget https://github.com/opencart/opencart/releases/download/4.1.0.3/opencart-4.1.0.3.zip
unzip opencart-4.1.0.3.zip
cp -r upload/* .
rm -rf upload opencart-4.1.0.3.zip

# Set correct permissions
chown -R apache:apache /var/www/html
chmod -R 755 /var/www/html
