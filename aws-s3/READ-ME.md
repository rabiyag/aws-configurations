
# Hosting a Static Website on AWS S3

This guide explains how to host a static website on AWS S3. With AWS S3, you can store your website files and configure the bucket to act as a public-facing web server, making it a cost-effective solution for hosting static sites.

## Prerequisites

Before you start, you need:
- An AWS account. [Sign up for an AWS account](https://aws.amazon.com/) if you don’t have one.
- Basic knowledge of AWS S3 and AWS Management Console.

## Table of Contents

1. [Step 1: Create an S3 Bucket](#step-1-create-an-s3-bucket)
2. [Step 2: Configure the Bucket for Static Website Hosting](#step-2-configure-the-bucket-for-static-website-hosting)
3. [Step 3: Upload Website Files to the Bucket](#step-3-upload-website-files-to-the-bucket)
4. [Step 4: Set Permissions for Public Access](#step-4-set-permissions-for-public-access)
5. [Step 5: Test the Website](#step-5-test-the-website)
6. [Step 6: Configure a Custom Domain (Optional)](#step-6-configure-a-custom-domain-optional)
7. [Step 7: Enable HTTPS using Amazon CloudFront (Optional)](#step-7-enable-https-using-amazon-cloudfront-optional)
8. [Troubleshooting](#troubleshooting)
9. [Cleaning Up](#cleaning-up)

---

### Step 1: Create an S3 Bucket

1. Go to the **S3 Console** in AWS.
2. Click **Create bucket**.
3. Choose a **Bucket name** (must be globally unique and typically matches your domain name).
4. Select the **Region** closest to your audience.
5. Leave the **Block Public Access settings** checked initially, as we will update permissions later.
6. Click **Create bucket**.

### Step 2: Configure the Bucket for Static Website Hosting

1. Go to the **Properties** tab of your newly created bucket.
2. Scroll down to **Static website hosting**.
3. Select **Enable**.
4. Under **Hosting type**, choose **Host a static website**.
5. Set the **Index document** (e.g., `index.html`).
6. Optionally, set an **Error document** (e.g., `404.html`).
7. Click **Save changes**.

### Step 3: Upload Website Files to the Bucket

1. Go to the **Objects** tab of your bucket.
2. Click **Upload**.
3. Select or drag-and-drop your website files (e.g., HTML, CSS, JavaScript files) into the console.
4. Click **Upload** to complete the process.

### Step 4: Set Permissions for Public Access

#### 4.1 Update Bucket Permissions

1. Go to the **Permissions** tab of your bucket.
2. Under **Block public access (bucket settings)**, click **Edit**.
03. Uncheck **Block all public access** and acknowledge the warning by checking the box, then click **Save changes**.

#### 4.2 Add a Bucket Policy

1. Under **Bucket policy**, click **Edit**.
2. Add the following policy to allow public access to your bucket’s contents:

    ```json
    {
      "Version": "2012-10-17",
      "Statement": [
        {
          "Effect": "Allow",
          "Principal": "*",
          "Action": "s3:GetObject",
          "Resource": "arn:aws:s3:::your-bucket-name/*"
        }
      ]
    }
    ```

3. Replace `"your-bucket-name"` with your actual bucket name.
4. Click **Save changes**.

### Step 5: Test the Website

1. Go back to the **Properties** tab.
2. Under **Static website hosting**, copy the **Bucket website endpoint** URL.
3. Paste it in a browser to test your website. If configured correctly, your website should display at this endpoint.

### Step 6: Configure a Custom Domain (Optional)

1. In your **domain provider’s settings**, set up an **alias record** (A record) or a **CNAME** pointing to the S3 website endpoint.
2. If using Amazon Route 53 as your domain registrar:
   - Go to **Route 53 Console** and select **Hosted zones**.
   - Choose your domain and click **Create Record**.
   - Add an **A record** and select **Alias to S3 website endpoint**.
3. It may take some time for DNS changes to propagate.

### Step 7: Enable HTTPS using Amazon CloudFront (Optional)

To secure your website with HTTPS, use Amazon CloudFront as a content delivery network (CDN) in front of your S3 bucket.

1. Go to the **CloudFront Console** and click **Create Distribution**.
2. Choose **Web** as the delivery method.
3. Set your S3 bucket endpoint as the **Origin Domain Name**.
4. Configure **Alternate Domain Names (CNAMEs)** with your custom domain.
5. Set **Viewer Protocol Policy** to **Redirect HTTP to HTTPS**.
6. Choose **Custom SSL Certificate** and select a certificate from AWS Certificate Manager (ACM) if you have one; otherwise, create one.
7. Click **Create Distribution**.

This may take several minutes to deploy. Once it’s done, you can access your website over HTTPS.

### Troubleshooting

- **Access Denied Error**: Check the bucket policy and make sure it allows public access.
- **Website Not Loading on Custom Domain**: Verify DNS records and ensure they point to the correct S3 endpoint or CloudFront distribution.

### Cleaning Up

To remove your static website:
1. Delete all files from the bucket in the **Objects** tab.
2. Delete the bucket.
3. If using CloudFront, disable and delete the distribution to avoid unnecessary charges.

---

## Additional Resources

- [AWS S3 Documentation](https://docs.aws.amazon.com/s3/)
- [Static Website Hosting on Amazon S3](https://docs.aws.amazon.com/AmazonS3/latest/dev/WebsiteHosting.html)
- [Amazon CloudFront Documentation](https://docs.aws.amazon.com/AmazonCloudFront/)

---

With this guide, your static website should be accessible through an S3 endpoint or a custom domain with optional HTTPS encryption through CloudFront. Happy hosting!
