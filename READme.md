# LAMP Stack Deployment with Ansible

This repository contains an Ansible playbook to deploy and configure a LAMP stack (Linux, Apache, MySQL, PHP) on multiple servers. The playbook is modular and supports multiple environments (e.g., development and production). It does not include Ansible Vault or UFW firewall configuration.

## Table of Contents
- [Overview](#overview)
- [Setup Instructions](#setup-instructions)
- [Inventory Structure](#inventory-structure)
- [How to Run the Playbook](#how-to-run-the-playbook)
- [Testing](#testing)
- [Notes on Security](#notes-on-security)
- [Contributing](#contributing)

## Overview
This Ansible project automates the deployment of a LAMP stack (Linux, Apache, MySQL, PHP) on multiple servers. It includes:
- Installation and configuration of Apache with a sample index.php.
- Installation and secure configuration of MySQL.
- Installation of PHP and confirmation that Apache can process PHP files.
- Support for multiple environments (development and production).

## Setup Instructions

### Prerequisites
1. Install Ansible on your control machine:
   ```bash
   sudo apt install ansible  # For Debian/Ubuntu
   sudo dnf install ansible  # For Red Hat/CentOS/Fedora
   ```
2. Ensure SSH access to the target servers.
3. Clone this repository:
   ```bash
   git clone https://github.com/John1316/lamp-stack.git
   cd lamp-stack
   ```

## Inventory Structure
The inventory is organized into separate directories for different environments (dev, prod). Each environment has its own hosts file and group variables.

```
inventory/
  dev/
    hosts.ini
    group_vars/
      all.yml
  prod/
    hosts.ini
    group_vars/
      all.yml
```

### Example Inventory Files

**inventory/dev/hosts**
```ini
[webservers]
web1-dev ansible_host=192.168.1.10

[dbservers]
db1-dev ansible_host=192.168.1.20
```

**inventory/prod/hosts**
```ini
[webservers]
web1-prod ansible_host=203.0.113.10

[dbservers]
db1-prod ansible_host=203.0.113.20
```

### Environment-Specific Variables
Define environment-specific variables in group_vars/all.yml for each environment.

**inventory/dev/group_vars/all.yml**
```yaml
ansible_user: devuser
ansible_become_password: devpassword
mysql_root_password: "devsecurepassword"
```

**inventory/prod/group_vars/all.yml**
```yaml
ansible_user: produser
ansible_become_password: prodpassword
mysql_root_password: "prodsecurepassword"
```

## How to Run the Playbook
Run the playbook for a specific environment using the `-i` flag to specify the inventory file.

### For Development Environment
```bash
ansible-playbook -i inventory/dev/hosts playbook.yml --ask-become-pass
```

### For Production Environment
```bash
ansible-playbook -i inventory/prod/hosts playbook.yml --ask-become-pass
```

The `--ask-become-pass` flag prompts you to enter the sudo password for the target machines.

## To run mysql

insure of your password in config.ini password root

## Testing
After running the playbook, perform the following tests:

### Apache
* Access the server's IP address in a browser:
  ```
  http://<server-ip>/
  ```
* Verify that a sample index.php file is served correctly.

### PHP
* Create a PHP script to test database connectivity:
  ```php
  <?php
  $mysqli = new mysqli("localhost", "root", "your_password", "test_db");
  if ($mysqli->connect_error) {
      die("Connection failed: " . $mysqli->connect_error);
  }
  echo "Connected successfully";
  ?>
  ```

### MySQL
* Log in to MySQL and verify that databases and users are configured correctly:
  ```bash
  mysql -u root -p
  ```

### phpMyAdmin (if installed)
* Access phpMyAdmin via a browser:
  ```
  http://<server-ip>/phpmyadmin
  ```

### Security
* Verify that sensitive files (e.g., info.php) are removed from /var/www/html.

## Notes on Security
* Use strong passwords for MySQL and other services.
* Restrict access to sensitive applications like phpMyAdmin by IP address or use .htaccess authentication.
* Regularly update packages and apply security patches.
* Monitor logs for suspicious activity:
  ```bash
  tail -f /var/log/apache2/error.log
  tail -f /var/log/mysql/error.log
  ```
