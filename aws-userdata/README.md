 Overview
This guide explains how to launch an EC2 instance on AWS with a **User Data script** to install OpenJDK and other dependencies for Java applications. This setup allows you to configure an environment ready for deploying Java applications upon instance launch.

## Prerequisites
- **AWS Account** with permissions to launch EC2 instances.
- **AWS Console** access.

---

## Steps to Launch an EC2 Instance with Java Application Dependencies

### Step 1: Create an EC2 Instance
1. Go to the **EC2 Dashboard** in the AWS Console.
2. Click **Launch Instance** and configure:
   - **AMI**: Choose an Amazon Machine Image (e.g., Amazon Linux 2).
   - **Instance Type**: Select an appropriate instance type (e.g., `t2.micro` for basic testing, or larger as needed).
   - **Key Pair**: Select or create a key pair for SSH access.

### Step 2: Add User Data Script
1. In the **Advanced Details** section, locate the **User Data** field.
2. Add the following User Data script to install OpenJDK and other essential packages:

   ```bash
   #!/bin/bash
   # Update package repository
   yum update -y
   # Install OpenJDK
   amazon-linux-extras install java-openjdk11 -y
   # Install additional utilities (optional)
   yum install -y git unzip wget

   This script:

- Updates the package repository.
- Installs OpenJDK 11 (or another version as needed).
- Installs essential utilities like Git, Unzip, and Wget.

3. Click Add Storage to specify the volume size, if needed.
4. Configure Security Group:
5. Allow inbound traffic on relevant ports if your Java application requires network access (e.g., port 8080 for HTTP).
6. Click Review and Launch, then Launch to start the instance.

### Step 3: Verify the Setup
1.  Once the instance is running, SSH into it using:
ssh -i /path/to/your-key.pem ec2-user@<Public-IP>

2. Verify the installation by checking the Java version:

java -version