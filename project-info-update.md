Here is a comprehensive overview and onboarding guide for this project, including goals, methodologies, tech stack, issues encountered, and the current state.

---

# **Project Overview & Onboarding Guide**

## **Project Purpose & Goals**

- **Goal:**  
  Deploy a fully automated, production-ready Mailcow email server on AWS EC2, with infrastructure-as-code (Terraform), configuration management (Ansible), and automated DNS setup (Porkbun API).  
  The system should support mailbox automation, secure access, and be robust against common deployment pitfalls.

---

## **Tech Stack**

- **AWS EC2**: Cloud compute for hosting Mailcow.
- **Terraform**: Infrastructure provisioning (EC2, security groups, EIP, etc.).
- **Ansible**: Server configuration, Mailcow installation, Docker setup.
- **Docker Compose v2**: Container orchestration for Mailcow.
- **Python**: Automation scripts (DNS updates, mailbox management).
- **Porkbun API**: DNS record management for the domain.
- **Ubuntu 22.04**: Server OS.
- **Virtual Environment**: All Python dependencies are managed in a `venv` located at `gritter-inbox/venv`.

---

## **Key Files & Directories**

- `gritter-inbox/ansible/playbook.yml`: Main Ansible playbook for server setup and Mailcow deployment.
- `gritter-inbox/ansible/mailcow.env.j2`: Jinja2 template for generating Mailcow's `.env` file.
- `gritter-inbox/ansible/inventory.ini`: Ansible inventory, contains the current Mailcow server IP.
- `gritter-inbox/update_dns.example.py`: Python script for updating DNS records via Porkbun API.
- `dns_records_info.txt`: Contains up-to-date Porkbun API credentials and DNS info.
- `temp_env.txt`: Stores environment variables and secrets (AWS, Porkbun, etc.).
- `.env` (project root): May contain environment variables, but not always used by scripts.
- `venv/`: Python virtual environment (activate before running Python scripts).

---

## **Methodology & Steps Taken**

### **1. Infrastructure Provisioning**
- Used Terraform (and AWS CLI for some steps) to:
  - Create a new EC2 instance (now t3.medium for more resources).
  - Assign a persistent Elastic IP.
  - Set up security groups (allow SSH, HTTP, HTTPS, Mail ports).
- Updated all references to the new instance's IP in:
  - `temp_env.txt`
  - `gritter-inbox/ansible/inventory.ini`
  - `gritter-inbox/update_dns.example.py`

### **2. Server Configuration & Mailcow Installation**
- Ansible playbook provisions the server:
  - Installs Docker, Docker Compose v2 (plugin, not legacy binary).
  - Clones Mailcow repo, generates `.env` with secure random DB/Redis credentials.
  - Starts Mailcow with Docker Compose.
- **Critical Fixes:**
  - Ensured Docker Compose v2 is installed (Mailcow requires this).
  - Fixed Docker daemon startup issues (systemd service file, PID file cleanup).
  - Ensured the Ansible playbook removes any old Mailcow directory before cloning.
  - Used Ansible's `password` lookup to generate secure credentials.

### **3. DNS Automation**
- Updated and ran `update_dns.example.py` to:
  - Use new Porkbun API credentials from `dns_records_info.txt`.
  - Set A, MX, SPF, DKIM, and DMARC records for `gritterbox.com`.
  - Confirmed DNS records are created successfully.

### **4. Troubleshooting & Lessons Learned**
- **Docker Issues:**  
  - Docker daemon failed to start due to systemd misconfiguration and leftover PID files.
  - Resolved by editing the systemd service, cleaning up PID files, and restarting Docker.
- **SSH/IPTables Issues:**  
  - Previous attempts (on t2.micro) resulted in the instance becoming inaccessible after Docker/Mailcow install, likely due to resource exhaustion or firewall changes.
  - Upgraded to t3.medium and ensured security group rules are correct.
  - Elastic IP is now used to avoid IP changes on reboot.
- **Virtual Environment:**  
  - All Python scripts must be run with the `venv` activated:  
    `source gritter-inbox/venv/bin/activate`
- **API Credentials:**  
  - All sensitive keys are in `temp_env.txt` and `dns_records_info.txt`.

---

## **Current State**

- **EC2 Instance:**  
  - Running (t3.medium), Elastic IP assigned, accessible via SSH.
- **Mailcow:**  
  - Installation is complete, containers are running (verify with `docker compose ps`).
- **DNS:**  
  - Porkbun records are set for mail delivery.
- **Ansible/Terraform:**  
  - All files updated to reflect new infrastructure.
- **Python Automation:**  
  - DNS script works with new credentials and IP.

---

## **Next Steps**

1. **Verify Mailcow Web UI:**
   - Visit `https://mail.gritterbox.com` (or the Elastic IP) in a browser.
   - Complete any initial setup (admin password, API key).
