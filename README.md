# Configuration Management Ansible
Configuring a Linux server with an Ansible playbook.

## 🔧 Roles
- `base` — basic server setup (installs utilities, updates the server, installs fail2ban, etc.)

- `nginx` — installs and configures nginx

- `app` — Clone the given repo of a static HTML website to the server.

- `ssh` — adds the given public key to the server

## 📂 Project Stucture
```
ansible-project/
├── inventory.ini
├── setup.yml
└── roles/
    ├── base/
    │   └── tasks/main.yml
    ├── nginx/
    │   └── tasks/main.yml
    ├── app/
    │   └── tasks/main.yml
    └── ssh/
        ├── files/
        └── tasks/main.yml
```

## 🛠 Setup
### 1. Provision a Remote Linux Server (AWS EC2)

1. Log in to [AWS Management Console](https://aws.amazon.com/console/).

2. Go to EC2 and launch a new instance:

    - Choose Ubuntu 22.04 LTS (or latest stable version).

    - Select a free-tier eligible instance type (e.g., `t3.micro`).

    - Configure networking → allow inbound traffic on ports 22 (SSH), 80 (HTTP), and optionally 443 (HTTPS).

    - Create and download your key pair (e.g., `aws-key.pem`).

    - Launch the instance.

3. Locate and copy your instance Public IP in the EC2 dashboard.
### 2. Update Inventory File
In `inventory.ini` file: 
- Replace `your_server_ip` with your instance's public IP.
- Update `ansible_user` to `ubuntu` if using the default Ubuntu user.
- Update `ansible_ssh_private_key_file` to the path of your downloaded key pair (e.g., `~/.ssh/aws-key.pem`).

### 3. Update App Role
In `roles/app/tasks/main.yml` file:
- Update the `repo` value to the URL of your static website repository.

### 4. Add New SSH Key Pair and Update SSH Role
 1. Generate a new SSH key pair (if not already done):
    ```bash
    ssh-keygen -t rsa -f ~/.ssh/id_rsa
    ```
 2. Update `roles/ssh/tasks/main.yml` file:
    - Add the public key path (e.g., `~/.ssh/id_rsa.pub`) to the task's `key` parameter.

### 5. Run Playbook

```bash
# Run all the roles
ansible-playbook -i inventory.ini setup.yml

# Run only the app role
ansible-playbook -i inventory.ini setup.yml --tags "app"

```
## Verify Setup

After running the playbook, verify that everything is working correctly:

1. Check SSH access with your new key:
   ```bash
   ssh -i ~/.ssh/id_rsa ubuntu@your_server_ip
   ```

2. Verify Nginx is running (in the server):
   ```bash
   sudo systemctl status nginx
   ```

3. Check your website is accessible:
   - Open a web browser and navigate to `http://<EC2_PUBLIC_IP>`
   - You should see your static website content

4. Verify fail2ban is active:
   ```bash
   sudo systemctl status fail2ban
   ```

## License

This project is licensed under the MIT License - see the [LICENSE](https://github.com/MGhaith/Configuration-Management-Ansible/blob/main/LICENSE) file for details.
