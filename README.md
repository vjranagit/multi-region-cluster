# Multi-Region Cluster Management with Ansible

Ansible-based infrastructure automation for managing a distributed cluster of servers across multiple geographic regions (UK, US, JP, FK) with ZeroTier networking, Docker deployment, and Zabbix monitoring.

## Purpose

Centralized management of geographically distributed servers with:
- Ansible playbooks for automated provisioning and configuration
- ZeroTier VPN mesh network (172.22.22.0/16)
- Docker container deployment
- Zabbix agent monitoring across all nodes
- Multi-inventory support (public IPs, ZeroTier IPs, aliases)

## Architecture

### Geographic Distribution
- **UK** - United Kingdom region
- **US1/US2** - United States regions (multiple availability zones)
- **JP** - Japan region
- **FK** - Falkland Islands region

### Network Configuration
- **Public IPs**: Internet-accessible endpoints
- **ZeroTier Network**: `d3ecf5726d5e9488` (mycluster)
- **ZeroTier Range**: 172.22.22.0/16
- **SSH Access**: Key-based authentication (ubuntu user)

## Directory Structure

```
my-cluster/
├── ansible.cfg                     # Ansible configuration
├── check-connectivity.sh           # Host connectivity validation
├── check-zerotier.sh               # ZeroTier status checker
├── README.md                       # This documentation
├── playbooks_report.md             # Playbook execution report
├── docs/
│   ├── history                     # Command execution history
│   ├── out                         # Ansible output logs
│   ├── requests                    # HTTP request logs
│   └── system.csv                  # System inventory data
├── inventory/
│   ├── combined                    # *EXCLUDED* - All hosts inventory
│   └── n8ncl                       # n8n cluster host
├── keys/
│   └── 11                          # *EXCLUDED* - SSH private key
├── playbooks/
│   ├── add_hosts_to_zabbix.yml     # *EXCLUDED* - Zabbix credentials
│   ├── add_hosts_to_zabbix_v2.yml  # *EXCLUDED* - Zabbix credentials
│   ├── check_n8n.yml               # n8n service verification
│   ├── check_zabbix_agent.yml      # Zabbix agent status check
│   ├── check_zerotier.yml          # ZeroTier connectivity check
│   ├── docker.yml                  # Docker installation
│   ├── playbook.yml                # General configuration
│   ├── pure_ftp.yml                # FTP server setup
│   ├── reinstall_zerotier.yml      # ZeroTier reinstallation
│   ├── system.csv                  # System data export
│   ├── zabbix_mon.yml              # *EXCLUDED* - Zabbix credentials
│   ├── zabbix_mon_fixed.yml        # *EXCLUDED* - Zabbix credentials
│   ├── zerotier-install.yml        # ZeroTier installation
│   └── zerotier-one.yml            # ZeroTier network join
├── gpt4free/
│   └── docker-compose.yml          # GPT4Free deployment
├── zabbix-cli.conf                 # *EXCLUDED* - Zabbix password
├── zabbix-cli-v2.conf              # *EXCLUDED* - Zabbix password
└── zabbix.conf                     # *EXCLUDED* - Zabbix password
```

## Playbooks

### Infrastructure Deployment

**docker.yml** (2.0KB)
- Installs Docker on FK region hosts
- Configures Docker daemon
- Deploys containers

**playbook.yml** (1.2KB)
- General iptables configuration
- Targets 'ok' host group
- Firewall rule management

**pure_ftp.yml** (271 bytes)
- FTP server deployment on US1-4
- Docker-based FTP service

### ZeroTier VPN Mesh

**zerotier-one.yml** (2.6KB)
- ZeroTier installation and configuration
- Limited to FK group initially

**zerotier-install.yml** (1.4KB)
- ZeroTier installation across all hosts
- Network join automation

**reinstall_zerotier.yml** (1.4KB)
- ZeroTier service reinstallation
- Network recovery procedures

**check_zerotier.yml** (1.7KB)
- ZeroTier connectivity validation
- Network status reporting

### Monitoring & Health Checks

**zabbix_mon.yml** (13.9KB) - *EXCLUDED (contains credentials)*
- Zabbix agent installation
- Monitoring configuration
- **Security**: Contains Zabbix admin password

**zabbix_mon_fixed.yml** (1.6KB) - *EXCLUDED (contains credentials)*
- Fixed version of Zabbix monitoring playbook