2. **Obtain Mailcow API Key:**
   - Needed for mailbox automation scripts.
3. **Test Email Delivery:**
   - Send/receive test emails.
4. **Automate Mailbox Management:**
   - Use Mailcow API and Python scripts for mailbox creation, etc.
5. **Finalize Documentation:**
   - Ensure all steps, credentials, and scripts are documented for future use.

---

## **Instructions for New Agents**

1. **Activate the Python Virtual Environment:**
   ```bash
   cd gritter-inbox
   source venv/bin/activate
   ```
2. **Check/Update Environment Variables:**
   - Review `temp_env.txt` and `dns_records_info.txt` for all credentials and IPs.
3. **SSH Access:**
   - Use the Elastic IP from `temp_env.txt` or `inventory.ini`.
   - Ensure your SSH key is available (`~/.ssh/id_rsa`).
4. **Run Ansible Playbook:**
   ```bash
   ansible-playbook -i ansible/inventory.ini ansible/playbook.yml
   ```
5. **Run DNS Automation:**
   ```bash
   python update_dns.example.py
   ```
6. **Mailcow Web UI:**
   - Access via browser, complete setup, retrieve API key.
7. **Troubleshooting:**
   - If Docker fails, check systemd service, PID files, and logs.
   - If SSH is lost, check AWS console for instance health and security group rules.

---

## **Known Issues & Precautions**

- **Resource Exhaustion:**  
  - Avoid t2.micro; use t3.medium or larger.
- **Docker/SSH Lockout:**  
  - Docker misconfiguration or firewall changes can lock out SSH. Always ensure security group rules are correct and Docker is properly configured.
- **Elastic IP:**  
  - Always use an Elastic IP to avoid losing access after instance restarts.
- **API Credentials:**  
  - Keep `temp_env.txt` and `dns_records_info.txt` up to date and secure.

---

## **Contact Points**

- **All credentials and configuration:**  
  - `temp_env.txt`, `dns_records_info.txt`
- **Infrastructure:**  
  - Terraform files (if used), AWS CLI commands
- **Server config:**  
  - `ansible/playbook.yml`, `ansible/inventory.ini`
- **Mailcow:**  
  - `/opt/mailcow-dockerized` on the server, `.env` generated by Ansible
- **DNS:**  
  - `update_dns.example.py`, Porkbun API

## Security & Secrets

- All sensitive credentials (API keys, passwords, tokens, etc.) must be stored in `temp_env.txt` (which is gitignored).
- Never commit secrets to the repository.
- Example (in temp_env.txt):
  MAILCOW_API_KEY=your-mailcow-api-key-here
  PORKBUN_API_KEY=your-porkbun-api-key-here
  AWS_ACCESS_KEY=your-aws-access-key-here
- Ensure `.gitignore` includes `temp_env.txt`, `.env`, `dns_records_info.txt`, and any other secret files.

## Current Status

1. EC2 Instance Provisioning
A new EC2 instance (t3.medium) was successfully created using the AWS CLI.
Elastic IP was allocated and associated:
Public IP: 54.167.252.109
Ansible inventory and environment files were updated to use the new IP.
Security group: The instance was launched with the mailcow-sg group (assumed to have the correct ports open for SSH, HTTP, HTTPS, SMTP, etc.) but let's not make assumptions. Need to confirm this is correct.

2. DNS Configuration
The DNS automation script (update_dns.example.py) was updated and run.
A, MX, SPF, and DMARC records were created for gritterbox.com on Porkbun, pointing to the new server. Double check we have everything set correctly.

3. Mailcow Installation
The Ansible playbook and environment templates were updated for robust, secure, and complete Mailcow deployment.

The previous instance failed due to resource constraints and Docker issues, but the new instance has more resources and a clean setup.

Docker and Docker Compose v2 were installed and configured on the new instance.
Need to make extra assurances that once docker is run, we will still be able to ssh into the server. Ensure issues surrounding iptables and possible overrides from docker are correctly configured.

## What Still Needs to Be Done

A. Confirm Mailcow is Running
We need to run the Ansible playbook to install and start Mailcow on the new instance.
After deployment, verify that Mailcow is accessible at https://mail.gritterbox.com (or via the Elastic IP for initial testing).

B. User Interaction
Once Mailcow is up, the user will need to:
Access the Mailcow web UI.
Set the admin password (if prompted).
Retrieve the Mailcow API key for mailbox automation.

C. Continue Automation
Use the Mailcow API key to automate mailbox creation and email retrieval.
Integrate and test the full workflow.
Finalize documentation and usage examples.
Next Steps (Automated)
Run the Ansible playbook to deploy Mailcow on the new instance.
Monitor the deployment and confirm all containers are running.
Verify web access to Mailcow.

## New Command to Run

sudo docker compose -f /opt/mailcow-dockerized/docker-compose.yml exec -T php-fpm-mailcow ls /var/www/html/helper/