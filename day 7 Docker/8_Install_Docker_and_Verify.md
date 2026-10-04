# 8. Docker Installation & Post-Install Setup (Linux / Ubuntu)


### Installation & Verification

```bash
# Update repository index and system packages
sudo apt update && sudo apt upgrade -y

# Install Docker engine
sudo apt install docker.io -y

# Check Docker service status
systemctl status docker

# Verify installed Docker version
docker --version
```

### Non-Root User Configuration

By default, the Docker daemon binds to a Unix socket owned by `root`. To run `docker` commands without `sudo`:

```bash
# Check current logged-in user
whoami

# Add current user ($USER) to the 'docker' group
sudo usermod -aG docker $USER

# Refresh group memberships in current terminal session
newgrp docker

# Verify execution without sudo
docker ps
```
