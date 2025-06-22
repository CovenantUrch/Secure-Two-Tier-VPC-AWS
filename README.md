<h1>☁️ Creating a VPC Networking Environment for the Café</h1>

This project demonstrates how to build a secure, production-ready two-tier VPC architecture on AWS using Amazon VPC, and EC2. It’s based on a real-world scenario where the café's growing business needed a more scalable and secure network structure.

---

<h2>🎯 Objective</h2>

Design and deploy a networking environment where:

- The **web application** runs in a **private subnet**
- The **database** (Amazon RDS) runs in a **separate private subnet**
- A **Bastion Host** in a **public subnet** provides secure SSH access
- A **NAT Gateway** allows private servers to access the internet for updates

---

<h2>🧱 Architecture Overview</h2>
<br/>
<img src="1 DB.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
<br />

---

## 🧰 AWS Services Used
| Service | Purpose |
|--------|---------|
| Amazon VPC | Create isolated networking environment |
| EC2 | Host Bastion and App servers |
| NAT Gateway | Allow internet access from private subnets |
| Subnets | Separate layers (public, app, database) |
| Security Groups | Control inbound and outbound traffic |

<h2>🪜 Step-by-Step Setup</h2>

✅ 1. Create Custom VPC and Subnets

VPC ID: Choose Lab VPC.
- Subnet name: Enter Public Subnet.
- Availability Zone: Choose Availability Zone a in your Region. For example, if your Region is us-east-1, choose us-east-1a.
- IPv4 subnet CIDR block: Enter 10.0.0.0/24.

✅ 2. Set Up Internet Gateway & Public Route Table

- IGW attached to VPC
- Route table for public subnet: `0.0.0.0/0 → IGW`

✅ 3. Launch Bastion Host (EC2 in public subnet)

- For Name, enter Bastion Host.
- For Amazon Machine Image (AMI), choose Amazon Linux 2023 AMI.
- For Instance type, choose t2.micro.
- For Key pair name - choose create key pair and save it.
- In the Network settings section, choose Edit, and configure the following options:
- For VPC - required, choose Lab VPC.
- For Subnet, choose Public Subnet.
- For Auto-assign public IP, choose Enable.
- For Security group, create a security group called Bastion Host SG that allows only the following traffic:
- For Type, choose ssh.
- For Port, enter 22.
- For Source type, choose My IP.
<h3>NOTE</h3>
Note: In practice, hardening a bastion host involves more work than only restricting Secure Shell (SSH) traffic from your IP address. A bastion host is typically placed in a network that's closed off from other networks. It's often protected with multi-factor authentication (MFA) and monitored with auditing tools. Most enterprises require an auditable access trail to the bastion host.

✅ 4.Testing the connection to the bastion host
In this task, you use the SSH key (.pem file or .ppk file) to test the SSH connection to your bastion host. You downloaded this key when you created your Bastion Host

- Connect to your bastion host by using SSH.
- After you have tested your connection to the bastion host, you can close the terminal or PuTTY.

Hint: If you get stuck, see the AWS Documentation(https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/concepts.html). This page provides detailed instructions about how to use SSH to connect to an EC2 instance.
Note for Microsoft Windows users: If you don't have PuTTY installed, you must download and install PuTTY(https://the.earth.li/~sgtatham/putty/latest/w64/putty.exe). It is recommend that you configure PuTTY so that your connection doesn't expire. To keep the PuTTY session open longer, for Seconds between keepalives, enter 30.

✅ 5. Creating a Private Subnet

- VPC ID: Choose Lab VPC.
- Subnet name: Enter Private Subnet.
- Availability Zone: Choose the same Availability Zone that you choose for the Public Subnet in one of the previous tasks.
- IPv4 subnet CIDR block: Enter 10.0.1.0/24.

✅ 6. Set Up NAT Gateway for Private Subnets

- Create a NAT gateway that has the following options:
- Name - optional: Enter Lab NAT Gateway.
- Subnet: Choose Public Subnet.
- Elastic IP allocation ID: Choose Allocate Elastic IP.
Tip: Your NAT gateway needs an Elastic IP address.
- Create a new route table
- Name - optional: Enter Private Route Table.
- VPC: Chose Lab VPC.
Attach this route table to the Private Subnet that you created earlier using the following options: Hint: If you get stuck, see the AWS Documentation(https://docs.aws.amazon.com/vpc/latest/userguide/vpc-nat-gateway.html#nat-gateway-creating).

✅ 7. Creating an EC2 instance in the private subnet

- Follow the steps you used in creating an EC2 instance in a public subnet, when you get to Network settings follow the step below
- In the Network settings section, choose Edit, and configure the following options:
- For VPC - required, choose Lab VPC.
- For Subnet, choose Private Subnet.
- For Security group, create a security group called Private Instance SG that allows only the following traffic:
- For Type, choose ssh.
- For Port, enter 22.
- For Source type, choose Custom.
- For Source, choose the bastion host security group

✅ 8. Configuring your SSH client for SSH passthrough

Because the private instance you just created uses a different key pair than the bastion host, you must configure your SSH client to use SSH passthrough. This action gives you the ability to use a key pair that's stored on your computer to access the private instance without uploading the key pair to the bastion host. This is a good security practice.  

✅ 9. Testing the SSH connection from the bastion host

- In this task, you test the SSH connection from your bastion host to the EC2 instance that is running in the Private Subnet.
- Connect to the bastion host instance by using SSH.
Tip: Use the connection method that was described in the SSH passthrough section.
- Connect to the private instance by using SSH and the IP address for the private instance. Your command should look similar to the following. In this command, replace ssh ec2-user@<private-ip-address-of-instance-in-private-subnet> with the private IP address of the instance in the private subnet:
- Now that you are connected to the EC2 instance in the Private Subnet, use the following command to test its connection to the internet:
  <br/>
<img src="2 DB.jpeg" height="80%" width="80%" alt="Disk Sanitization Steps"/>
<br />

Tip: Press Ctrl+C to exit the command.

You have now established a communication between the Bastion Host in the Public Subnet and the EC2 instance in the Private Subnet as in the following diagram:

 <br/>
<img src="3 DB.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
<br />

<h3>📈 Why This Project Matters</h3>

| Challenge | Solution |
|----------|----------|
| Insecure public-facing DB | RDS in private subnet |
| App needs updates | NAT Gateway for outbound |
| Remote access needed | Bastion Host with SSH |
| No network isolation | VPC with subnet tiers |

<h1>🙋 Author</h1>
Covenant Onwukwe**  
Cloud Builder | AWS Projects | Lagos, Nigeria
