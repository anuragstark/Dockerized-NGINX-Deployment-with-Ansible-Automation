# 🚀 Ansible Automation Project: Dockerized NGINX Deployment

This project is designed to **automate Docker and NGINX setup** using Ansible across multiple remote servers. It simplifies environment setup by installing Python3, pip, Docker SDK, and Docker itself, and then automatically deploys an NGINX container.

Whether you're a DevOps engineer, system administrator, or student, this project helps you understand how to automate container-based deployments using Ansible.

---

## 📌 Project Objective

To automate the full lifecycle of:

- Setting up Python and pip on remote machines  
- Installing Docker and Docker SDK  
- Starting the Docker service  
- Pulling and running an **NGINX container**  
- Making it accessible over port `80`  

All done **remotely using Ansible**.

---

## 🧰 Tools & Technologies Used

- **Ansible** – for automation and remote configuration  
- **Docker** – container runtime to run NGINX  
- **NGINX** – lightweight web server used in the container  
- **Python3 + pip3** – needed for Ansible and Docker SDK  
- **Ansible Galaxy Role: `community.docker`** – for Docker management  

---

## 🔧 Prerequisites

Before using this project, make sure:

1. **Control Node (your system)**:
   - Has Ansible installed (`ansible --version`)
   - Has Python3 and pip3 installed
   - SSH access to all remote hosts
   - `community.docker` collection installed:  
     ```bash
     ansible-galaxy collection install community.docker
     ```

2. **Remote Nodes (targets)**:
   - Run a CentOS or RHEL-based OS
   - Have SSH enabled
   - User account has `sudo` privileges

---

## ⚙️ How It Works

Once you execute the Ansible playbook:

1. It connects to each target server.
2. Installs Python3 and pip3 (if missing).
3. Installs the Docker SDK (via pip).
4. Ensures the Docker service is installed, enabled, and started.
5. Pulls the official NGINX Docker image.
6. Runs the NGINX container with port `80` exposed.

---

## 🚀 How to Run

1. Clone the repository:
   ```bash
   git clone https://github.com/anuragstark/Dockerized-NGINX-Deployment-with-Ansible-Automation.git
   cd docker-nginx-ansible
   ```

2. Update your inventory file (`hosts.ini`) with the remote IPs:
   ```ini
   [webservers]
   web1 ansible_host=192.168.1.***
   web2 ansible_host=192.168.1.***
   
   [all:vars]
   ansible_user=your-ssh-user
   ansible_ssh_private_key_file=/path/to/private/key
   ```

3. Run the playbook:
   ```bash
   ansible-playbook -i hosts.ini docker_playbook.yml
   ```

4. After success, open your browser and go to:
   ```
   http://<your-remote-IP>
   ```
   You'll see the default NGINX welcome page.

---

## 📁 Project Structure

```
docker-nginx-ansible/
├── docker_playbook.yml      # Main playbook file
├── hosts.ini                # Inventory file
├── roles/                   # Ansible roles (if used)
│   └── docker/
│       ├── tasks/
│       │   └── main.yml
│       └── handlers/
│           └── main.yml
└── README.md                # This file
```

---

## 📋 Sample Playbook

Here's what the main playbook looks like:

```yaml
---
- name: Deploy NGINX with Docker
  hosts: webservers
  become: true
  
  tasks:
    - name: Install Python3 and pip3
      yum:
        name:
          - python3
          - python3-pip
        state: present
      
    - name: Install Docker SDK for Python
      pip:
        name: docker
        executable: pip3
      
    - name: Install Docker
      yum:
        name: docker
        state: present
        
    - name: Start and enable Docker service
      systemd:
        name: docker
        state: started
        enabled: yes
        
    - name: Pull NGINX Docker image
      community.docker.docker_image:
        name: nginx:latest
        source: pull
        
    - name: Run NGINX container
      community.docker.docker_container:
        name: nginx-server
        image: nginx:latest
        state: started
        ports:
          - "80:80"
        restart_policy: always
```

---

## 🛠️ Troubleshooting

Common issues and solutions:

1. **SSH Connection Issues**
   - Check SSH key permissions: `chmod 600 /path/to/private/key`
   - Verify SSH connectivity: `ssh -i /path/to/key user@remote-ip`

2. **Docker Installation Fails**
   - Make sure your target system has the proper repositories:
     ```bash
     sudo yum install -y yum-utils
     sudo yum-config-manager --add-repo https://download.docker.com/linux/centos/docker-ce.repo
     ```

3. **NGINX Container Not Accessible**
   - Check firewall settings: `sudo firewall-cmd --add-port=80/tcp --permanent`
   - Restart firewall: `sudo firewall-cmd --reload`

---

## 🔒 Security Considerations

- Always use SSH keys instead of passwords
- Consider using Ansible Vault for sensitive data
- Review Docker security best practices
- Limit network exposure to only necessary ports

---

## 🙏 Acknowledgements

- [Ansible Documentation](https://docs.ansible.com/)
- [Docker Documentation](https://docs.docker.com/)
- [NGINX Documentation](https://nginx.org/en/docs/)

---


## 👥 Contributing

Contributions are welcome! Please feel free to submit a Pull Request.

1. Fork the project
2. Create your feature branch (`git checkout -b feature/amazing-feature`)
3. Commit your changes (`git commit -m 'Add some amazing feature'`)
4. Push to the branch (`git push origin feature/amazing-feature`)
5. Open a Pull Request