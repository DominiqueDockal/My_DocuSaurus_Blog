# V-Server Setup

Technical documentation for a cloud VM setup project. This README describes the basic server setup process, including initial access, system updates, SSH key authentication, and the structure for the remaining required configuration steps.

## Table of Contents

- [Project Overview](#project-overview)
- [Prerequisites](#prerequisites)
- [Server Information](#server-information)
- [Initial Access](#initial-access)
- [System Updates](#system-updates)
- [SSH Key Authentication](#ssh-key-authentication)
- [Disable Password Login](#disable-password-login)
- [Install and Configure NGINX](#install-and-configure-nginx)
- [Git Configuration](#git-configuration)
- [Testing](#testing)
- [Further References](#further-references)

## Project Overview

Define a cloud-based Ubuntu server configuration for secure remote access and basic web hosting. Implement SSH key-based authentication, disable password login after verification, install and configure NGINX, and prepare Git for repository access from the server.

## Prerequisites

Ensure the following requirements are met before starting:

- A running Ubuntu-based cloud VM.
- SSH access to the VM with a username and password for the first login.
- A local SSH key pair generated on the client machine.
- A Git repository with a feature branch for documenting the setup.

:::warning
Do not commit passwords, private keys, or other sensitive credentials to the repository.
:::

## Server Information

Replace the example values with your actual server details in the final documentation:

- Provider: Cloud provider name
- Operating system: Ubuntu 24.04 LTS
- Server IP: `123.45.67.89`
- Remote user: `example-user`

## Initial Access

1. Open an SSH connection to the server using the assigned username and password:

   ```bash
   ssh example-user@123.45.67.89
   ```

2. Confirm the server fingerprint when prompted so that the host key is stored in the local `known_hosts` file.

## System Updates

1. Update the package lists and install the available upgrades:

   ```bash
   sudo apt update
   sudo apt upgrade
   ```

2. Reboot the server if a new kernel version was installed and reconnect via SSH:

   ```bash
   sudo reboot
   ```

## SSH Key Authentication

### 1. Generate a local SSH key pair

1. Generate an ED25519 SSH key pair on the local machine:

   ```bash
   ssh-keygen -t ed25519
   ```

2. Display the public key on the local machine (Linux/macOS/Bash):

   ```bash
   cat ~/.ssh/id_ed25519.pub
   ```

:::note
If you use Windows, display the key in a Bash shell provided by WSL or Git Bash instead of PowerShell to keep the commands consistent.
:::

### 2. Prepare the `.ssh` directory on the server

1. Create the `.ssh` directory on the server and apply the correct permissions:

   ```bash
   mkdir -p ~/.ssh
   chmod 700 ~/.ssh
   ```

2. Open the `authorized_keys` file in a text editor:

   ```bash
   nano ~/.ssh/authorized_keys
   ```

3. Paste the public key into the file as a single line, save the file, and set the correct permissions:

   ```bash
   chmod 600 ~/.ssh/authorized_keys
   ```

### 3. Test SSH key login

1. Test SSH login with key authentication from the local machine:

   ```bash
   ssh -i ~/.ssh/id_ed25519 example-user@123.45.67.89
   ```

2. Verify that the login works without requiring the server account password.

## Disable Password Login

Verify that SSH key authentication works correctly, then disable password-based login in the SSH server configuration.

### 1. Open the SSH daemon configuration

1. Open the SSH daemon configuration file:

   ```bash
   sudo nano /etc/ssh/sshd_config
   ```

### 2. Update password authentication settings

1. Locate the following line:

   ```text
   #PasswordAuthentication yes
   ```

2. Change it to:

   ```text
   PasswordAuthentication no
   ```

### 3. Validate configuration and restart the service

1. Validate the SSH daemon configuration:

   ```bash
   sudo sshd -t -f /etc/ssh/sshd_config
   ```

2. Restart the SSH service:

   ```bash
   sudo systemctl restart ssh
   ```

### 4. Verify login behavior

1. Open a new SSH session from the local machine using the existing SSH key:

   ```bash
   ssh -i ~/.ssh/id_ed25519 example-user@123.45.67.89
   ```

2. Test that password authentication is disabled by explicitly disabling public key authentication for a test:

   ```bash
   ssh -o PubkeyAuthentication=no example-user@123.45.67.89
   ```

3. Confirm that the command returns a `Permission denied (publickey)` error, showing that password-based login is no longer allowed and only SSH key authentication is accepted.

## Install and Configure NGINX

### 1. NGINX installation

1. Update the package lists (if not done already):

   ```bash
   sudo apt update
   ```

2. Install NGINX:

   ```bash
   sudo apt install nginx -y
   ```

3. Check the status of the NGINX service:

   ```bash
   systemctl status nginx.service
   ```

4. Verify that the service is active and running so that the default NGINX page is served.

### 2. NGINX configuration for an alternative site

1. Create a new document root for the alternative site:

   ```bash
   sudo mkdir -p /var/www/alternatives
   ```

2. Create the HTML file for the alternative site:

   ```bash
   sudo nano /var/www/alternatives/alternate-index.html
   ```

3. Add custom HTML content to the file, for example:

   ```html
   <!doctype html>
   <html>
   <head>
       <meta charset="utf-8">
       <title>Hello, NGINX!</title>
   </head>
   <body>
       <h1>Hello, NGINX!</h1>
       <p>I have just configured my NGINX web server on Ubuntu Server.</p>
   </body>
   </html>
   ```

4. Create a new NGINX site configuration:

   ```bash
   sudo nano /etc/nginx/sites-enabled/alternatives
   ```

5. Add the following server block:

   ```nginx
   server {
       listen 8081;
       listen [::]:8081;

       root /var/www/alternatives;
       index alternate-index.html;

       location / {
           try_files $uri $uri/ =404;
       }
   }
   ```

6. Validate the NGINX configuration:

   ```bash
   sudo nginx -t
   ```

7. Restart NGINX:

   ```bash
   sudo service nginx restart
   ```

8. Open the server IP address with the configured port in a browser:

   ```text
   http://123.45.67.89:8081
   ```

9. Verify that the custom HTML page is displayed instead of the default NGINX landing page.

## Git Configuration

### 1. Configure Git user information

1. Set the Git user name and email on the server:

   ```bash
   git config --global user.name "Your GitHub Name"
   git config --global user.email "your-github-email@example.com"
   ```

2. Verify the configuration:

   ```bash
   git config --global --list
   ```

### 2. Create an SSH key for GitHub access from the server

1. Generate a dedicated ED25519 SSH key pair for GitHub on the server:

   ```bash
   ssh-keygen -t ed25519 -f ~/.ssh/id_ed25519_github -C "github-key-on-server"
   ```

2. Display the public key:

   ```bash
   cat ~/.ssh/id_ed25519_github.pub
   ```

3. Add the public key to your GitHub account under “Settings → SSH and GPG keys” as a new SSH key.

### 3. Configure SSH client for GitHub

1. Create or edit the SSH client configuration file:

   ```bash
   nano ~/.ssh/config
   ```

2. Add the following configuration:

   ```text
   Host github.com
       HostName github.com
       User git
       IdentityFile ~/.ssh/id_ed25519_github
       IdentitiesOnly yes
   ```

3. Test the SSH connection to GitHub from the server:

   ```bash
   ssh -T git@github.com
   ```

4. Confirm that GitHub responds with a success message indicating that authentication works and that no shell access is provided.

## Testing

Use the following checks to verify that the setup is complete:

- Verify that SSH login with key authentication works (see [SSH Key Authentication](#ssh-key-authentication)).
- Verify that password-based login is disabled and fails as expected (see [Disable Password Login](#disable-password-login)).
- Verify that the web server is reachable in the browser and serves the expected content on the configured port (see [Install and Configure NGINX](#install-and-configure-nginx)).
- Verify that a custom HTML page is displayed instead of the default NGINX landing page (see [Install and Configure NGINX](#install-and-configure-nginx)).
- Verify that Git is configured with the correct username and email, and that SSH access to GitHub from the server works (see [Git Configuration](#git-configuration)).

## Further References

- [Ubuntu Server Documentation](https://ubuntu.com/server/docs)
- [OpenSSH Documentation](https://www.openssh.com/manual.html)
- [NGINX Documentation](https://nginx.org/en/docs/)
- [Git Documentation](https://git-scm.com/doc)