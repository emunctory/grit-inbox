# Project: Email Server with Mailcow on AWS EC2

## Goal
Create an on-demand AWS EC2 email server using Mailcow and Porkbun API for DNS that can programmatically generate and check email addresses.

## Repository
```
git clone https://github.com/augurybot3/gritter-inbox
cd gritter-inbox
```

## Core Requirements
1. Automate email address generation via API
2. Enable programmatic email retrieval using Python
3. Optimize EC2 resources for cost efficiency (target AWS free tier where possible)
4. Use environment variables from the .env file for configuration
5. Follow best practices for security and code quality

## Implementation Steps

### 1. Project Setup
[ ] Fork the repository to your GitHub account
[ ] Clone your forked repository locally
[ ] Create and activate a Python virtual environment
[ ] Review existing code in `gritter-inbox/update_dns.example.py`, `gritter-inbox/mailcow_inst.md`, and `gritter-inbox/get_email.example.py`
[ ] Examine the `Repositories/.env` file for required configuration variables
[ ] Commit initial setup changes

### 2. AWS Infrastructure Setup
[ ] Launch an EC2 instance (t2.micro if possible for free tier)
[ ] Configure security groups for email ports (25, 465, 587, 143, 993)
[ ] Allocate and associate an Elastic IP
[ ] Configure SSH access
[ ] Document the infrastructure setup in a README file
[ ] Commit infrastructure documentation

### 3. DNS Configuration with Porkbun API
[ ] Implement proper DNS records setup using the Porkbun API
[ ] Create MX, SPF, DKIM, and DMARC records
[ ] Test DNS propagation
[ ] Create automation script for DNS configuration
[ ] Commit DNS configuration code

### 4. Mailcow Installation
[ ] Install Docker and Docker Compose on the EC2 instance
[ ] Clone and configure Mailcow following their documentation
[ ] Set up Mailcow using the credentials from .env
[ ] Verify Mailcow installation and email delivery functionality
[ ] Document the installation process
[ ] *PAUSE FOR USER INTERACTION*: Provide specific instructions for obtaining the Mailcow API key from the web interface
[ ] Commit Mailcow setup documentation

### 5. Email Automation Implementation
[ ] Create a Python script to generate email addresses via Mailcow API
[ ] Implement email retrieval functionality based on `/mail/get_mail.example.py`
[ ] Add proper error handling and logging
[ ] Write unit tests to verify functionality
[ ] Commit automation code

### 6. System Integration
[ ] Create orchestration script that combines DNS configuration, email generation, and retrieval
[ ] Implement configuration validation
[ ] Add detailed logging throughout the system
[ ] Test the complete workflow end-to-end
[ ] Commit integration code

### 7. Documentation and Finalization
[ ] Write comprehensive README with setup and usage instructions
[ ] Document API endpoints and parameters
[ ] Create example usage scripts
[ ] Update requirements.txt with all dependencies
[ ] Submit pull request to the original repository

## Notes
- For any step requiring user input or external access provide explicit instructions and pause
- All API keys and credentials must be handled securely using environment variables
- Regular commits should be made with descriptive messages
- The checklist will be updated as tasks are completed
- The implementation should prioritize reliability and minimal resource usage

## User Interaction Points
- Obtaining Mailcow admin credentials
- Verifying email functionality
- Testing email generation and retrieval
- Reviewing the final solution before pull request

Please provide feedback or adjustments at any point in the process.