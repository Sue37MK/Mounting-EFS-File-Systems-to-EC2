---
![Alt text](EFS.jpg)

# 📁 Mounting EFS File System to EC2 Instances Across Availability Zones

This project demonstrates how to mount an Amazon Elastic File System (EFS) to two Amazon EC2 instances located in different Availability Zones within the same AWS region. EFS provides a scalable, elastic, cloud-native NFS file system that can be used by multiple EC2 instances simultaneously.

## 🗂️ Project Structure

- Two EC2 instances in different Availability Zones (`us-east-1a` and `us-east-1b`)
- One EFS file system (`MyEFSFilesystem`) shared between both instances
- EC2 instances configured to mount the EFS on boot using EFS Utils

---

## 🔧 Step-by-Step Setup

### 1. Create a Security Group for EFS Access

1. Go to the **EC2 Management Console** > **Security Groups**.
2. Create a new Security Group:
   - **Name**: `EFS-Access`
   - **Description**: Allow access to EFS
   - **VPC**: Default VPC
3. Add the following inbound rule:
   - Type: SSH, Protocol: TCP, Port: 22, Source: Your IP
4. Save the group.
   
![Alt text](security-group.png)

6. After creation, edit the inbound rules again to add:
   - Type: NFS, Protocol: TCP, Port: 2049, Source: **The same `EFS-Access` Security Group**
![Alt text](inbondrule.png)
---

### 2. Launch EC2 Instances in Different Availability Zones

1. Go to **EC2 Dashboard** > **Launch Instance**
2. Configure the following:
   - **AMI**: Amazon Linux 2 AMI
     
   ![Alt text](ami.png)
   
   - **Instance Type**: t2.micro (Free Tier)
     
   ![Alt text](instancetype.png)

   - **Subnet**: Choose `us-east-1a` for the first instance
   - **Security Group**: Select the previously created `EFS-Access`
     
   ![Alt text](networksetting.png)
   
3. Repeat the above steps for the second instance and choose `us-east-1b` as the subnet.
   
   ![Alt text](ami1.png)
   
   ![Alt text](instancetype1.png)
   
   ![Alt text](networksetting1.png)
   
---

### 3. Create an EFS File System

1. Go to the **EFS Console** > **Create File System**
2. Name the file system: `MyEFSFilesystem`

![Alt text](create-efs.png)

4. Choose "Customize" and uncheck **Enable automatic backups**
   
![Alt text](uncheck-autobackup.png)
   
6. Leave the rest of the settings as default and complete the creation.
   
![Alt text](performance.png)

![Alt text](networkaccess.png)
---

### 4. Mount the EFS on EC2 Instances

#### 4.1 Connect to EC2 Instances

Use **EC2 Instance Connect** or SSH to connect to both instances.

![Alt text](ec2-connect.png)

![Alt text](ec2-connect1.png)

#### 4.2 Update ,Create a Mount Directory and Install EFS Utils

Run the following commands on **both instances**:

```bash
sudo yum -y update
mkdir ~/efs-mount-point
sudo yum install -y amazon-efs-utils
```
![Alt text](Efsutil1.png)

![Alt text](Efsutil2.png)

![Alt text](Efsutil3.png)

![Alt text](Efsutil4.png)


#### 4.3 Mount the EFS

1. Go to the **EFS Console**, select your file system, and click **Attach**.
2. Copy the mount command (EFS Mount Helper option), e.g.:

```bash
sudo mount -t efs fs-xxxxxx:/ ~/efs-mount-point
```

3. Run this command on both instances.
   
![Alt text](mounted.png)

![Alt text](mounted1.png)

---

## ✅ Test the Shared File System

1. On the **first EC2 instance**, create a test directory and file:

```bash
cd ~/efs-mount-point
mkdir testdirectory
touch testdirectory/testfile.txt
```
![Alt text](creatdir-file.png)

2. On the **second EC2 instance**, check the shared file:

```bash
cd ~/efs-mount-point
ls 
```
![Alt text](Testing1.png)

![Alt text](Testing2.png)

![Alt text](Testing3.png)

If you can see the directory and file, the EFS mount is successfully shared across both instances.

---

## 📌 Notes

- This setup allows a shared file system between EC2 instances across multiple Availability Zones.
- EFS provides scalable storage and is ideal for applications needing shared access to data.
- Be sure to unmount and clean up resources to avoid unexpected charges if this was for testing purposes.

---
