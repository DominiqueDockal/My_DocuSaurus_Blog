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

The goal of this project is to configure a cloud-based Ubuntu server for secure remote access and basic web hosting. The setup includes SSH key-based authentication, disabling password login after verification, installing and configuring NGINX, and preparing Git for repository access from the server.

## Prerequisites

Before starting, the following requirements should be met:

- A running Ubuntu-based cloud VM.
- SSH access to the VM with a username and password for the first login.
- A local SSH key pair generated on the client machine.
- A Git repository with a feature branch for documenting the setup.

## Server Information

The following example values are placeholders and should be replaced in the final project documentation:

- Provider: Cloud provider name
- Operating system: Ubuntu 24.04 LTS
- Server IP: `123.45.67.89`
- Remote user: `example-user`

Important: No passwords, private keys, or sensitive credentials should be committed to the repository.

## Initial Access

The first login to the server is done with the assigned username and password:

```bash
ssh example-user@123.45.67.89
```

When connecting to the server for the first time, SSH may ask to confirm the server fingerprint. After confirming the fingerprint, the connection is established and the host is added to the local `known_hosts` file.

## System Updates

After the initial login, the package lists should be updated and the available upgrades should be installed:

```bash
sudo apt update
sudo apt upgrade
```

If the upgrade installs a new kernel version, a reboot is required:

```bash
sudo reboot
```

After the reboot, reconnect to the server with SSH.

## SSH Key Authentication

An ED25519 SSH key pair should be generated on the local machine:

```bash
ssh-keygen -t ed25519
```

On Windows PowerShell, the public key can be displayed like this:

```powershell
type C:\Users\YourUserName\.ssh\id_ed25519.pub
```

The public key should then be copied to the server user's `authorized_keys` file. First, create the `.ssh` directory and apply the correct permissions:

```bash
mkdir -p ~/.ssh
chmod 700 ~/.ssh
```

Then open the `authorized_keys` file:

```bash
nano ~/.ssh/authorized_keys
```

Paste the public key into the file as a single line, save the file, and set the correct permissions:

```bash
chmod 600 ~/.ssh/authorized_keys
```

After that, test the SSH key login from the local machine:

```powershell
ssh -i C:\Users\YourUserName\.ssh\id_ed25519 example-user@123.45.67.89
```

At this point, login should work without requiring the server account password.

## Disable Password Login

After verifying that SSH key authentication was working correctly, password-based login was disabled in the SSH server configuration.

First, the SSH daemon configuration file was opened:

```bash
sudo nano /etc/ssh/sshd_config
```

In this file, the following line was updated:

```text
#PasswordAuthentication yes
```

was changed to:

```text
PasswordAuthentication no
```

After saving the file, the configuration was validated and the SSH service was restarted:

```bash
sudo sshd -t -f /etc/ssh/sshd_config
sudo systemctl restart ssh
```

To ensure that key-based login still worked, a new SSH session was opened from the local machine using the existing SSH key:

```powershell
ssh -i C:\Users\YourUserName\.ssh\id_ed25519 example-user@123.45.67.89
```

Then, password authentication was explicitly tested and confirmed to be disabled by attempting to connect without using public key authentication:

```powershell
ssh -o PubkeyAuthentication=no example-user@123.45.67.89
```

This command returned a `Permission denied (publickey)` error, demonstrating that password-based login was no longer allowed and only SSH key authentication was accepted.

## Install and Configure NGINX

### NGINX installation

To prepare the server for hosting a simple website, the NGINX web server was installed.

First, the package lists were updated (this step was already performed earlier during the initial system setup, but is shown here for completeness):

```bash
sudo apt update
```

Then NGINX was installed using the package manager:

```bash
sudo apt install nginx -y
```

After the installation, the status of the NGINX service was checked to ensure that the web server was running correctly:

```bash
systemctl status nginx.service
```

The status output indicated that the service was active and running, which confirmed that NGINX was installed successfully and the default site was being served by the server.

### NGINX configuration

After installing NGINX, a separate document root was created for a custom landing page.

First, a new directory for the alternative site content was created:

```bash
sudo mkdir /var/www/alternatives
```

Then a new HTML file was created inside this directory:

```bash
sudo touch /var/www/alternatives/alternate-index.html
```

A new NGINX site configuration was added in the `sites-enabled` directory:

```bash
sudo nano /etc/nginx/sites-enabled/alternatives
```

The following server block was used:

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

After that, the custom HTML page was added to the new document root and saved as:

```text
/var/www/alternatives/alternate-index.html
```

The file contained the following content:

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

Before applying the configuration, the NGINX setup was validated with:

```bash
sudo nginx -t
```

The validation completed without errors, confirming that the configuration syntax was correct.

After that, the NGINX service was restarted:

```bash
sudo service nginx restart
```

The final setup was tested in the browser by opening the server IP address together with the configured port:

```text
http://123.45.67.89:8081
```

If the configuration is correct, the browser displays the custom HTML page instead of the default NGINX landing page.

## Git Configuration

To ensure that commits created on the server are associated with the correct GitHub identity, Git was configured with the same user name and email address that are used in the GitHub account.

```bash
git config --global user.name "Your GitHub Name"
git config --global user.email "your-github-email@example.com"
git config --global --list
```

The last command was used to verify that the `user.name` and `user.email` settings were applied correctly.

### SSH key for GitHub access from the server

A dedicated ED25519 SSH key pair was generated on the server for authenticating with GitHub:

```bash
ssh-keygen -t ed25519 -f ~/.ssh/id_ed25519_github -C "github-key-on-server"
```

This created the following files:

- `~/.ssh/id_ed25519_github` (private key, kept on the server)
- `~/.ssh/id_ed25519_github.pub` (public key)

The public key was displayed with:

```bash
cat ~/.ssh/id_ed25519_github.pub
```

and then added to the GitHub account under **Settings → SSH and GPG keys** as a new SSH key.

To ensure that this key is always used for connections to GitHub, an SSH client configuration was added on the server:

```bash
nano ~/.ssh/config
```

with the following content:

```text
Host github.com
    HostName github.com
    User git
    IdentityFile ~/.ssh/id_ed25519_github
    IdentitiesOnly yes
```

After saving the file, the SSH connection to GitHub was tested from the server:

```bash
ssh -T git@github.com
```

GitHub responded with a success message similar to:

```text
Hi YourGitHubUserName! You've successfully authenticated, but GitHub does not provide shell access.
```

This confirms that the server can authenticate to GitHub via SSH using the dedicated key.

## Testing

The final documentation includes the following checks that were completed during the project:

- SSH login with key authentication works (see [SSH Key Authentication](#ssh-key-authentication)).
- Password-based login is disabled and was verified via an explicit connection test (see [Disable Password Login](#disable-password-login)).
- The web server is reachable in the browser and serves the expected content (see [Install and Configure NGINX](#install-and-configure-nginx)).
- A custom HTML page is displayed instead of the default NGINX landing page (see [Install and Configure NGINX](#install-and-configure-nginx)).
- Git is configured with the correct username and email, and SSH access to GitHub from the server works (see [Git Configuration](#git-configuration)).

## Further References

- [Ubuntu Server Documentation](https://ubuntu.com/server/docs)
- [OpenSSH Documentation](https://www.openssh.com/manual.html)
- [NGINX Documentation](https://nginx.org/en/docs/)
- [Git Documentation](https://git-scm.com/doc)
