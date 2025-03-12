<div align="center" id="top"> 
  <img src="https://logos-world.net/wp-content/uploads/2021/08/Amazon-Web-Services-AWS-Logo.png" alt="Coding" />

  &#xa0;
</div>

<h1 align="center">AWS EC2 Deployment</h1>


<p align="center">
This project provides an automated solution for launching and managing Amazon EC2 instances using Ansible. It simplifies the process of provisioning EC2 instances on AWS by leveraging Ansible's powerful automation capabilities. The project is designed to be easily customizable, enabling users to quickly spin up EC2 instances for development, testing, or production environments.
</p>



## Features: ##

- Quickly launch EC2 instances on AWS with predefined configurations.
- Configure instances in different AWS regions with ease.
- Define and manage AWS security groups for your instances.

## Requirements: ##

- Ansible Core ```version 2.11 or higher```
- Amazon AWS Account
- Amazon Machine Image (AMI) - [Amazon Machine Images in Amazon EC2 - Amazon Elastic Compute Cloud](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/AMIs.html)
- Amazon Instance Type (General Purpose T3 for testing) - [Cloud Compute Instances – Amazon EC2 Instance Types – AWS](https://aws.amazon.com/ec2/instance-types/)

## Setup:
These instructions will walk you through setting up your Amazon EC2 Instances for Automation.

### Step One: Install Ansible (Ubuntu)
---

1. Ensure your Ubuntu machine is up to date:
   
   ```shell
   sudo apt update -y && sudo apt upgrade -y
   ```

2. Install Ansible on Ubuntu with the following command:
   
   ```shell
   sudo apt install ansible -y
   ```

3. Verify the installation:

   ```shell
   ansible --version
   ```

### Step Two: Create an AWS EC2 Key Pair
---
> [!IMPORTANT]  
> Make sure that you select the correct region in AWS before creating your Key Pair or you will receive an error.

1. Navigate to **aws.amazon.com**, login, and then click on **"AWS Management Console"** 

2. Click on EC2, then under **"Network and Security"**, click on **"Key Pairs"**

3. Click **"Create Key Pair"** in the top-right corner

4. Give your **"Key Pair"** a name
   - Select **"RSA"** as the **"Key Pair Type"**
   - Select **".pem"** as the **"Private key file format"**
   - Give it a tag (optional)


### Step Three: Using the Key Pair
---
This task is specifically for handling the .pem (Privacy Enhanced Mail) key file that AWS provides when you create a new key pair in the AWS Management Console.

```yml
- name: Ensure Key is Not Publicly Viewable
  file:
    path: /path/to/pem
    mode: '0400'
  delegate_to: localhost
```

When you created this key pair in AWS, it would have downloaded a file like **"Your_Key_Pair .pem".** The task: needs to be updated with the actual path to where you saved the downloaded **".pem"** file. This permission setting (0400) is required because AWS and SSH are very strict about key file permissions, if they're too permissive, SSH will refuse to use the key for security reasons.

### Step Four: Group_Vars
---

Create a directory in your repository called ```group_vars```

```shell
sudo mkdir group_vars
```

```shell
sudo nano vars.yml
```

Add the following variables:

```yml
aws_access_key:
aws_secret_key:
security_group:
security_group_description: 
instance_name:
key_name: 
image_id:
instance_type:
region:
```

In your Ansible playbook, add the following

```yml
vars_files:
  - ./group_vars/vars.yml
```

### Step Five: Ansible Vault
---
After creating our **vars.yml** file, we will use **Ansible Vault** to encrypt our sensitive information. Use the following commands:

```shell
cd group_vars/
```

```shell
sudo ansible-vault encrypt vars.yml
```

You will be asked to **create a new vault password** and to **confirm the vault password**

```shell
New Vault Password:
Confirm New Vault Password:
```

If successful, you should see the following message:

```shell
Encryption successful
```

Verify that your files have been encrypted using the following command:

```shell
sudo cat /group_vars/vars.yml
```

You should now see the following:

```shell
$ANSIBLE_VAULT;1.1;AES256
xxxxx
```

### Step Six: Running The Playbook
---
We will now run our Ansible Playbook with Ansible Vault. Use the following command to run the playbook:

```shell
ansible-playbook run.yml --ask-vault-pass
```

