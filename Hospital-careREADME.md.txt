# 🏥 AWS Hospital Care Notification System

A cloud-based hospital notification project built using **AWS VPC, Amazon EC2, IAM, VPC Endpoints, AWS Systems Manager (SSM), and Amazon SNS**.

The project demonstrates how a **private EC2 instance without a public IP address** can securely communicate with AWS services through **VPC Endpoints** and send notifications using **Amazon SNS**.

---

## 📌 Project Overview

The objective of this project is to create a secure AWS environment for a hospital-care application.

The EC2 instance is deployed inside a **private subnet** and does not have a public IPv4 address. AWS service communication is provided through VPC Endpoints, while an IAM role provides the required permissions.

The application sends a test notification through an **Amazon SNS topic** to a confirmed email subscription.

---

## 🏗️ AWS Architecture

```text
                    AWS Cloud
                       │
                ┌──────▼──────┐
                │    VPC      │
                │ 10.0.0.0/16 │
                └──────┬──────┘
                       │
              Private Subnet
                       │
                ┌──────▼──────┐
                │    EC2      │
                │ t3.micro    │
                │ Private IP  │
                │ 10.0.131.174│
                └──────┬──────┘
                       │
                 IAM Role
              Hospital-Report
                       │
          ┌────────────┴────────────┐
          │                         │
    VPC Endpoints              AWS Services
          │                         │
     ┌────┴─────┐              ┌────▼────┐
     │   SNS    │─────────────►│   SNS   │
     │ Endpoint │              │  Topic  │
     └──────────┘              └────┬────┘
                                    │
                              Email Subscription
                                    │
                                    ▼
                              📧 Notification
```

---

## ☁️ AWS Services Used

| AWS Service             | Purpose                                       |
| ----------------------- | --------------------------------------------- |
| **Amazon VPC**          | Creates the isolated network                  |
| **Amazon EC2**          | Hosts the application/server                  |
| **IAM**                 | Provides secure permissions to EC2            |
| **VPC Endpoints**       | Provides private connectivity to AWS services |
| **AWS Systems Manager** | Allows management of the private EC2 instance |
| **Amazon SNS**          | Sends notification messages                   |
| **Amazon S3**           | Access through the S3 Gateway Endpoint        |
| **AWS CLI**             | Publishes the test SNS message                |

---

## 🔧 Project Configuration

### VPC

* **Name:** `hospital-care-vpc`
* **VPC CIDR:** `10.0.0.0/16`
* **DNS Resolution:** Enabled
* **DNS Hostnames:** Enabled

### EC2

* **Instance Name:** `hospital-care`
* **Instance Type:** `t3.micro`
* **Subnet:** Private subnet
* **Private IP:** `10.0.131.174`
* **Public IPv4:** None
* **IAM Role:** `Hospital-Report`

### IAM Policies

The EC2 instance uses the following policies:

* `AmazonSNSFullAccess`
* `AmazonSSMManagedInstanceCore`

> For production environments, permissions should be reduced to the minimum required by the application.

### VPC Endpoints

The project contains the following endpoints:

* S3 Gateway Endpoint
* SNS Interface Endpoint
* SSM Interface Endpoint
* SSM Messages Interface Endpoint
* EC2 Messages Interface Endpoint

All endpoints shown in the project are **Available**.

### SNS

* **Topic:** `hospital-care`
* **Topic Type:** Standard
* **Protocol:** Email
* **Subscription Status:** Confirmed

---

# 🚀 Step-by-Step Implementation

## Step 1 — Create the VPC

Create a custom VPC with the following configuration:

```text
VPC Name: hospital-care-vpc
CIDR: 10.0.0.0/16
```

Enable:

```text
DNS Resolution: Enabled
DNS Hostnames: Enabled
```

The VPC provides the private network for the hospital-care application.

---

## Step 2 — Create the Private Subnet

Create a private subnet inside the VPC.

Example:

```text
VPC: hospital-care-vpc
Subnet Type: Private
Availability Zone: us-east-1a
```

The EC2 instance is launched inside this private subnet.

---

## Step 3 — Create the IAM Role

Create an IAM role for EC2:

```text
Role Name: Hospital-Report
```

Attach the required policies:

```text
AmazonSNSFullAccess
AmazonSSMManagedInstanceCore
```

This allows the EC2 instance to communicate with SNS and be managed using Systems Manager.

---

## Step 4 — Create VPC Endpoints

Because the EC2 instance does not have a public IP address, create VPC endpoints for AWS services.

Configure:

```text
S3
SNS
SSM
SSM Messages
EC2 Messages
```

The interface endpoints allow the private EC2 instance to communicate with AWS services without requiring direct Internet access.

---

## Step 5 — Create the SNS Topic

Create an Amazon SNS topic:

