# Changelog

All notable changes to the Ubuntu_Standardization project are documented in this file.

The format is based on [Keep a Changelog](https://keepachangelog.com/en/1.0.0/),
and this project adheres to [Semantic Versioning](https://semver.org/spec/v2.0.0.html).

## [Unreleased]

## [1.0.0] - 2026-05-01

### Added

#### Core Features
- **MOTD Configuration** - Deploy custom Message of the Day templates to `/home/user/motd`
  - Template-based configuration using Jinja2 templates
  - File-based MOTD management with proper permissions

- **Landscape System Info** - Automated system information in login banner
  - Deploy `/etc/update-motd.d/50-landscape-sysinfo` script
  - CPU load-aware caching to prevent performance degradation
  - Shows disk usage, uptime, and system information
  - Graceful fallback on high system load

- **Bash Configuration** - Comprehensive shell customization
  - Custom aliases for common commands (ls, cd, git, apt)
  - Fancy Powerline-style bash prompt with host-based color configuration
  - Dynamic PWD truncation for long directory paths
  - 256-color terminal support
  - Automatic sourcing of `.bash_aliases` and `.fancy_bash`

- **Passwordless Sudo** - Enhanced security and convenience
  - Configure sudo without password for user
  - Sudoers syntax validation before deployment
  - Proper file permissions (`0440`) and ownership

- **SSH Key Management** - Remote access automation
  - Automated SSH public key deployment via Semaphore variables
  - Duplicate key detection to prevent multiple entries
  - Proper SSH directory permissions (`0700`)
  - Authorized keys management with correct permissions (`0600`)

- **Hostname Configuration** - Network identity setup
  - Automatic hostname detection using `hostname` command
  - Dynamic `/etc/hosts` entry configuration
  - Format: `127.0.1.1 HOSTNAME.local HOSTNAME`
  - Uses regex-based lineinfile for reliable updates

#### Infrastructure
- **Modular Playbook Structure** - Six independent task modules:
  - `set_motd.yml` - MOTD deployment
  - `set_landscape_sysinfo.yml` - System info script
  - `set_bashrc.yml` - Bash customization
  - `set_sudo.yml` - Sudo configuration
  - `set_ssh_key.yml` - SSH key management
  - `set_hosts.yml` - Hostname configuration

---

## Legend

- **Added** - New features
- **Changed** - Changes to existing functionality
- **Deprecated** - Soon-to-be removed features
- **Removed** - Removed features
- **Fixed** - Bug fixes
- **Security** - Security vulnerability fixes
