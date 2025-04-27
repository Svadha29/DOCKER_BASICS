# Bakery Foundation Example on Mac M2 🍞🍏

## Overview
This guide provides step-by-step instructions on setting up and using **Packer** to create a machine image (AMI) on AWS, but this time **on a Mac M2**.

---

## Prerequisites
Before starting, make sure you have:
- A Mac M2 machine with admin access (can use Terminal and sudo).
- An AWS account with IAM credentials (Access Key and Secret Key).
- Basic knowledge of AWS and the Terminal.

---

## Step 1: Install Required Tools

### 1.1 Install Packer

#### Step 1: Install Packer using Homebrew
If you don’t have Homebrew installed, install it first:
```bash
/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"
```

Then install Packer:
```bash
brew tap hashicorp/tap
brew install hashicorp/tap/packer
```

#### Step 2: Verify Packer Installation
Check that Packer is installed:
```bash
packer --version
```
✅ If successful, it will show the installed Packer version.

---

### 1.2 Install AWS CLI

#### Step 1: Install AWS CLI using Homebrew
```bash
brew install awscli
```

#### Step 2: Verify Installation
```bash
aws --version
```
✅ Output should look like:  
`aws-cli/2.x.x Python/3.x.x Darwin/23.x.x source/arm64`

---

### 1.3 Configure AWS CLI (5 minutes)

Run:
```bash
aws configure
```
Enter when prompted:
- **AWS Access Key ID:** `<Your AWS Access Key>`
- **AWS Secret Access Key:** `<Your AWS Secret Key>`
- **Default region name:** `us-east-1` (or your preferred region)
- **Default output format:** `json` (just press Enter)

✅ AWS CLI is now configured!

---

## Step 2: Create the Packer Template

### 2.1 Create the Packer HCL File

1. Open **VS Code** or **any editor**.
2. Create a new file and paste the following:

```hcl
packer {
  required_plugins {
    amazon = {
      source  = "github.com/hashicorp/amazon"
      version = ">= 1.0.0"
    }
  }
}

variable "aws_region" {
  default = "us-east-1"
}

source "amazon-ebs" "python39" {
  ami_name      = "bakery-foundation-python39-${formatdate("YYYYMMDD-HHmmss", timestamp())}"
  instance_type = "t2.micro"
  region        = var.aws_region
  source_ami    = "ami-0a25f237e97fa2b5e"
  ssh_username  = "ubuntu"
}

build {
  sources = ["source.amazon-ebs.python39"]

  provisioner "shell" {
    inline = [
      "sudo apt-get update",
      "sudo apt-get install -y python3.9 python3.9-venv python3.9-dev"
    ]
  }
}
```
3. Save it as `bakery.pkr.hcl` in a folder (e.g., `~/packer-projects/`).

---

### 2.2 Find a Valid Ubuntu AMI

Run:
```bash
aws ec2 describe-images --owners 099720109477 --filters "Name=name,Values=ubuntu/images/hvm-ssd/ubuntu-focal-20.04-amd64-server-*" --query "Images | sort_by(@, &CreationDate)[-1].ImageId" --output text
```

✅ Copy the latest AMI ID and update your `source_ami` in the HCL file:
```hcl
source_ami = "ami-xxxxxxxxxxxxxxxxx"
```

---

## Step 3: Validate and Build the Image

### 3.1 Initialize and Validate Packer Template

Navigate to your project directory:
```bash
cd ~/packer-projects/
```

Initialize Packer:
```bash
packer init .
```

Validate the template:
```bash
packer validate bakery.pkr.hcl
```

✅ If the configuration is correct, you will see:  
`The configuration is valid.`

---

### 3.2 Build the Machine Image

Now build the AMI:
```bash
packer build bakery.pkr.hcl
```

This will:
- Launch a temporary EC2 instance.
- Install Python 3.9.
- Create an AMI.
- Terminate the temporary instance automatically.

---

## Step 4: Deploy and Test the AMI

### 4.1 Find the AMI

Go to:
- [AWS Console](https://aws.amazon.com/console/)
- EC2 → **AMIs** → Make sure your region matches.
- Look for your AMI:  
  `bakery-foundation-python39-<timestamp>`

---

### 4.2 Launch an EC2 Instance with Your AMI

1. Go to **Launch Instance**.
2. Under **My AMIs**, select your AMI.
3. Choose:
   - **Instance Type:** `t2.micro`
   - **Key Pair:** Select or create a new one.
   - **Security Group:** Open **port 22** for SSH.
4. Launch 🚀.

---

### 4.3 Connect to Your Instance

Find the **Public IP** from EC2 dashboard, then:

```bash
chmod 400 path/to/your-key.pem
ssh -i "path/to/your-key.pem" ubuntu@your-instance-ip
```

✅ Accept the fingerprint if asked (type `yes`).

---

### 4.4 Verify Python Installation

Inside the instance:
```bash
python3.9 --version
```
✅ You should see:  
`Python 3.9.5`

---

## Bonus: Python Versions on Ubuntu (Quick Explanation)

| Command | Purpose | Expected Output |
|:--------|:--------|:----------------|
| `python3 --version` | Check default Python 3 version | Python 3.8.10 |
| `python3.9 --version` | Check manually installed Python 3.9 | Python 3.9.5 |

Ubuntu 20.04 ships with **Python 3.8** by default — that's why installing 3.9 manually is necessary.

**Note:** `python` (without `3`) is not available by default in Ubuntu 20.04+.

---

# 🎉 Conclusion
You have successfully:  
✅ Installed Packer and AWS CLI on Mac M2  
✅ Built an AWS AMI with Python 3.9  
✅ Launched an EC2 instance and connected via SSH  

🚀 Now you're ready to use your AMI for Python projects in the cloud! 🚀