**check_zabbix_agent.yml** (1.9KB)
- Zabbix agent status verification
- Service health checks

**check_n8n.yml** (761 bytes)
- n8n workflow automation service check

### Zabbix Integration

**add_hosts_to_zabbix.yml** (1.2KB) - *EXCLUDED (contains password)*
- Automated Zabbix host registration
- **Security**: Contains Zabbix API password

**add_hosts_to_zabbix_v2.yml** (1.2KB) - *EXCLUDED (contains password)*
- Updated Zabbix host registration
- **Security**: Contains Zabbix login password

## Inventory Management

### Inventory Files

**combined** - *EXCLUDED from git*
- Comprehensive inventory with all hosts
- Public IPs and ZeroTier IPs
- Geographic grouping
- Unreachable hosts commented out

**n8ncl** (16 bytes)
- Single host for n8n cluster deployment

### Host Groups

- `[UK]`, `[US1]`, `[US2]`, `[jp]`, `[FK]` - Geographic regions
- `[u18]` - Ubuntu 18.04 hosts
- `[working]` - Confirmed reachable hosts
- `[ok]` - Hosts under active management
- `[aliased]` - Hosts with custom names
- `[zerotier_aliased]` - Hosts with ZeroTier network aliases

## Usage

### Connectivity Testing
```bash
# Test all hosts
ansible all -m ping

# Test specific region
ansible UK -m ping

# Use connectivity script
./check-connectivity.sh

# Check ZeroTier status
./check-zerotier.sh
```

### Running Playbooks
```bash
# Install Docker on FK region
ansible-playbook playbooks/docker.yml

# Deploy ZeroTier to all hosts
ansible-playbook playbooks/zerotier-install.yml

# Check n8n service status
ansible-playbook playbooks/check_n8n.yml
```

### Using Different Inventories
```bash
# Use ZeroTier inventory
ansible-playbook -i inventory/hosts_zero playbooks/playbook.yml

# Limit to specific host group
ansible-playbook playbooks/docker.yml --limit UK
```

## SSH Access

**Note**: SSH private key excluded from version control

```bash
# Manual SSH access (requires private key from keys/11)
ssh -i keys/11 ubuntu@<host-ip>
```

## ZeroTier Network

**Network ID**: `d3ecf5726d2e9488` (mycluster)
**IP Range**: 172.22.22.0/16

All hosts configured with ZeroTier mesh network for:
- Private inter-node communication
- Bypassing regional network restrictions
- Secure service-to-service connectivity

```bash
# Check ZeroTier status on host
sudo zerotier-cli listnetworks
sudo zerotier-cli listpeers
```

## GPT4Free Deployment

**gpt4free/docker-compose.yml** (387 bytes)
- Docker Compose configuration for GPT4Free API
- LLM proxy service deployment

## Monitoring Integration

### Zabbix Configuration
- **Server**: Centralized Zabbix monitoring
- **Agent**: Deployed across all cluster nodes
- **Credentials**: Stored in excluded config files

### System Data Export
- `docs/system.csv` - Collected system inventory
- `playbooks/system.csv` - Alternative system data

## Security Notes

### Excluded from Version Control (Credentials & Keys)

**SSH Keys:**
- `keys/11` - Private SSH key for ubuntu user access

**Zabbix Credentials (5 files):**
Default admin credentials: REDACTED






**Inventory:**
- `inventory/combined` - Contains IP addresses and hostnames

### Safe to Commit (No Credentials)

**Playbooks (8 files):**
- check_n8n.yml
- check_zabbix_agent.yml
- check_zerotier.yml
- docker.yml
- playbook.yml
- pure_ftp.yml
- reinstall_zerotier.yml
- zerotier-install.yml
- zerotier-one.yml

**Configuration:**
- ansible.cfg
- gpt4free/docker-compose.yml
- inventory/n8ncl

**Documentation:**
- docs/system.csv
- playbooks_report.md
- README.md

**Scripts:**
- check-connectivity.sh
- check-zerotier.sh

## Requirements

- Ansible 2.9+
- Python 3.6+
- SSH access to all cluster nodes
- ZeroTier client (for VPN mesh)
- Ubuntu 18.04+ on managed hosts

## Related Infrastructure

This cluster integrates with:
- **Zabbix Monitoring** - Centralized monitoring at production node
