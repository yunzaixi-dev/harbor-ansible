# Harbor Ansible Deployment

Automated Harbor deployment using Ansible with SSL/TLS certificate management via Cloudflare DNS.

## Features

- ✅ Automated Harbor installation with Docker Compose
- ✅ SSL/TLS certificates via Let's Encrypt (Cloudflare DNS validation)
- ✅ Automatic certificate renewal
- ✅ Trivy vulnerability scanner integration
- ✅ Fully idempotent playbook (safe to re-run)
- ✅ Production-ready configuration

## Requirements

### Control Node (your machine)
- Ansible 2.9+
- Python 3.6+

### Target Server
- Ubuntu 20.04+ / Debian 10+
- Root or sudo access
- Minimum 4GB RAM, 2 CPU cores
- 40GB+ disk space

### Cloudflare Account
- Domain managed by Cloudflare
- API Token or Global API Key with DNS edit permissions

## Quick Start

### 1. Clone this repository

```bash
git clone https://github.com/yourusername/harbor-ansible.git
cd harbor-ansible
```

### 2. Create inventory file

```bash
cp inventory.example inventory
```

Edit `inventory` and add your Harbor server:

```ini
[harbor]
harbor1 ansible_host=YOUR_SERVER_IP ansible_user=root
```

### 3. Configure variables

Edit `group_vars/all.yml` and update:

- `harbor_hostname`: Your domain name (e.g., `harbor.example.com`)
- `harbor_admin_password`: Strong password for admin user
- `cloudflare_email`: Your Cloudflare account email
- `cloudflare_api_key`: Your Cloudflare API Token
- `harbor_db_password`: Database password
- `docker_compose_version`: Docker Compose version (optional)

### 4. Run the playbook

```bash
ansible-playbook -i inventory playbooks/deploy.yml
```

### 5. Access Harbor

After deployment completes, access Harbor at:

```
https://YOUR_DOMAIN
Username: admin
Password: (the password you set in group_vars/all.yml)
```

## Configuration

### Variables Reference

All configuration variables are in `group_vars/all.yml`:

| Variable | Default | Description |
|----------|---------|-------------|
| `harbor_version` | `v2.14.0` | Harbor version to install |
| `harbor_hostname` | `harbor.example.com` | Domain name for Harbor |
| `harbor_admin_password` | `Harbor12345` | Admin user password |
| `cloudflare_email` | - | Cloudflare account email |
| `cloudflare_api_key` | - | Cloudflare API Token/Key |
| `harbor_install_dir` | `/opt/harbor` | Harbor installation directory |
| `harbor_data_dir` | `/data/harbor` | Harbor data directory |
| `docker_compose_version` | `2.40.0` | Docker Compose version |
| `harbor_http_port` | `80` | HTTP port |
| `harbor_https_port` | `443` | HTTPS port |
| `harbor_db_password` | - | PostgreSQL password |

### Cloudflare API Token

To create a Cloudflare API Token with proper permissions:

1. Log in to Cloudflare Dashboard
2. Go to **My Profile** > **API Tokens**
3. Click **Create Token**
4. Use the **Edit zone DNS** template
5. Set permissions: **Zone** > **DNS** > **Edit**
6. Select your domain under **Zone Resources**
7. Create the token and copy it to `group_vars/all.yml`

## Managing Harbor

### Check status

```bash
ssh root@YOUR_SERVER
cd /opt/harbor/harbor
docker-compose ps
```

### Stop/Start/Restart

```bash
cd /opt/harbor/harbor
docker-compose stop
docker-compose start
docker-compose restart
```

### View logs

```bash
cd /opt/harbor/harbor
docker-compose logs -f
```

### Certificate renewal

Certificates are automatically renewed daily at 2:00 AM. To manually renew:

```bash
/usr/local/bin/renew-harbor-cert.sh
```

## Project Structure

```
harbor-ansible/
├── playbooks/
│   └── deploy.yml          # Main deployment playbook
├── group_vars/
│   └── all.yml            # Configuration variables
├── inventory.example       # Example inventory file
└── README.md
```

## Troubleshooting

### SSL certificate issues

If certificate issuance fails:

1. Verify Cloudflare API credentials
2. Check DNS propagation: `dig harbor.example.com`
3. Review acme.sh logs: `/root/.acme.sh/acme.sh.log`

### Harbor not starting

1. Check Docker status: `systemctl status docker`
2. View Harbor logs: `cd /opt/harbor/harbor && docker-compose logs`
3. Verify disk space: `df -h`

### Re-running the playbook

The playbook is idempotent and safe to re-run. It will:
- Skip already installed components
- Not re-issue valid SSL certificates
- Update only changed configurations

## Security Notes

- Change default passwords in `group_vars/all.yml`
- Keep your Cloudflare API token secure
- Use SSH keys instead of passwords
- Enable firewall on the server
- Regularly update Harbor version

## License

MIT License

## Contributing

Contributions are welcome! Please feel free to submit a Pull Request.

## Support

For issues and questions:
- Open an issue on GitHub
- Check Harbor documentation: https://goharbor.io/docs/

## Acknowledgments

- [Harbor](https://goharbor.io/) - Container registry project
- [acme.sh](https://github.com/acmesh-official/acme.sh) - SSL certificate automation
- [Ansible](https://www.ansible.com/) - Automation platform
