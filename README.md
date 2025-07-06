# 🛠️ Ansible Automation for EC2 Configuration

## 📦 Purpose
This playbook automates the configuration of an AWS EC2 instance running Ubuntu. It installs Docker and ensures it starts on boot. This is **Part 2** of a larger project involving full-stack cloud deployment with automation tools.

## 📁 Folder Structure
ansible-ec2/
│
├── hosts.ini               # Inventory file with EC2 IP and SSH credentials
├── install_docker.yml      # Ansible playbook to install and configure Docker
└── my-stockholm-key.pem    # Private key (.pem) to connect to EC2 via SSH

## 🔧 Requirements
- Ansible installed on local machine (via WSL or Linux/macOS)
- AWS EC2 Ubuntu instance already running
- SSH key (.pem) used to launch the instance
- Public IP address of the EC2 instance

## 🌐 Step 1: Inventory Setup
Edit `hosts.ini`:

[web]
<EC2_PUBLIC_IP> ansible_user=ubuntu ansible_ssh_private_key_file=./my-stockholm-key.pem

✅ Replace `<EC2_PUBLIC_IP>` with your actual EC2 instance public IPv4 address.

## 🧾 Step 2: Playbook – install_docker.yml

---
- name: Install and configure Docker
  hosts: web
  become: true

  tasks:
    - name: Update apt cache
      apt:
        update_cache: yes

    - name: Install required packages
      apt:
        name:
          - apt-transport-https
          - ca-certificates
          - curl
          - software-properties-common
        state: present

    - name: Add Docker GPG key
      apt_key:
        url: https://download.docker.com/linux/ubuntu/gpg
        state: present

    - name: Add Docker repository
      apt_repository:
        repo: deb [arch=amd64] https://download.docker.com/linux/ubuntu focal stable
        state: present

    - name: Install Docker CE
      apt:
        name: docker-ce
        state: latest

    - name: Enable and start Docker service
      systemd:
        name: docker
        enabled: yes
        state: started

## ▶️ Step 3: Run the Playbook

In your terminal (inside `ansible-ec2` folder):

ansible-playbook -i hosts.ini install_docker.yml

Ansible will:
- Connect to your EC2 instance via SSH
- Install Docker with all dependencies
- Enable Docker to start on system boot

## ✅ Result
Your EC2 instance will have Docker installed and ready to run containers immediately. This setup is essential for the next stage, where we deploy a Dockerized Flask app.

## 📝 Notes
- Ensure `chmod 400 my-stockholm-key.pem` is applied to avoid permission issues.
- Make sure port 22 (SSH) is open in your EC2 security group.
