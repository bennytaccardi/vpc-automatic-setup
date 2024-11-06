# Secure VPC Setup Ansible Playbook

This Ansible playbook automates the setup of a secure VPC environment, including SSH configuration, new user creation, and firewall rules.

## Prerequisites

- Ansible installed on your local machine
- Access to a VPC with root credentials
- Basic understanding of SSH key pairs

## Setup Steps

### 1. SSH Key Generation

Generate a new SSH key pair for VPC access:

```bash
ssh-keygen -t rsa -b 4096 -C "your-email@testemail.com"
```

The keys will be generated in your `~/.ssh` directory:

- Private key: `~/.ssh/id_rsa`
- Public key: `~/.ssh/id_rsa.pub`

### 2. VPC Key Configuration

Copy your public key to the VPC's authorized keys:

1. Access your VPC using existing credentials
2. Add the content of `id_rsa.pub` to `~/.ssh/authorized_keys` on the VPC

### 3. Inventory Setup

Create a hosts file (`hosts`) with your VPC details:

```ini
[vpc]
1.1.1.1  # Replace with your VPC's public IP
```

> **Note**: If you modify the `vpc` group name, ensure you update the corresponding reference in `./personalvpc.yml` under the `hosts` property.

### 4. Configuration

1. Edit `./config.yml` to set your preferred VPC name
2. Create an encrypted vault file for sensitive information:
   ```bash
   ansible-vault create secrets.yml
   ```
3. Add the following information to your vault:
   ```yaml
   ansible_user: <vpc_root_user>
   ansible_ssh_private_key_file: <path_to_vpc_pvt_key>
   custom_user_password: <new_user_pwd>
   custom_user: <new_user_username>
   ```

### 5. Execution

Run the playbook with:

```bash
ansible-playbook vpc_playbook.yml --ask-vault-pass
```

Enter your vault password when prompted.

## What Gets Configured

The playbook will:

1. Set up SSH configuration
2. Create a new user with sudo privileges
3. Configure firewall rules
4. Generate new SSH keys for the custom user
5. Create a local directory containing the new SSH keys for future VPC access

## Output

After successful execution, you'll find a new directory on your local machine containing:

- Private and public SSH keys for the new user
- Connection details for accessing the VPC with the new credentials

## Security Notes

- Keep your vault password secure
- Store the generated SSH private keys safely
- Never commit sensitive information to version control
- Regularly rotate SSH keys and passwords

## Troubleshooting

If you encounter issues:

1. Verify your VPC's IP address is correct in the hosts file
2. Ensure root SSH access is working before running the playbook
3. Check that all paths in `secrets.yml` are absolute paths
4. Verify your vault file is properly encrypted

## Additional Configuration

To customize the setup further, you can modify:

- `config.yml` for VPC-specific settings
- `personalvpc.yml` for playbook tasks and roles
- Firewall rules in the respective task files

For support or contributions, please open an issue or pull request in the repository.
