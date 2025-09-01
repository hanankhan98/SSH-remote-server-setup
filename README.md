## SSH Remote Server Setup

This project is a **hands-on guide** to setting up a remote Linux server and configuring it for secure SSH access.  
It’s designed to help you practice the fundamentals of **server administration**, **SSH key management**, and **basic security hardening**.  

Inspired by a challenge from (https://roadmap.sh/projects/ssh-remote-server-setup)

---

## Goal
The project focuses on helping you:

- Launch and configure a remote Linux server (AWS EC2 as an example)  
- Generate and use multiple SSH key pairs for secure authentication  
- Simplify SSH connections with a local SSH config file  
- Strengthen server security with **fail2ban** against brute-force attacks  

---

## Features
- AWS EC2 Linux server setup  
- Multiple SSH key pairs for authentication  
- `authorized_keys` configuration for secure login  
- Local SSH config file for quick access  
- **fail2ban** installation and configuration for added protection  

---

## Step 1 — Launch a Remote Linux Server
1. Log in to AWS and open **EC2 Dashboard**.  
2. Launch a new instance:  
   - AMI: **Amazon Linux 2023** or **Amazon Linux 2** (free tier eligible)  
   - Instance type: **t2.micro** (free tier eligible)  
   - Create and download a new key pair (`.pem` file)  
   - Configure security group → allow **SSH (port 22)** from your IP  
   - Use default storage (8GB)  
   - Launch the instance  

---

## Step 2 — Generate Local SSH Key Pairs
Run the following on your local machine:
```bash
ssh-keygen -t rsa -b 4096 -f ~/.ssh/aws_key1
ssh-keygen -t rsa -b 4096 -f ~/.ssh/aws_key2
```

---

## Step 3 — Add Public Keys to the Server
1. Connect with the `.pem` key:
   ```bash
   ssh -i key-name.pem ec2-user@<instance-ip>
   ```
2. Display your public keys locally:
   ```bash
   cat ~/.ssh/aws_key1.pub
   cat ~/.ssh/aws_key2.pub
   ```
3. On the server, edit the `authorized_keys` file:
   ```bash
   sudo nano ~/.ssh/authorized_keys
   ```
   Paste both public keys (one per line), then save.

---

## Step 4 — Test Access with Both Keys
```bash
ssh -i ~/.ssh/aws_key1 ec2-user@<instance-ip>
ssh -i ~/.ssh/aws_key2 ec2-user@<instance-ip>
```
Both keys should work.

---

## Step 5 — Simplify Access with SSH Config
Edit or create `~/.ssh/config` on your local machine:
```bash
nano ~/.ssh/config
```

Add:
```
Host aws-server1
    HostName <instance-ip>
    User ec2-user
    IdentityFile ~/.ssh/aws_key1

Host aws-server2
    HostName <instance-ip>
    User ec2-user
    IdentityFile ~/.ssh/aws_key2
```

Now connect easily:
```bash
ssh aws-server1
ssh aws-server2
```

---

## Step 6 — Install and Configure Fail2ban
1. Connect to the server:
   ```bash
   ssh aws-server1
   ```
2. Install fail2ban:
   ```bash
   sudo amazon-linux-extras install epel
   sudo yum install fail2ban
   ```
3. Enable fail2ban:
   ```bash
   sudo systemctl start fail2ban
   sudo systemctl enable fail2ban
   ```
4. Configure fail2ban:
   ```bash
   sudo cp /etc/fail2ban/jail.conf /etc/fail2ban/jail.local
   sudo nano /etc/fail2ban/jail.local
   ```
5. In the `[sshd]` section, ensure it looks like this:
   ```
   [sshd]
   enabled = true
   port = ssh
   filter = sshd
   logpath = /var/log/secure
   maxretry = 3
   bantime = 600
   ```
6. Save and Exit.
   
7. Restart fail2ban:
   ```bash
   sudo systemctl restart fail2ban
   ```
This configuration will ban an IP after 3 failed attempts for 10 minutes.

---

## Outcome
By the end of this project, you will have:

- A working **AWS Linux server** accessible via SSH  
- Multiple SSH key pairs configured for login  
- A simplified **SSH config file** for faster access  
- **fail2ban** running to protect against brute-force attacks  

---

## What You Learned
- Provisioning servers on AWS EC2  
- Generating and managing SSH key pairs  
- Configuring secure SSH access  
- Hardening servers with fail2ban  