```text
Topic Name: hospital-care
Topic Type: Standard
```

The topic is responsible for distributing notification messages.

---

## Step 6 — Create the Email Subscription

Create an email subscription:

```text
Protocol: EMAIL
Endpoint: Your email address
```

Open the confirmation email and confirm the subscription.

The subscription should show:

```text
Status: Confirmed
```

---

## Step 7 — Launch the EC2 Instance

Launch an EC2 instance in the private subnet.

Example configuration:

```text
Name: hospital-care
Instance Type: t3.micro
Subnet: Private subnet
Public IP: Disabled
IAM Role: Hospital-Report
```

The instance should have a private IP address.

Example:

```text
Private IP: 10.0.131.174
```

---

## Step 8 — Connect to EC2 Using AWS Systems Manager

Since the EC2 instance does not have a public IP address, use **AWS Systems Manager Session Manager** to access it.

Verify that:

```text
AmazonSSMManagedInstanceCore
```

is attached to the EC2 IAM role.

Also verify that the SSM-related VPC endpoints are available.

---

## Step 9 — Install/Verify AWS CLI

Inside the EC2 instance, verify AWS CLI:

```bash
aws --version
```

Verify the AWS identity:

```bash
aws sts get-caller-identity
```

The command should return information about the IAM role being used by the EC2 instance.

---

## Step 10 — Publish an SNS Message

Run:

```bash
aws sns publish \
  --topic-arn arn:aws:sns:us-east-1:905367996208:hospital-care \
  --message "testing the application - from EC2" \
  --region us-east-1
```

If successful, AWS returns a response similar to:

```json
{
    "MessageId": "6bcc472e-459e-58dd-975a-25c76f6fce08"
}
```

The `MessageId` confirms that the SNS publish request was successfully accepted.

---

# 📸 Screenshots

The project documentation contains the screenshots for each major configuration step.

Recommended GitHub structure:

```text
AWS-Hospital-Care/
│
├── README.md
│
├── documentation/
│   └── AWS_Hospital_Care_Project_Document.pdf
│
├── screenshots/
│   ├── 01-vpc.png
│   ├── 02-iam-role.png
│   ├── 03-vpc-endpoints.png
│   ├── 04-sns.png
│   ├── 05-ec2.png
│   └── 06-sns-cli.png
│
└── commands/
    └── sns-command.txt
```

---

# 🧪 Testing

The project was tested by publishing an SNS message directly from the private EC2 instance.

Command:

```bash
aws sns publish \
  --topic-arn arn:aws:sns:us-east-1:905367996208:hospital-care \
  --message "testing the application - from EC2" \
  --region us-east-1
```

### Result

```text
SNS Publish: SUCCESS
MessageId: Generated successfully
Email Subscription: Confirmed
```

---

# 🔐 Security Features

This project demonstrates several AWS security concepts:

* EC2 deployed in a private subnet
* No public IPv4 address on the EC2 instance
* IAM role instead of storing AWS access keys
* VPC Endpoints for private AWS service communication
* AWS Systems Manager for private instance access
* SNS subscription confirmation
* VPC-level network isolation

---

# 📊 Project Result

The final architecture successfully demonstrates:

```text
Private EC2
     ↓
IAM Role
     ↓
VPC Endpoint
     ↓
Amazon SNS
     ↓
Confirmed Email Subscription
     ↓
📧 Notification
```

The SNS CLI command successfully returned a `MessageId`, confirming successful message publishing.

---

# 🛠️ Technologies Used

```text
AWS
Amazon VPC
Amazon EC2
AWS IAM
AWS Systems Manager
Amazon SNS
Amazon S3
AWS CLI
Linux
```

---

# 📚 What I Learned

Through this project, I gained practical experience with:

* Creating and configuring an AWS VPC
* Working with CIDR blocks
* Creating public/private network architecture
* Launching EC2 instances in private subnets
* Creating IAM roles and policies
* Configuring VPC Endpoints
* Using AWS Systems Manager
* Creating SNS topics and subscriptions
* Publishing SNS messages using AWS CLI
* Building secure AWS cloud architectures
* Troubleshooting AWS service connectivity

---

# 📄 Project Documentation

The complete step-by-step project documentation is available here:

```text
documentation/AWS_Hospital_Care_Project_Document.pdf
```

---

# 👨‍💻 Author

**Aman Maniyar**

📧 Email: `maniyaram744@gmail.com`

🔗 LinkedIn:
https://www.linkedin.com/in/aman-maniyar-693583401/

🔗 GitHub:
https://github.com/maniyaraman744-hue

---

# ⭐ Project

If you find this project useful, consider giving the repository a ⭐ star.

**AWS Hospital Care Notification System — Built for learning and practical AWS/DevOps experience.**
