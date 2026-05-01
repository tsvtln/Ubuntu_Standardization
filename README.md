# Ubuntu_Standardization

Standardize Ubuntu VMs in my homelab using Ansible.

This playbook provides a modular, reusable approach to configure and standardize Ubuntu systems with essential configurations for both security and usability.

## Features

This playbook configures the following on target Ubuntu systems:

- **MOTD (Message of the Day)** - Custom welcome message at `/home/user/motd`
- **Landscape System Info** - Automated system information display via update-motd.d script
- **Bash Configuration** - Custom aliases and fancy colored bash prompt with Powerline-style separators
- **Passwordless Sudo** - Configure sudo without password requirement for the `user` user
- **SSH Keys** - Automated SSH public key deployment for remote access
- **Hostname Configuration** - Automatically set proper hostname entries in `/etc/hosts`

## Project Structure

```
ubuntu_standardization/
├── main.yml                                    # Main playbook entry point
├── README.md                                   # This file
├── CHANGELOG.md                                # Version history
├── LICENSE                                     # License file
└── roles/
    └── ubuntu_standardization/
        ├── defaults/                           # Default variables
        ├── files/                              # Static files
        ├── handlers/                           # Event handlers
        ├── meta/                               # Role metadata
        ├── tasks/
        │   ├── main.yml                        # Main task orchestrator
        │   ├── set_motd.yml                    # MOTD configuration
        │   ├── set_landscape_sysinfo.yml       # Landscape system info
        │   ├── set_bashrc.yml                  # Bash configuration
        │   ├── set_sudo.yml                    # Sudo configuration
        │   ├── set_ssh_key.yml                 # SSH key deployment
        │   └── set_hosts.yml                   # Hostname configuration
        └── templates/
            ├── motd.j2                         # MOTD template
            ├── 50-landscape-sysinfo.j2         # Landscape sysinfo script
            └── fancy_bash.j2                   # Fancy bash prompt configuration
```

## Prerequisites

- Ansible 2.9 or higher
- Target Ubuntu systems (18.04, 20.04, 22.04, or later)
- SSH access to target systems
- User with sudo privileges (for initial setup)

## Usage

### Basic Execution

```bash
ansible-playbook main.yml -i inventory.ini
```

### With Extra Variables

```bash
ansible-playbook main.yml -i inventory.ini -e "SSHMasterKey='ssh-rsa AAAA...' configure_user='your_vm_username"
```

### Using Semaphore

When running via Semaphore, set the following variable:

- `SSHMasterKey` - Your SSH public key for remote access

## Configuration Variables

### Required Variables

- `SSHMasterKey` - SSH public key to add to `/home/user/.ssh/authorized_keys`

### Target User

All configurations are applied to the `user` user by default.

## Task Details

### 1. set_motd.yml
Deploys a custom MOTD template to `/home/user/motd`. Edit the `templates/motd.j2` file to customize the welcome message.

### 2. set_landscape_sysinfo.yml
Deploys the landscape-sysinfo script to `/etc/update-motd.d/50-landscape-sysinfo` for automatic system information display with caching and load-aware behavior.

### 3. set_bashrc.yml
- Deploys fancy bash prompt configuration to `/home/user/.fancy_bash`
- Adds convenient aliases to `.bashrc`:
  - `ll`, `la`, `l` - Enhanced ls variants
  - `c` - Clear screen
  - `h` - History
  - `..`, `...` - Directory navigation
  - `ze` - Sudo apt-get
  - `gpu`, `gps`, `gc`, `ga` - Git shortcuts
  - `gerp`, `gper` - Typo-tolerant grep aliases
- Configures bash to source both `.bash_aliases` and `.fancy_bash`

### 4. set_sudo.yml
Configures passwordless sudo for the `user` user by creating `/etc/sudoers.d/user` with proper permissions and validation.

### 5. set_ssh_key.yml
- Creates `/home/user/.ssh` directory with correct permissions (`0700`)
- Checks if SSH key already exists to prevent duplicates
- Adds SSH public key to `/home/user/.ssh/authorized_keys`

### 6. set_hosts.yml
- Automatically detects the system hostname
- Updates `/etc/hosts` with proper hostname entries:
  - Example: `127.0.1.1 CHECKPOINTVML.local CHECKPOINTVML`

## Best Practices

- **FQCN Usage** - All modules use Fully Qualified Collection Names (e.g., `ansible.builtin.template`)
- **Idempotency** - All tasks are idempotent and safe to run multiple times
- **Modularity** - Each configuration feature is in its own task file for easy management
- **Validation** - Critical operations include validation (e.g., sudoers syntax check)
- **Duplicate Prevention** - SSH key deployment checks for existing keys

## Customization

### Modify MOTD
Edit `roles/ubuntu_standardization/templates/motd.j2` and replace with your custom content.

### Modify Bash Aliases
Edit the aliases section in `roles/ubuntu_standardization/tasks/set_bashrc.yml`.

### Modify Bash Prompt
Edit `roles/ubuntu_standardization/templates/fancy_bash.j2` to customize colors and prompt format based on hostname.

### Add New Tasks
1. Create a new task file in `roles/ubuntu_standardization/tasks/set_something.yml`
2. Add import statement to `roles/ubuntu_standardization/tasks/main.yml`

## Troubleshooting

### SSH Key Not Being Added
- Ensure `SSHMasterKey` variable is properly set
- Check that `/home/user/.ssh` directory exists with correct permissions
- Verify the key format is valid

### Sudo Configuration Issues
- Run `visudo` on the target system to verify `/etc/sudoers.d/user` syntax
- Ensure file permissions are `0440`

### Bash Configuration Not Applied
- Verify user has logged out and back in after playbook execution
- Check that `~/.bashrc` is being sourced by the shell

## License

See LICENSE file for details.

## Author

This playbook is maintained for personal homelab use.
