# Custom VPC Setup on AWS with S3 Access

## Overview
This guide provides instructions for setting up a custom VPC on AWS with both public and private subnets, configured for secure access to Amazon S3. This setup isolates your network environment while allowing controlled access to resources outside the VPC.

## Prerequisites
- **AWS Account** with required permissions to create VPC, subnets, and security groups.
- **AWS Console** access.

---

## Steps to Create a Custom VPC in the AWS Console

### Step 1: Create the VPC
1. Go to the **VPC Dashboard** in the AWS Console.
2. Select **Create VPC** and enter the following details:
   - **Name tag**: `CustomVPC`
   - **IPv4 CIDR block**: `10.0.0.0/16`
   - **IPv6 CIDR block**: No IPv6 CIDR block.
   - **Tenancy**: Default
3. Click **Create VPC**.

### Step 2: Create Subnets
1. In the VPC Dashboard, navigate to **Subnets** and select **Create subnet**.
2. For **Name tag**, enter `PublicSubnet`.
   - Select `CustomVPC` for the VPC.
   - **Availability Zone**: Select an available zone (e.g., `us-east-1a`).
   - **IPv4 CIDR block**: `10.0.1.0/24`
3. Repeat to create a **Private Subnet** with:
   - **Name tag**: `PrivateSubnet`
   - **IPv4 CIDR block**: `10.0.2.0/24`

### Step 3: Set Up an Internet Gateway for Public Access
1. Go to **Internet Gateways** and select **Create internet gateway**.
   - Enter a **Name tag**: `CustomIGW`
2. Once created, select the Internet Gateway and **Attach to VPC**, choosing `CustomVPC`.

### Step 4: Configure Route Tables
1. Go to **Route Tables** in the VPC Dashboard and create a new route table:
   - **Name tag**: `PublicRouteTable`
   - **VPC**: `CustomVPC`
2. Add a route to the **PublicRouteTable**:
   - **Destination**: `0.0.0.0/0`
   - **Target**: Internet Gateway (`CustomIGW`)
3. **Associate** the `PublicRouteTable` with the **PublicSubnet**.
4. Repeat to create a **PrivateRouteTable** for the **PrivateSubnet**:
   - No need to add a route for `0.0.0.0/0` at this step (this route will be used for NAT setup if needed).

### Step 5: (Optional) Configure NAT Gateway for Outbound Access from Private Subnet
1. Allocate an **Elastic IP** by navigating to **Elastic IPs** in the EC2 Console.
2. Go to **NAT Gateways** and select **Create NAT Gateway**:
   - **Subnet**: Choose `PublicSubnet`
   - **Elastic IP allocation ID**: Select the newly created Elastic IP
3. Add a route in the **PrivateRouteTable** to allow outbound internet access:
   - **Destination**: `0.0.0.0/0`
   - **Target**: NAT Gateway

### Step 6: Set Up Security Groups
1. Go to **Security Groups** in the VPC Dashboard and select **Create Security Group**:
   - **Name tag**: `AppSecurityGroup`
   - **VPC**: `CustomVPC`
2. Add inbound rules to allow necessary traffic (e.g., HTTP/HTTPS or custom application ports).
3. Add an outbound rule for internet access if using a NAT Gateway.

---

## Configuring S3 Access

### Step 1: Create an S3 Bucket
1. Go to the **S3 Console** and select **Create bucket**.
2. Enter a unique **Bucket name** and select an appropriate **Region**.
3. Set permissions as needed (public access settings, block all public access unless required for specific use cases).

### Step 2: Set Up IAM Role for S3 Access
1. Go to the **IAM Console** and create a new **IAM Role** with **AWS service** as the trusted entity.
2. Choose **EC2** for the service that will use the role and attach a policy that allows S3 access, such as:

   ```json
   {
       "Version": "2012-10-17",
       "Statement": [
           {
               "Effect": "Allow",
               "Action": "s3:*",
               "Resource": "arn:aws:s3:::YOUR_BUCKET_NAME/*"
           }
       ]
   }