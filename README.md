# 3-Tier Architecture App on AWS

This project demonstrates the deployment of a **Highly Available, Secure, and Scalable 3-Tier Web Application** on AWS using **VPC, EC2, S3, RDS (Aurora), IAM, Load Balancers, and CloudFront**.

---

## 📌 Architecture Overview

The application is deployed in a **3-tier architecture**:

1. **Web Tier** – Public-facing layer serving the front-end, hosted on EC2 in public subnets, behind an Internet-Facing Load Balancer.
2. **Application Tier** – Backend logic running on EC2 in private subnets, behind an Internal Load Balancer.
3. **Database Tier** – Amazon Aurora (MySQL/PostgreSQL-compatible) running in private subnets, Multi-AZ for high availability.

**Key AWS Services Used:**
- **Amazon VPC** – Custom network setup across two Availability Zones.
- **Amazon S3** – Stores application code and configuration files.
- **AWS IAM** – Role-based access to S3 and other AWS services.
- **Amazon RDS (Aurora)** – Managed relational database service.
- **Elastic Load Balancers** – External (web tier) and internal (app tier) load balancers.
- **AWS CloudFront** – CDN for faster global content delivery.
- **AWS WAF** – Protects from malicious traffic.
- **AWS Certificate Manager (ACM)** – Manages SSL/TLS certificates.

---

## 🛠️ Project Setup Steps

### **Step 1 – VPC Creation**
- Create a custom VPC with public and private subnets in **two Availability Zones**.
- Configure Internet Gateway, NAT Gateway, and appropriate route tables.

### **Step 2 – S3 Bucket & IAM Role**
- Create an **S3 bucket** and upload application code in the following structure:

- application-code/
├── app-tier/
└── web-tier/

- Create an **IAM role** with S3 access and attach it to EC2 instances.

### **Step 3 – Database Configuration**
- Launch **Amazon Aurora** in private subnets.
- Update `application-code/app-tier/DbConfig.js` with:
```javascript
module.exports = Object.freeze({
    DB_HOST: '<DB_ENDPOINT>',
    DB_USER: 'admin',
    DB_PWD: '<DB_PASSWORD>',
    DB_DATABASE: 'webappdb'
});

Step 4 – Application Tier Setup
Launch EC2 instances in private subnets for backend.

Install MySQL client, Node.js, and PM2.

Configure database:
CREATE DATABASE webappdb;
CREATE TABLE transactions (
  id INT NOT NULL AUTO_INCREMENT,
  amount DECIMAL(10,2),
  description VARCHAR(100),
  PRIMARY KEY (id)
);

Download code from S3:

aws s3 cp s3://<S3BucketName>/application-code/app-tier/ app-tier --recursive
cd app-tier && npm install && pm2 start index.js

Step 5 – Web Tier Setup
Launch EC2 instances in public subnets for frontend.
Install Node.js and Nginx.
Download web code from S3 and build:
aws s3 cp s3://<S3BucketName>/application-code/web-tier/ web-tier --recursive
cd web-tier && npm install && npm run build

Step 6 – SSL & Domain Mapping
Request SSL certificate via AWS Certificate Manager.

Attach to External Load Balancer.
Map domain in Route 53.

✅ Verification
Access the application using the External Load Balancer’s domain or Route 53 record.
Verify /health endpoint:
curl http://<APP_PRIVATE_IP>:4000/health

Ensure database inserts are reflected when submitting forms.

🧹 Cleanup
When finished:

Delete Auto Scaling Groups
Delete Load Balancers & Target Groups
Delete AMIs & Snapshots
Delete Database
Delete S3 Bucket
Delete SSL Certificates
Delete Route 53 Records
Delete NAT Gateway & Elastic IP
Delete VPC

<img width="1214" height="548" alt="3TierArch" src="https://github.com/user-attachments/assets/4a8254c9-1e30-4bd3-bbf6-486724837904" />
