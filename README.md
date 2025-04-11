# JIRA SSL Renewal with JKS Management

This repository contains an Ansible playbook for renewing the SSL certificate of a JIRA server and managing the associated Java Keystore (JKS) file.

## Files in this repository

1. **`jira_ssl_renewal_jks.yaml`**: The main Ansible playbook for automating the SSL renewal and JKS file management.
2. **`secrets.yml`**: The encrypted file containing sensitive variables such as the domain name, keystore password, and keystore alias.
3. **`jira.ini`**: An Ansible inventory file for defining the JIRA server and SSH connection details.

---

## Overview

The playbook `jira_ssl_renewal_jks.yaml` performs the following key tasks:
- Checks if the existing JKS file is present.
- Creates a backup of the current JKS file, appending the current date to the filename.
- Renews the SSL certificate using Certbot in standalone mode.
- Converts the renewed SSL certificate into a PKCS12 file and then into a JKS file.
- Sets appropriate permissions for the new JKS file.

---

## Prerequisites

1. **Ansible**: Ensure that Ansible is installed on your control machine.
2. **Certbot**: Certbot should be installed on the JIRA server for renewing the SSL certificate.
3. **OpenSSL**: OpenSSL should be available on the JIRA server to handle certificate conversion.
4. **Keytool**: The `keytool` utility must be available on the JIRA server for JKS management.
5. **SSH Access**: Ensure you have SSH access to the JIRA server using the private key specified in `jira.ini`.

---

## Setup Instructions

1. Clone this repository:
   ```bash
   git clone <repository-url>
   cd jira_ssl_renewal_jks
   ```

2. **Edit the `jira.ini` file**:
   - Replace the IP address, SSH user, and private key path with your server details.

   Example:
   ```ini
   [jira_server]
   65.0.203.162 ansible_ssh_private_key_file=/path/to/your/private/key.pem ansible_ssh_user=ubuntu
   ```

3. **Create and Encrypt `secrets.yml`**:
   - Create a new `secrets.yml` file with the following structure:
     ```yaml
     domain_name: "jira.mydomain.shop"
     keystore_pass: "your_keystore_password"
     keystore_alias: "your_keystore_alias"
     ```
   - Encrypt the file using Ansible Vault:
     ```bash
     ansible-vault encrypt secrets.yml
     ```

4. **Check and Update Variables in the Playbook**:
   - Open `jira_ssl_renewal_jks.yaml` and ensure the variables such as `jks_path`, `backup_dir`, and `temp_p12_path` match your environment.

---

## How to Run the Playbook

1. Run the playbook with the following command:
   ```bash
   ansible-playbook -i jira.ini jira_ssl_renewal_jks.yaml --ask-vault-pass
   ```
   - Use `--ask-vault-pass` to enter the password for decrypting `secrets.yml`.

2. The playbook will:
   - Backup the existing JKS file (if it exists).
   - Renew the SSL certificate using Certbot.
   - Convert the certificate to a JKS file and set proper permissions.

---

## Troubleshooting

1. **Certbot Issues**:
   - If Certbot renewal fails, ensure that the JIRA server's port 80 is accessible for HTTP-01 challenges.

2. **Ansible Vault Errors**:
   - If you encounter decryption errors, verify the password used to encrypt `secrets.yml`.

3. **Keytool Errors**:
   - Ensure that the `keytool` command is installed and accessible on the JIRA server.

---

## Notes

- The playbook is designed to be idempotent and will only make changes when necessary.
- Always backup your `secrets.yml` file and ensure it is securely encrypted.

---

## Example `secrets.yml` File

Below is an example of how the `secrets.yml` file should look before encryption:

```yaml
domain_name: "jira.mydomain.shop"
keystore_pass: "vishal@chinu"
keystore_alias: "jira"
```

After creating the file, encrypt it using:
```bash
ansible-vault encrypt secrets.yml
```

---

## License

This project is licensed under the MIT License. See the LICENSE file for details.