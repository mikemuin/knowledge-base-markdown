# Docker & Networking Troubleshooting Command Reference

A comprehensive guide for troubleshooting Docker, Portainer, and network connectivity issues in multi-server environments.

**Environment Context:** `comcen` (command center) with Portainer → Agents on: `kr-vm-001`, `hspc-dev-001`, `hspc-prod-001`

------

## Table of Contents

1. [Quick Diagnostic Checklist](https://claude.ai/chat/ff9b341f-5e75-4854-b153-1a476c095682#quick-diagnostic-checklist)
2. [Docker Container Management](https://claude.ai/chat/ff9b341f-5e75-4854-b153-1a476c095682#docker-container-management)
3. [Network Connectivity Testing](https://claude.ai/chat/ff9b341f-5e75-4854-b153-1a476c095682#network-connectivity-testing)
4. [Port and Service Checking](https://claude.ai/chat/ff9b341f-5e75-4854-b153-1a476c095682#port-and-service-checking)
5. [Firewall Management](https://claude.ai/chat/ff9b341f-5e75-4854-b153-1a476c095682#firewall-management)
6. [IP Address Discovery](https://claude.ai/chat/ff9b341f-5e75-4854-b153-1a476c095682#ip-address-discovery)
7. [Cloud Provider Specific](https://claude.ai/chat/ff9b341f-5e75-4854-b153-1a476c095682#cloud-provider-specific)
8. [Portainer Specific](https://claude.ai/chat/ff9b341f-5e75-4854-b153-1a476c095682#portainer-specific)
9. [Common Troubleshooting Workflows](https://claude.ai/chat/ff9b341f-5e75-4854-b153-1a476c095682#common-troubleshooting-workflows)
10. [Advanced Debugging](https://claude.ai/chat/ff9b341f-5e75-4854-b153-1a476c095682#advanced-debugging)

------

## Quick Diagnostic Checklist

**Use this first when troubleshooting connectivity issues:**

```bash
# 1. Verify container is running
docker ps | grep portainer-agent

# 2. Check recent logs for errors
docker logs portainer_agent --tail 50 --timestamps

# 3. Verify port is listening
sudo ss -tlnp | grep 9001

# 4. Test local connectivity
curl -k -v https://localhost:9001/ping

# 5. Check firewall rules
sudo ufw status numbered

# 6. Test from Portainer server (comcen)
curl -k -v https://<agent_ip>:9001/ping
```

**Expected Results:**

- Container: `Up` status
- Logs: No fatal errors or connection refused messages
- Port: `LISTEN` on `0.0.0.0:9001` or `:::9001`
- Local test: HTTP 204 or pong response
- Firewall: Port 9001 allowed from comcen IP
- Remote test: HTTP 204 response

------

## Docker Container Management

### Check Running Containers

```bash
# List all running containers
docker ps

# List all containers (including stopped)
docker ps -a

# Filter for specific container
docker ps | grep portainer
docker ps | grep portainer-agent

# Show container resource usage
docker stats --no-stream

# Show only container names and status
docker ps --format "table {{.Names}}\t{{.Status}}\t{{.Ports}}"
```

**📝 Note:** Use `docker ps -a` to find containers that exited unexpectedly. The `STATUS` column shows uptime or exit codes.

**💡 Tip:** Add `--format` to customize output for easier reading in scripts or when managing many containers.

------

### View Container Logs

```bash
# View all logs
docker logs <container_name>

# View last 20 lines
docker logs <container_name> --tail 20

# View last 50 lines with timestamps
docker logs <container_name> --tail 50 --timestamps

# Follow logs in real-time
docker logs -f <container_name>

# Show logs since specific time
docker logs --since 1h <container_name>
docker logs --since 2024-02-01T10:00:00 <container_name>

# Combine options for detailed debugging
docker logs --tail 100 --timestamps --since 30m <container_name>
```

**📝 Note:** Timestamps (`--timestamps`) are critical for correlating events across multiple servers. Always use them when debugging agent connectivity issues.

**⚠️ Common Issue:** If logs show "EOF" or "connection reset by peer," this typically indicates network interruption between Portainer and agent.

**Example:**

```bash
docker logs portainer_agent --tail 50 --timestamps | grep -i error
```

------

### Restart/Stop/Start Containers

```bash
# Restart a container (graceful)
docker restart <container_name>

# Restart with timeout (force kill after 10s)
docker restart -t 10 <container_name>

# Stop a container (graceful)
docker stop <container_name>

# Stop with immediate kill
docker stop -t 0 <container_name>

# Start a stopped container
docker start <container_name>

# Remove a container (must be stopped first)
docker rm <container_name>

# Force remove a running container
docker rm -f <container_name>

# Remove and recreate (useful for config changes)
docker rm -f <container_name> && docker run [original_parameters]
```

**📝 Note:** Restarting an agent doesn't affect its registration with Portainer, but Portainer may show it as "down" for 30-60 seconds.

**💡 Tip:** Before removing an agent container, save its configuration with `docker inspect portainer_agent > agent-config.json` for reference.

------

### Inspect Container Configuration

```bash
# View full container configuration
docker inspect <container_name>

# View specific configuration (e.g., ports)
docker inspect <container_name> | grep -A 10 "Ports"

# View environment variables
docker inspect <container_name> | grep -A 20 "Env"

# View mounts/volumes
docker inspect <container_name> | grep -A 10 "Mounts"

# View network settings
docker inspect <container_name> | grep -A 20 "Networks"

# Get specific value using format
docker inspect --format='{{.RestartPolicy.Name}}' <container_name>
docker inspect --format='{{range .NetworkSettings.Networks}}{{.IPAddress}}{{end}}' <container_name>

# Export full config to JSON file
docker inspect <container_name> > container-config.json
```

**📝 Note:** Check `RestartPolicy` to ensure agents auto-restart after server reboots. Should be `always` or `unless-stopped`.

**💡 Tip:** When troubleshooting, compare `docker inspect` output between working and non-working agents to spot configuration differences.

------

### Container Health Checks

```bash
# Check container health status
docker inspect --format='{{.State.Health.Status}}' <container_name>

# View health check logs
docker inspect --format='{{range .State.Health.Log}}{{.Output}}{{end}}' <container_name>

# Run manual health check command
docker exec <container_name> curl -f http://localhost:9001/ping || exit 1
```

**📝 Note:** Portainer agents don't have built-in health checks by default. Consider adding one for production environments.

------

## Network Connectivity Testing

### Basic Connectivity Test (Ping)

```bash
# Ping an IP address (4 packets)
ping -c 4 <ip_address>

# Ping continuously with timestamp (Ctrl+C to stop)
ping <ip_address> | while read line; do echo "$(date): $line"; done

# Ping with specific interval (every 5 seconds)
ping -i 5 -c 10 <ip_address>

# Ping a domain
ping -c 4 google.com

# Test with larger packet size (MTU testing)
ping -c 4 -s 1472 <ip_address>
```

**📝 Note:** ICMP (ping) may be blocked by cloud firewalls even when TCP ports are open. A failed ping doesn't mean the service is unreachable.

**💡 Tip:** For agent servers, if ping works but port 9001 doesn't, it's definitely a firewall or service issue, not a routing problem.

**Example:**

```bash
ping -c 4 167.71.205.137
```

------

### Test Specific Port Connectivity

#### Using curl (HTTP/HTTPS)

```bash
# Test HTTPS connection (ignore SSL certificate)
curl -k -v https://<ip_address>:<port>

# Test HTTP connection with verbose output
curl -v http://<ip_address>:<port>

# Test with timeout (fail after 5 seconds)
curl --connect-timeout 5 -k https://<ip_address>:<port>

# Test Portainer agent ping endpoint
curl -k -v https://<ip_address>:9001/ping

# Show only HTTP status code
curl -k -o /dev/null -s -w "%{http_code}\n" https://<ip_address>:9001/ping

# Test with custom headers
curl -k -v -H "X-API-Key: your-key" https://<ip_address>:<port>
```

**📝 Note:** Portainer agent returns HTTP 204 (No Content) on successful ping. Any other code indicates an issue.

**⚠️ Common Error:** "SSL certificate problem" is normal with `-k` flag. Remove `-k` if using proper certificates.

**Example:**

```bash
# Quick test from comcen to agent
curl -k -v https://167.71.205.137:9001/ping
# Expected: < HTTP/1.1 204 No Content
```

------

#### Using telnet

```bash
# Test if port is open (type Ctrl+] then 'quit' to exit)
telnet <ip_address> <port>

# With timeout (useful for scripts)
timeout 5 telnet <ip_address> <port>

# Test multiple services
for port in 9001 22 80; do
  echo "Testing port $port..."
  timeout 2 telnet <ip_address> $port
done
```

**📝 Note:** Telnet shows "Connected" if port is open, "Connection refused" if port is closed, "Connection timed out" if firewall is blocking.

**💡 Tip:** If telnet isn't installed: `sudo apt-get install telnet` (Debian/Ubuntu) or use `nc` instead.

**Example:**

```bash
telnet 167.71.205.137 9001
```

------

#### Using netcat (nc)

```bash
# Test if port is open (verbose mode with zero I/O)
nc -zv <ip_address> <port>

# Test port with timeout
timeout 5 nc -zv <ip_address> <port>

# Test multiple ports
nc -zv <ip_address> 9001 9002 9003

# Test port range
nc -zv <ip_address> 9000-9010

# Keep connection open and send data
echo -e "GET /ping HTTP/1.1\r\nHost: localhost\r\n\r\n" | nc <ip_address> 9001

# Listen mode (useful for reverse testing)
nc -l -p 9001
```

**📝 Note:** `nc` is preferred over telnet for automation and scripting. Returns exit code 0 for success, 1 for failure.

**💡 Tip:** Use `nc` to test from comcen to all agents in a loop:

```bash
for agent in kr-vm-001 hspc-dev-001 hspc-prod-001; do
  echo "Testing $agent..."
  nc -zv -w 2 $agent 9001
done
```

**Example:**

```bash
nc -zv 167.71.205.137 9001
# Output: Connection to 167.71.205.137 9001 port [tcp/*] succeeded!
```

------

### Test from Inside a Container

```bash
# Execute curl inside a container
docker exec <container_name> curl -k https://<ip_address>:<port>

# Execute ping inside a container
docker exec <container_name> ping -c 4 <ip_address>

# Test DNS resolution inside container
docker exec <container_name> nslookup google.com
docker exec <container_name> dig google.com

# Get interactive shell inside container
docker exec -it <container_name> /bin/bash
docker exec -it <container_name> /bin/sh

# Install troubleshooting tools in running container (temporary)
docker exec <container_name> apk add curl  # Alpine-based
docker exec <container_name> apt-get update && apt-get install -y curl  # Debian-based
```

**📝 Note:** Portainer agent containers are Alpine-based. Use `apk` package manager, not `apt`.

**💡 Tip:** Testing from inside the container helps determine if the issue is with Docker networking or the host network.

**Example:**

```bash
# Test if agent can reach Portainer server from inside container
docker exec portainer_agent curl -k https://comcen-ip:9443
```

------

### Advanced Network Testing

```bash
# Trace route to destination
traceroute <ip_address>
mtr -r -c 10 <ip_address>  # Better than traceroute

# Check DNS resolution
nslookup <domain>
dig <domain>
host <domain>

# Test bandwidth between servers (requires iperf3 on both ends)
# On server 1: iperf3 -s
# On server 2: iperf3 -c <server1_ip>

# Monitor network traffic on specific port
sudo tcpdump -i any port 9001 -n -X

# Check network latency
ping -c 100 <ip_address> | tail -1
```

**📝 Note:** `tcpdump` is invaluable for seeing if packets are reaching the server. Install: `sudo apt-get install tcpdump`

------

## Port and Service Checking

### Check What's Listening on Ports

#### Using ss (modern, recommended)

```bash
# Show all listening TCP ports
sudo ss -tlnp

# Show all listening UDP ports
sudo ss -ulnp

# Show both TCP and UDP
sudo ss -tulnp

# Find specific port
sudo ss -tlnp | grep <port>

# Show all connections (not just listening)
sudo ss -tanp

# Show summary statistics
sudo ss -s

# Filter by state
sudo ss -t state established
sudo ss -t state listening

# Show process and timer information
sudo ss -tp
```

**📝 Note:** `ss` is faster than `netstat` and provides more detailed information. It's the modern replacement.

**Output explanation:**

- `LISTEN` = Port is listening for connections
- `0.0.0.0:9001` = Listening on all IPv4 interfaces
- `:::9001` = Listening on all IPv6 interfaces
- `127.0.0.1:9001` = Only listening on localhost (NOT accessible remotely!)
- `users:(("docker-proxy",pid=2576))` = Process using the port

**⚠️ Critical:** If you see `127.0.0.1:9001` instead of `0.0.0.0:9001`, the agent is NOT accessible from other servers. Check your Docker run command's `-p` flag.

**Example:**

```bash
sudo ss -tlnp | grep 9001
# Good: tcp LISTEN 0 4096 0.0.0.0:9001 0.0.0.0:* users:(("docker-proxy",pid=2576))
# Bad: tcp LISTEN 0 4096 127.0.0.1:9001 0.0.0.0:* users:(("docker-proxy",pid=2576))
```

------

#### Using netstat (older, but widely available)

```bash
# Show all listening TCP ports
sudo netstat -tlnp

# Show all listening UDP ports
sudo netstat -ulnp

# Find specific port
sudo netstat -tlnp | grep <port>

# Show all connections with process info
sudo netstat -tanp

# Show network statistics
netstat -s

# Show routing table
netstat -r
```

**📝 Note:** If `netstat` isn't installed: `sudo apt-get install net-tools`

------

#### Using lsof

```bash
# Show what's using a specific port
sudo lsof -i :<port>

# Show all network connections
sudo lsof -i

# Show specific protocol
sudo lsof -i TCP
sudo lsof -i UDP

# Show connections by specific process
sudo lsof -c docker

# Combine filters (TCP port 9001)
sudo lsof -i TCP:9001

# Show IPv4 only
sudo lsof -i 4

# Show IPv6 only
sudo lsof -i 6
```

**📝 Note:** `lsof` shows both listening ports and established connections, with process details.

**Example:**

```bash
sudo lsof -i :9001
# COMMAND PID USER FD TYPE DEVICE SIZE/OFF NODE NAME
# docker-pr 2576 root 4u IPv6 45678 0t0 TCP *:9001 (LISTEN)
```

------

## Firewall Management

### UFW (Uncomplicated Firewall) - Ubuntu/Debian

#### Check Status

```bash
# Check firewall status
sudo ufw status

# Check with more details (numbered rules)
sudo ufw status numbered

# Check verbose (shows default policies)
sudo ufw status verbose

# Show raw rules
sudo ufw show raw
```

**📝 Note:** `inactive` means UFW is disabled. All traffic is allowed unless iptables rules exist.

**💡 Tip:** Always use `status numbered` when you need to delete specific rules.

------

#### Allow/Deny Ports

```bash
# Allow a port (TCP)
sudo ufw allow 9001/tcp

# Allow UDP port
sudo ufw allow 9001/udp

# Allow both TCP and UDP
sudo ufw allow 9001

# Allow from specific IP
sudo ufw allow from <ip_address> to any port 9001

# Allow from specific IP with protocol
sudo ufw allow from <ip_address> to any port 9001 proto tcp

# Allow from subnet
sudo ufw allow from 10.0.0.0/24 to any port 9001

# Deny a port
sudo ufw deny 9001/tcp

# Delete a rule (by number from 'status numbered')
sudo ufw delete <rule_number>

# Delete a rule (by specification)
sudo ufw delete allow 9001/tcp

# Insert rule at specific position
sudo ufw insert 1 allow from <comcen_ip> to any port 9001
```

**📝 Note:** For Portainer agents, it's more secure to allow port 9001 only from the comcen IP rather than from anywhere.

**🔒 Security Best Practice:**

```bash
# Instead of: sudo ufw allow 9001/tcp
# Use: sudo ufw allow from <comcen_ip> to any port 9001 proto tcp
```

**Examples:**

```bash
# Allow Portainer agent port from anywhere
sudo ufw allow 9001/tcp

# Better: Allow only from comcen server
sudo ufw allow from 174.138.21.71 to any port 9001 proto tcp

# Allow SSH from specific IP
sudo ufw allow from 203.0.113.0/24 to any port 22
```

------

#### Enable/Disable UFW

```bash
# Enable firewall (prompts for confirmation)
sudo ufw enable

# Enable without prompt
sudo ufw --force enable

# Disable firewall (temporarily)
sudo ufw disable

# Reset firewall (delete all rules, prompts for confirmation)
sudo ufw reset

# Reload rules (apply changes)
sudo ufw reload
```

**⚠️ Warning:** Enabling UFW on a remote server without allowing SSH first will lock you out! Always run this first:

```bash
sudo ufw allow 22/tcp
sudo ufw enable
```

------

#### UFW Default Policies

```bash
# Set default policies
sudo ufw default deny incoming
sudo ufw default allow outgoing
sudo ufw default deny routed

# Check current defaults
sudo ufw status verbose
```

**📝 Note:** Best practice is `deny incoming`, `allow outgoing`. Explicitly allow needed ports.

------

### iptables (Lower-level firewall)

#### View Rules

```bash
# List all rules
sudo iptables -L

# List with more details (verbose, numeric)
sudo iptables -L -n -v

# List with line numbers
sudo iptables -L --line-numbers

# List specific chain
sudo iptables -L INPUT -n -v
sudo iptables -L OUTPUT -n -v
sudo iptables -L FORWARD -n -v

# Find specific port
sudo iptables -L -n -v | grep <port>

# Show NAT rules (important for Docker)
sudo iptables -t nat -L -n -v

# Show all tables
sudo iptables -t filter -L -n -v
sudo iptables -t nat -L -n -v
sudo iptables -t mangle -L -n -v
sudo iptables -t raw -L -n -v
```

**📝 Note:** Docker extensively uses iptables NAT rules. The `DOCKER` chain contains port mappings for containers.

**💡 Tip:** If UFW shows port allowed but connection still fails, check iptables for conflicting rules.

**Example:**

```bash
sudo iptables -L -n -v | grep 9001
# Look for ACCEPT rules on port 9001
```

------

#### Save/Restore iptables

```bash
# Save current rules (Ubuntu/Debian)
sudo iptables-save > /tmp/iptables-backup.txt
sudo iptables-save | sudo tee /etc/iptables/rules.v4

# Restore rules
sudo iptables-restore < /tmp/iptables-backup.txt

# Make rules persistent across reboots (Debian/Ubuntu)
sudo apt-get install iptables-persistent
sudo netfilter-persistent save
```

**⚠️ Warning:** Docker modifies iptables dynamically. Restoring a saved ruleset may break container networking.

------

### firewalld (CentOS/RHEL/Fedora)

#### Check Status

```bash
# Check if firewalld is running
sudo systemctl status firewalld

# List all rules in default zone
sudo firewall-cmd --list-all

# List rules in specific zone
sudo firewall-cmd --zone=public --list-all

# List open ports
sudo firewall-cmd --list-ports

# List allowed services
sudo firewall-cmd --list-services

# Get default zone
sudo firewall-cmd --get-default-zone

# Get active zones
sudo firewall-cmd --get-active-zones
```

------

#### Allow/Remove Ports

```bash
# Allow a port (temporary - until reload/restart)
sudo firewall-cmd --add-port=9001/tcp

# Allow a port permanently
sudo firewall-cmd --permanent --add-port=9001/tcp
sudo firewall-cmd --reload

# Remove a port
sudo firewall-cmd --permanent --remove-port=9001/tcp
sudo firewall-cmd --reload

# Allow from specific source
sudo firewall-cmd --permanent --add-rich-rule='rule family="ipv4" source address="<comcen_ip>" port port="9001" protocol="tcp" accept'
sudo firewall-cmd --reload
```

**📝 Note:** Changes without `--permanent` flag are lost on reload. Always use `--permanent` for production.

**Example:**

```bash
sudo firewall-cmd --permanent --add-port=9001/tcp
sudo firewall-cmd --reload
sudo firewall-cmd --list-ports  # Verify
```

------

## IP Address Discovery

### Get Your Public IP

```bash
# Using ifconfig.me (fast and reliable)
curl -4 ifconfig.me

# Using icanhazip.com
curl -4 icanhazip.com

# Using ipinfo.io (with more details in JSON)
curl ipinfo.io
curl ipinfo.io/ip  # Just IP
curl ipinfo.io/city  # Just city

# Using AWS IP service
curl -4 checkip.amazonaws.com

# IPv6 address
curl -6 ifconfig.me

# Multiple methods for redundancy
curl -4 ifconfig.me 2>/dev/null || curl -4 icanhazip.com
```

**📝 Note:** These services query what IP they see your request coming from. Essential for configuring cloud firewalls.

**💡 Tip:** Save this to a variable for scripts:

```bash
PUBLIC_IP=$(curl -4 -s ifconfig.me)
echo "This server's public IP: $PUBLIC_IP"
```

------

### Get Local/Private IP

```bash
# Using ip command (modern, recommended)
ip addr show
ip a

# Get specific interface
ip addr show eth0
ip addr show ens3

# Quick one-liner for main IP
hostname -I

# Get just the IP (no interface name)
hostname -i

# Using ifconfig (older, may need net-tools package)
ifconfig

# Get default route interface IP
ip route get 8.8.8.8 | awk '{print $7}'

# Get all IPs (one per line)
hostname -I | tr ' ' '\n'
```

**📝 Note:** VPS servers typically have one public IP on the main interface (eth0, ens3, etc.) and may have a private IP on a secondary interface.

**Example:**

```bash
ip addr show eth0 | grep "inet " | awk '{print $2}' | cut -d/ -f1
# Output: 10.0.0.5
```

------

### Get All Network Interfaces

```bash
# List all interfaces (short)
ip link show

# List all interfaces (detailed)
ip addr show

# Show only interface names
ip -br link show

# Show interfaces with IPs
ip -br addr show

# Show routing table
ip route show

# Show default gateway
ip route | grep default
ip route show default

# Show specific route
ip route get 8.8.8.8
```

**💡 Tip:** `ip -br` (brief) mode is great for quick overview of all interfaces and their status.

------

## Cloud Provider Specific

### DigitalOcean

#### Get Droplet ID

```bash
# Get your droplet ID
curl -s http://169.254.169.254/metadata/v1/id

# Get all metadata
curl -s http://169.254.169.254/metadata/v1/

# Get metadata as JSON (if available)
curl -s http://169.254.169.254/metadata/v1.json | jq .
```

**📝 Note:** Droplet ID is needed for using DigitalOcean CLI (doctl) to manage cloud firewalls.

**💡 Tip:** Save droplet ID for later use:

```bash
DROPLET_ID=$(curl -s http://169.254.169.254/metadata/v1/id)
echo "Droplet ID: $DROPLET_ID"
```

------

#### Get Droplet Information

```bash
# Get droplet hostname
curl -s http://169.254.169.254/metadata/v1/hostname

# Get region
curl -s http://169.254.169.254/metadata/v1/region

# Get public IPv4
curl -s http://169.254.169.254/metadata/v1/interfaces/public/0/ipv4/address

# Get private IPv4 (if available)
curl -s http://169.254.169.254/metadata/v1/interfaces/private/0/ipv4/address

# Get all interfaces
curl -s http://169.254.169.254/metadata/v1/interfaces/

# Get tags
curl -s http://169.254.169.254/metadata/v1/tags
```

**💡 Tip:** Use this in provisioning scripts to automatically configure based on droplet attributes.

------

#### DigitalOcean CLI (doctl)

```bash
# Install doctl (if not installed)
# See: https://docs.digitalocean.com/reference/doctl/how-to/install/

# List all droplets
doctl compute droplet list

# List firewalls
doctl compute firewall list

# Add firewall rule
doctl compute firewall add-rules <firewall_id> \
  --inbound-rules "protocol:tcp,ports:9001,sources:addresses:<comcen_ip>"

# Remove firewall rule
doctl compute firewall remove-rules <firewall_id> \
  --inbound-rules "protocol:tcp,ports:9001,sources:addresses:<ip_address>"
```

**📝 Note:** Requires API token authentication. Generate at: https://cloud.digitalocean.com/account/api/tokens

------

### AWS EC2

#### Get Instance Metadata

```bash
# Get instance ID
curl -s http://169.254.169.254/latest/meta-data/instance-id

# Get public IP
curl -s http://169.254.169.254/latest/meta-data/public-ipv4

# Get private IP
curl -s http://169.254.169.254/latest/meta-data/local-ipv4

# Get availability zone
curl -s http://169.254.169.254/latest/meta-data/placement/availability-zone

# Get region from AZ
curl -s http://169.254.169.254/latest/meta-data/placement/availability-zone | sed 's/[a-z]$//'

# Get instance type
curl -s http://169.254.169.254/latest/meta-data/instance-type

# Get security groups
curl -s http://169.254.169.254/latest/meta-data/security-groups

# Get IAM role
curl -s http://169.254.169.254/latest/meta-data/iam/security-credentials/

# List all available metadata
curl -s http://169.254.169.254/latest/meta-data/
```

**📝 Note:** IMDSv2 (Instance Metadata Service Version 2) requires a token. Use this for newer EC2 instances:

```bash
TOKEN=$(curl -X PUT "http://169.254.169.254/latest/api/token" -H "X-aws-ec2-metadata-token-ttl-seconds: 21600")
curl -H "X-aws-ec2-metadata-token: $TOKEN" http://169.254.169.254/latest/meta-data/instance-id
```

------

### Azure

#### Get Instance Metadata

```bash
# Get instance metadata (requires header)
curl -H Metadata:true "http://169.254.169.254/metadata/instance?api-version=2021-02-01" | jq .

# Get compute info
curl -H Metadata:true "http://169.254.169.254/metadata/instance/compute?api-version=2021-02-01" | jq .

# Get network info
curl -H Metadata:true "http://169.254.169.254/metadata/instance/network?api-version=2021-02-01" | jq .
```

**📝 Note:** Azure metadata service requires `Metadata:true` header and API version parameter.

------

## Portainer Specific

### Check Portainer Container

```bash
# Check Portainer server
docker ps | grep portainer

# View Portainer server logs
docker logs portainer --tail 100 --timestamps

# Check Portainer agent
docker ps | grep portainer-agent

# View Portainer agent logs
docker logs portainer_agent --tail 100 --timestamps

# Follow agent logs in real-time
docker logs -f portainer_agent

# Check for specific errors
docker logs portainer_agent 2>&1 | grep -i "error\|fail\|timeout"

# Check agent resource usage
docker stats --no-stream portainer_agent
```

**📝 Note:** Common log messages to look for:

- "Secured connection established" = Good, agent connected
- "connection refused" = Portainer can't reach agent
- "context deadline exceeded" = Timeout, usually firewall issue
- "EOF" = Connection interrupted

------

### Standard Portainer Agent Installation

```bash
# Install Portainer Agent (Basic)
docker run -d \
  -p 9001:9001 \
  --name portainer_agent \
  --restart=always \
  -v /var/run/docker.sock:/var/run/docker.sock \
  -v /var/lib/docker/volumes:/var/lib/docker/volumes \
  portainer/agent:latest
```

**📝 Note:** This is the minimal configuration. Works for most setups.

------

### Portainer Agent with Extended Timeout

```bash
# Agent with longer timeout (720 hours = 30 days)
docker run -d \
  -p 9001:9001 \
  --name portainer_agent \
  --restart=always \
  -v /var/run/docker.sock:/var/run/docker.sock \
  -v /var/lib/docker/volumes:/var/lib/docker/volumes \
  -e AGENT_CLUSTER_ADDR=<your_server_ip> \
  -e EDGE_ASYNC_TIMEOUT=720h \
  portainer/agent:latest
```

**📝 Note:** `EDGE_ASYNC_TIMEOUT` prevents agent from timing out if you don't connect within 3 days. Useful for staging environments.

**⚠️ Important:** Replace `<your_server_ip>` with the agent server's public IP or hostname.

------

### Portainer Agent with Edge Mode (for NAT/Firewall)

```bash
# Edge agent (polls Portainer server instead of being polled)
docker run -d \
  -p 9001:9001 \
  --name portainer_agent \
  --restart=always \
  -v /var/run/docker.sock:/var/run/docker.sock \
  -v /var/lib/docker/volumes:/var/lib/docker/volumes \
  -v portainer_agent_data:/data \
  -e EDGE=1 \
  -e EDGE_ID=<edge_id_from_portainer> \
  -e EDGE_KEY=<edge_key_from_portainer> \
  -e EDGE_INSECURE_POLL=1 \
  portainer/agent:latest
```

**📝 Note:** Edge mode is useful when agents are behind NAT or can't accept incoming connections. They poll Portainer instead.

------

### Portainer Agent with Custom Network

```bash
# Create custom network first
docker network create portainer-network

# Run agent on custom network
docker run -d \
  --network portainer-network \
  -p 9001:9001 \
  --name portainer_agent \
  --restart=always \
  -v /var/run/docker.sock:/var/run/docker.sock \
  -v /var/lib/docker/volumes:/var/lib/docker/volumes \
  portainer/agent:latest
```

**💡 Tip:** Custom networks enable agent to communicate with other containers by name rather than IP.

------

### Test Portainer Agent

```bash
# Test from the agent server itself (should always work)
curl -k https://localhost:9001/ping

# Test with verbose output
curl -k -v https://localhost:9001/ping

# Test from external server (from comcen)
curl -k https://<agent_ip>:9001/ping
curl -k -v https://<agent_ip>:9001/ping

# Test with timeout
curl --connect-timeout 5 -k https://<agent_ip>:9001/ping

# Get HTTP status code only
curl -k -o /dev/null -s -w "%{http_code}\n" https://<agent_ip>:9001/ping
```

**Expected response:**

- HTTP 204 No Content (success)
- Empty response body
- Connection closes immediately

**📝 Note:** If you get connection refused/timeout, it's a network/firewall issue. If you get different HTTP codes, it's an agent configuration issue.

**Example test script for all agents:**

```bash
#!/bin/bash
AGENTS=("kr-vm-001:10.0.0.10" "hspc-dev-001:10.0.0.11" "hspc-prod-001:10.0.0.12")

for agent in "${AGENTS[@]}"; do
  name="${agent%%:*}"
  ip="${agent##*:}"
  echo "Testing $name ($ip)..."
  if curl -k -s -o /dev/null -w "%{http_code}" --connect-timeout 5 https://$ip:9001/ping | grep -q "204"; then
    echo "  ✓ $name is reachable"
  else
    echo "  ✗ $name is NOT reachable"
  fi
done
```

------

### Portainer Agent Update/Upgrade

```bash
# Stop and remove old agent
docker stop portainer_agent
docker rm portainer_agent

# Pull latest image
docker pull portainer/agent:latest

# Run new agent (use original run command)
docker run -d \
  -p 9001:9001 \
  --name portainer_agent \
  --restart=always \
  -v /var/run/docker.sock:/var/run/docker.sock \
  -v /var/lib/docker/volumes:/var/lib/docker/volumes \
  portainer/agent:latest

# Or use Docker Compose for easier management
```

**💡 Tip:** Use Watchtower to automatically update agents:

```bash
docker run -d \
  --name watchtower \
  --restart=always \
  -v /var/run/docker.sock:/var/run/docker.sock \
  containrrr/watchtower \
  --interval 86400 \
  portainer_agent
```

------

## Common Troubleshooting Workflows

### Workflow 1: "Can't Connect to Portainer Agent"

**Symptoms:** Portainer shows agent as disconnected or timeout errors

```bash
# Step 1: Verify agent container is running
docker ps | grep portainer_agent
# Expected: Container with "Up" status

# Step 2: Check agent logs for errors
docker logs portainer_agent --tail 50 --timestamps | grep -i "error\|fail"

# Step 3: Verify port is listening on all interfaces
sudo ss -tlnp | grep 9001
# Expected: 0.0.0.0:9001 or :::9001 (NOT 127.0.0.1:9001)

# Step 4: Test local connectivity on agent server
curl -k -v https://localhost:9001/ping
# Expected: HTTP/1.1 204 No Content

# Step 5: Check UFW firewall rules
sudo ufw status numbered
# Look for rule allowing port 9001

# Step 6: Test remote connectivity from comcen
curl -k -v https://<agent_ip>:9001/ping
# Expected: HTTP/1.1 204 No Content

# Step 7: If remote test fails, check iptables
sudo iptables -L INPUT -n -v | grep 9001
# Look for ACCEPT rules

# Step 8: Check cloud firewall (DigitalOcean/AWS Security Groups)
# Log into cloud provider console and verify inbound rule for port 9001

# Step 9: Test with netcat from comcen
nc -zv <agent_ip> 9001
# Expected: "Connection succeeded"

# Step 10: Check network route
traceroute <agent_ip>
mtr -r -c 10 <agent_ip>
```

**Common fixes:**

```bash
# Fix 1: Agent only listening on localhost
# Stop and recreate with proper port binding
docker rm -f portainer_agent
docker run -d -p 9001:9001 --name portainer_agent --restart=always \
  -v /var/run/docker.sock:/var/run/docker.sock \
  -v /var/lib/docker/volumes:/var/lib/docker/volumes \
  portainer/agent:latest

# Fix 2: Firewall blocking
sudo ufw allow from <comcen_ip> to any port 9001 proto tcp

# Fix 3: iptables blocking despite UFW allow
# Check Docker iptables rules
sudo iptables -L DOCKER -n -v
```

------

### Workflow 2: "Port Already in Use"

**Symptoms:** `bind: address already in use` when starting container

```bash
# Step 1: Find what's using the port
sudo lsof -i :9001
sudo ss -tlnp | grep 9001

# Step 2: Check if it's another Docker container
docker ps | grep 9001

# Step 3: Check system services
sudo systemctl status | grep -i portainer
sudo systemctl --type=service --state=running

# Step 4: If it's the old agent container, remove it
docker ps -a | grep portainer_agent
docker rm -f portainer_agent

# Step 5: If it's a system service, stop it
sudo systemctl stop <service_name>
sudo systemctl disable <service_name>  # Prevent auto-start

# Step 6: If process is hung, kill it
sudo kill -9 <pid>

# Step 7: Verify port is now free
sudo ss -tlnp | grep 9001
# Should return nothing

# Step 8: Start your container
docker run -d -p 9001:9001 ...
```

**📝 Note:** Always prefer `docker stop` over `docker rm -f` to allow graceful shutdown.

------

### Workflow 3: "Container Keeps Restarting"

**Symptoms:** Container status shows "Restarting" or repeatedly goes up and down

```bash
# Step 1: Check container status and restart count
docker ps -a | grep <container_name>
docker inspect <container_name> --format='{{.RestartCount}}'

# Step 2: View recent logs with timestamps
docker logs <container_name> --tail 100 --timestamps

# Step 3: Look for specific error patterns
docker logs <container_name> 2>&1 | grep -i "error\|fatal\|panic\|exception"

# Step 4: Check for port conflicts
CONTAINER_PORT=$(docker inspect <container_name> --format='{{range $p, $conf := .NetworkSettings.Ports}}{{$p}}{{end}}' | cut -d'/' -f1)
sudo ss -tlnp | grep $CONTAINER_PORT

# Step 5: Check resource limits
docker inspect <container_name> --format='{{.HostConfig.Memory}}'
docker inspect <container_name> --format='{{.HostConfig.CpuShares}}'

# Step 6: Check volume mounts
docker inspect <container_name> --format='{{range .Mounts}}{{.Source}} -> {{.Destination}}{{println}}{{end}}'
# Verify source paths exist and have correct permissions

# Step 7: Try running without restart policy to see real error
docker stop <container_name>
docker rm <container_name>
docker run -it --rm <image_name>  # Interactive, removed on exit

# Step 8: Check system resources
df -h  # Disk space
free -h  # Memory
uptime  # Load average

# Step 9: Check Docker daemon logs
sudo journalctl -u docker --since "10 minutes ago"
sudo cat /var/log/docker.log | tail -50
```

**Common causes:**

1. **Port conflict** - Another service using the same port
2. **Missing volume** - Container can't find required mounted directory
3. **Permission denied** - Container lacks permissions to Docker socket or volumes
4. **Out of memory** - Container killed by OOM killer
5. **Configuration error** - Wrong environment variables or command

------

### Workflow 4: "Slow or Intermittent Connectivity"

**Symptoms:** Agent connects sometimes but frequently disconnects, or very slow

```bash
# Step 1: Check network latency
ping -c 100 <agent_ip> | tail -3
# Look for high average, packet loss

# Step 2: Monitor real-time latency
mtr -r -c 100 <agent_ip>
# Identifies where packet loss/latency occurs

# Step 3: Check for bandwidth saturation
# Install iftop: sudo apt-get install iftop
sudo iftop -i eth0

# Step 4: Monitor Docker network
docker network inspect bridge
docker network inspect <custom_network>

# Step 5: Check container logs for timeout patterns
docker logs portainer_agent --since 1h | grep -i timeout

# Step 6: Test sustained connectivity
while true; do
  echo -n "$(date): "
  curl -k -o /dev/null -s -w "%{http_code} - %{time_total}s\n" --connect-timeout 5 https://<agent_ip>:9001/ping
  sleep 5
done

# Step 7: Check system load on agent server
uptime
top -bn1 | head -20

# Step 8: Monitor Docker daemon performance
docker stats --no-stream

# Step 9: Check for rate limiting or DDoS protection
sudo tail -f /var/log/ufw.log
sudo tail -f /var/log/syslog | grep -i "rate limit"
```

**Common fixes:**

```bash
# Increase agent timeout
docker rm -f portainer_agent
docker run -d -p 9001:9001 --name portainer_agent --restart=always \
  -v /var/run/docker.sock:/var/run/docker.sock \
  -v /var/lib/docker/volumes:/var/lib/docker/volumes \
  -e EDGE_ASYNC_TIMEOUT=720h \
  portainer/agent:latest

# Optimize Docker network MTU
docker network create --opt com.docker.network.driver.mtu=1450 portainer-network
```

------

### Workflow 5: "Agent Connects but Can't Manage Containers"

**Symptoms:** Agent shows as connected in Portainer but can't list/start/stop containers

```bash
# Step 1: Check Docker socket mount
docker inspect portainer_agent --format='{{range .Mounts}}{{.Source}} -> {{.Destination}}{{println}}{{end}}'
# Must include: /var/run/docker.sock -> /var/run/docker.sock

# Step 2: Verify socket permissions
ls -l /var/run/docker.sock
# Should be srw-rw---- with docker group

# Step 3: Check if agent can access Docker API
docker exec portainer_agent docker ps
# Should list containers

# Step 4: Check agent logs for permission errors
docker logs portainer_agent | grep -i "permission\|denied\|unauthorized"

# Step 5: Verify volumes mount
docker inspect portainer_agent --format='{{range .Mounts}}{{if eq .Destination "/var/lib/docker/volumes"}}{{.Source}}{{end}}{{end}}'
# Should show: /var/lib/docker/volumes

# Step 6: Test Docker API directly
docker exec portainer_agent wget -qO- --no-check-certificate http://localhost:9001/api/docker
```

**Common fixes:**

```bash
# Fix: Docker socket not mounted
docker rm -f portainer_agent
docker run -d -p 9001:9001 --name portainer_agent --restart=always \
  -v /var/run/docker.sock:/var/run/docker.sock \
  -v /var/lib/docker/volumes:/var/lib/docker/volumes \
  portainer/agent:latest

# Fix: Permission issues (run agent as root)
docker run -d -p 9001:9001 --name portainer_agent --restart=always \
  -u root \
  -v /var/run/docker.sock:/var/run/docker.sock \
  -v /var/lib/docker/volumes:/var/lib/docker/volumes \
  portainer/agent:latest
```

------

## Advanced Debugging

### Docker Network Debugging

```bash
# List all Docker networks
docker network ls

# Inspect specific network
docker network inspect bridge
docker network inspect <network_name>

# Check container network settings
docker inspect <container_name> --format='{{.NetworkSettings.Networks}}'

# Test DNS resolution inside container
docker exec <container_name> nslookup google.com
docker exec <container_name> cat /etc/resolv.conf

# Create test container for network debugging
docker run --rm -it nicolaka/netshoot

# Check iptables rules created by Docker
sudo iptables -t nat -L -n -v
sudo iptables -L DOCKER -n -v

# Monitor network traffic
sudo tcpdump -i docker0 -n
sudo tcpdump -i any port 9001 -A

# Check Docker network plugin issues
sudo systemctl status docker
docker network prune  # Clean up unused networks
```

------

### System-Level Debugging

```bash
# Check system resource usage
top -bn1 | head -20
htop  # If installed
df -h  # Disk usage
free -h  # Memory usage
iostat -x 2 5  # I/O statistics

# Monitor system logs
sudo journalctl -f  # Follow all logs
sudo journalctl -u docker -f  # Follow Docker logs
sudo tail -f /var/log/syslog
sudo tail -f /var/log/kern.log

# Check for Out Of Memory (OOM) kills
sudo dmesg | grep -i "out of memory\|kill"
sudo journalctl -k | grep -i oom

# Monitor file descriptors (important for Docker)
cat /proc/sys/fs/file-max
cat /proc/sys/fs/file-nr
lsof | wc -l  # Current open files

# Check Docker daemon configuration
sudo cat /etc/docker/daemon.json
docker info

# Verify Docker storage driver
docker info | grep -i storage

# Check Docker disk usage
docker system df
docker system df -v  # Verbose
```

------

### Packet Capture for Deep Analysis

```bash
# Capture traffic on port 9001 to file
sudo tcpdump -i any port 9001 -w /tmp/portainer-capture.pcap

# View capture file
sudo tcpdump -r /tmp/portainer-capture.pcap -A

# Capture with filters (only SYN packets)
sudo tcpdump -i any 'tcp[tcpflags] & (tcp-syn) != 0 and port 9001'

# Capture between two IPs
sudo tcpdump -i any host <comcen_ip> and host <agent_ip> -w /tmp/capture.pcap

# Download capture file for Wireshark analysis
# Then analyze locally with Wireshark GUI
```

------

### Performance Profiling

```bash
# Profile container CPU usage
docker stats <container_name> --no-stream

# Get detailed process info from container
docker top <container_name>
docker top <container_name> aux

# Monitor container events
docker events --filter container=<container_name>

# Check container health history
docker inspect <container_name> --format='{{range .State.Health.Log}}{{.Start}}: {{.Output}}{{println}}{{end}}'

# Benchmark network speed between servers
# On agent: iperf3 -s
# On comcen: iperf3 -c <agent_ip>
```

------

### Automation Scripts

#### Multi-Agent Health Check

```bash
#!/bin/bash
# Save as: check-all-agents.sh

AGENTS=(
  "kr-vm-001:192.168.1.10"
  "hspc-dev-001:192.168.1.11"
  "hspc-prod-001:192.168.1.12"
)

echo "=== Portainer Agent Health Check ==="
echo "Date: $(date)"
echo ""

for agent in "${AGENTS[@]}"; do
  name="${agent%%:*}"
  ip="${agent##*:}"

  echo "Checking $name ($ip)..."

  # Test ping
  if ping -c 2 -W 2 $ip &>/dev/null; then
    echo "  ✓ Ping successful"
  else
    echo "  ✗ Ping failed"
    continue
  fi

  # Test port 9001
  if nc -zv -w 2 $ip 9001 &>/dev/null; then
    echo "  ✓ Port 9001 open"
  else
    echo "  ✗ Port 9001 closed/filtered"
    continue
  fi

  # Test agent endpoint
  http_code=$(curl -k -o /dev/null -s -w "%{http_code}" --connect-timeout 5 https://$ip:9001/ping)
  if [ "$http_code" = "204" ]; then
    echo "  ✓ Agent responding (HTTP $http_code)"
  else
    echo "  ✗ Agent not responding properly (HTTP $http_code)"
  fi

  echo ""
done

echo "=== Check Complete ==="
```

**Usage:**

```bash
chmod +x check-all-agents.sh
./check-all-agents.sh
```

------

#### Automated Firewall Setup

```bash
#!/bin/bash
# Save as: setup-agent-firewall.sh

COMCEN_IP="174.138.21.71"  # Replace with actual comcen IP
AGENT_PORT="9001"

echo "Setting up firewall for Portainer Agent..."

# Enable UFW if not already enabled
sudo ufw --force enable

# Allow SSH (critical!)
sudo ufw allow 22/tcp

# Allow Portainer agent port from comcen only
sudo ufw allow from $COMCEN_IP to any port $AGENT_PORT proto tcp

# Show status
echo ""
echo "Firewall rules:"
sudo ufw status numbered

echo ""
echo "Testing connectivity..."
curl -k https://localhost:$AGENT_PORT/ping
```

------

#### Log Aggregation

```bash
#!/bin/bash
# Save as: collect-agent-logs.sh

OUTPUT_DIR="/tmp/portainer-logs"
mkdir -p $OUTPUT_DIR

echo "Collecting Portainer agent diagnostics..."

# Container logs
docker logs portainer_agent --tail 500 > $OUTPUT_DIR/agent-logs.txt

# Container config
docker inspect portainer_agent > $OUTPUT_DIR/agent-config.json

# System info
{
  echo "=== System Info ==="
  uname -a
  echo ""
  echo "=== Docker Version ==="
  docker version
  echo ""
  echo "=== Network Interfaces ==="
  ip addr show
  echo ""
  echo "=== Listening Ports ==="
  sudo ss -tlnp
  echo ""
  echo "=== Firewall Rules ==="
  sudo ufw status verbose
  echo ""
  echo "=== iptables Rules ==="
  sudo iptables -L -n -v
} > $OUTPUT_DIR/system-info.txt

# Create archive
cd /tmp
tar -czf portainer-diagnostics-$(date +%Y%m%d-%H%M%S).tar.gz portainer-logs/

echo "Diagnostics saved to: /tmp/portainer-diagnostics-*.tar.gz"
```

------

## Quick Reference Cheat Sheet

| Task                     | Command                                          |
| ------------------------ | ------------------------------------------------ |
| Check if port is open    | `nc -zv <ip> <port>`                             |
| See what's on a port     | `sudo ss -tlnp | grep <port>`                    |
| Get public IP            | `curl -4 ifconfig.me`                            |
| Get private IP           | `hostname -I`                                    |
| Allow port in UFW        | `sudo ufw allow from <ip> to any port <port>`    |
| Check firewall           | `sudo ufw status numbered`                       |
| Test HTTPS endpoint      | `curl -k -v https://<ip>:<port>`                 |
| Test agent               | `curl -k https://<ip>:9001/ping`                 |
| View container logs      | `docker logs <container> --tail 50 --timestamps` |
| Restart container        | `docker restart <container>`                     |
| Check running containers | `docker ps`                                      |
| Get droplet ID           | `curl -s http://169.254.169.254/metadata/v1/id`  |
| Find process on port     | `sudo lsof -i :<port>`                           |
| Monitor network          | `sudo tcpdump -i any port <port>`                |
| Check Docker socket      | `ls -l /var/run/docker.sock`                     |
| Docker system info       | `docker system df`                               |
| Monitor resources        | `docker stats --no-stream`                       |

------

## Security Best Practices

### Firewall Configuration

```bash
# 1. Default deny incoming traffic
sudo ufw default deny incoming
sudo ufw default allow outgoing

# 2. Allow SSH from specific IPs only (if possible)
sudo ufw allow from <admin_ip> to any port 22

# 3. Allow Portainer agent only from comcen
sudo ufw allow from <comcen_ip> to any port 9001 proto tcp

# 4. Don't allow 9001 from anywhere
# Bad: sudo ufw allow 9001/tcp
# Good: sudo ufw allow from <comcen_ip> to any port 9001

# 5. Review rules regularly
sudo ufw status numbered

# 6. Use cloud provider firewalls as primary defense
# Configure in DigitalOcean/AWS console
```

------

### Docker Security

```bash
# 1. Don't run containers as root unnecessarily
docker run --user 1000:1000 ...

# 2. Use read-only root filesystem when possible
docker run --read-only ...

# 3. Limit resources
docker run --memory="512m" --cpus="1.0" ...

# 4. Don't expose Docker socket unnecessarily
# Only expose to trusted containers like Portainer agent

# 5. Use specific image tags, not :latest
docker pull portainer/agent:2.19.4  # Not :latest

# 6. Scan images for vulnerabilities
docker scan portainer/agent:latest

# 7. Remove unused images/containers regularly
docker system prune -a

# 8. Enable Docker Content Trust
export DOCKER_CONTENT_TRUST=1
```

------

### Monitoring and Alerts

```bash
# Set up automated health checks
# Use cron to run check-all-agents.sh hourly
crontab -e
# Add: 0 * * * * /path/to/check-all-agents.sh | mail -s "Agent Health" admin@example.com

# Monitor Docker daemon
sudo systemctl enable docker
sudo systemctl status docker

# Set up log rotation
sudo nano /etc/docker/daemon.json
# Add: {"log-driver": "json-file", "log-opts": {"max-size": "10m", "max-file": "3"}}

# Monitor disk usage
df -h | grep -E '(9[0-9]|100)%'  # Alert if >90% full
```

------

## Common Error Messages and Solutions

| Error                                           | Meaning                            | Solution                                           |
| ----------------------------------------------- | ---------------------------------- | -------------------------------------------------- |
| `Connection refused`                            | Port closed or service not running | Check if container is running, verify port binding |
| `Connection timed out`                          | Firewall blocking or network issue | Check UFW, iptables, cloud firewall                |
| `No route to host`                              | Network routing problem            | Check IP address, verify routing table             |
| `context deadline exceeded`                     | Request timeout                    | Check network latency, increase timeout            |
| `port already allocated`                        | Port in use by another container   | Find and stop conflicting container                |
| `bind: address already in use`                  | Port in use by host process        | Find process with `lsof` and stop it               |
| `dial tcp: lookup ... no such host`             | DNS resolution failed              | Check DNS settings, try IP instead                 |
| `permission denied`                             | Insufficient permissions           | Check Docker socket permissions, run as root       |
| `EOF`                                           | Connection closed unexpectedly     | Check logs for crash, verify network stability     |
| `x509: certificate signed by unknown authority` | SSL certificate issue              | Use `-k` flag with curl, or install proper certs   |
| `Cannot connect to the Docker daemon`           | Docker not running or socket issue | Start Docker: `sudo systemctl start docker`        |
| `error during connect: unauthorized`            | Authentication failed              | Check Portainer credentials, agent key             |

------

## Additional Resources

### Documentation

- **Docker Documentation:** https://docs.docker.com/
- **Portainer Documentation:** https://docs.portainer.io/
- **Portainer Agent:** https://docs.portainer.io/admin/environments/add/docker/agent
- **UFW Documentation:** https://help.ubuntu.com/community/UFW
- **DigitalOcean Tutorials:** https://www.digitalocean.com/community/tutorials

### Useful Tools

- **netshoot:** Network troubleshooting container - `docker run --rm -it nicolaka/netshoot`
- **ctop:** Container metrics - https://github.com/bcicen/ctop
- **dive:** Explore Docker images - https://github.com/wagoodman/dive
- **Portainer logs:** https://docs.portainer.io/advanced/troubleshooting

### Monitoring Solutions

- **Prometheus + Grafana:** For metrics
- **ELK Stack:** For log aggregation
- **Uptime Kuma:** For uptime monitoring
- **Netdata:** Real-time performance monitoring

------

## Appendix: Multi-Server Setup Notes

### Current Infrastructure

**Command Center (comcen):**

- Portainer Server installed
- Manages all agent servers
- Should have firewall rules to allow outbound to agents on port 9001

**Agent Servers:**

- `kr-vm-001` - Portainer agent
- `hspc-dev-001` - Portainer agent
- `hspc-prod-001` - Portainer agent

**Required connectivity:**

- comcen → agents (port 9001, HTTPS)
- Agents must allow incoming on 9001 from comcen IP
- Agents don't need to initiate connections to comcen (unless using Edge mode)

------

### Setup Checklist

On each agent server:

- [ ] Docker installed and running
- [ ] Portainer agent container running
- [ ] Port 9001 listening on 0.0.0.0 (not 127.0.0.1)
- [ ] UFW allowing port 9001 from comcen IP
- [ ] Cloud firewall allowing port 9001 from comcen IP
- [ ] Agent responds to `curl -k https://localhost:9001/ping`
- [ ] Agent responds to `curl -k https://<agent_ip>:9001/ping` from comcen
- [ ] Container restart policy set to `always`
- [ ] Docker socket mounted: `/var/run/docker.sock:/var/run/docker.sock`
- [ ] Volumes mounted: `/var/lib/docker/volumes:/var/lib/docker/volumes`

On comcen:

- [ ] Can reach all agents on port 9001
- [ ] All agents added to Portainer
- [ ] All agents showing as "connected" status
- [ ] Can manage containers on all agents from Portainer UI

------

### Maintenance Tasks

**Weekly:**

- Check agent connectivity: `./check-all-agents.sh`
- Review agent logs for errors
- Verify container resource usage

**Monthly:**

- Update Portainer agent images
- Review firewall rules
- Clean up unused Docker resources: `docker system prune`
- Check disk space on all servers

**Quarterly:**

- Update Docker on all servers
- Review and update firewall rules
- Test disaster recovery procedures
- Update documentation

------

**Document Version:** 2.0
**Last Updated:** 2024-02-01
**Maintained For:** comcen + kr-vm-001, hspc-dev-001, hspc-prod-001

------

*This reference guide is optimized for troubleshooting Docker and Portainer in production environments.*ß