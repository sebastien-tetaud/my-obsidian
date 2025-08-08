[[Cloudflare Tunnel Setup Guide for FastAPI]]
[[Web Dev]]


This guide will help you set up a secure HTTPS tunnel from Cloudflare to your FastAPI application using Cloudflare Tunnel.

## Prerequisites

- A domain name added to Cloudflare
- FastAPI application running on your server
- Ubuntu/Linux server with root access

## Step 1: Install cloudflared

### Option A: Download and install directly

```bash
# Download the latest cloudflared binary
wget https://github.com/cloudflare/cloudflared/releases/latest/download/cloudflared-linux-amd64.deb

# Install the package
sudo dpkg -i cloudflared-linux-amd64.deb
```

### Option B: Install using package manager

```bash
# Add Cloudflare's GPG key
curl -fsSL https://pkg.cloudflare.com/cloudflared-ascii-pubkey.gpg | sudo tee /usr/share/keyrings/cloudflared.gpg >/dev/null

# Add the repository
echo "deb [signed-by=/usr/share/keyrings/cloudflared.gpg] https://pkg.cloudflare.com/cloudflared $(lsb_release -cs) main" | sudo tee /etc/apt/sources.list.d/cloudflared.list

# Update and install
sudo apt update && sudo apt install cloudflared
```

## Step 2: Authenticate with Cloudflare

```bash
# Login to your Cloudflare account
cloudflared tunnel login
```

This will open a browser window. Select the domain you want to use and authorize the connection.

## Step 3: Create a Tunnel

```bash
# Create a new tunnel (replace 'mapster-api' with your preferred name)
cloudflared tunnel create mapster-api
```

**Important**: Save the tunnel ID that appears in the output. You'll need it later.

## Step 4: Create Tunnel Configuration

```bash
# Create the cloudflared config directory
mkdir -p ~/.cloudflared

# Create the configuration file
nano ~/.cloudflared/config.yml
```

Add the following configuration (replace placeholders with your values):

```yaml
tunnel: YOUR_TUNNEL_ID
credentials-file: /home/ubuntu/.cloudflared/YOUR_TUNNEL_ID.json

ingress:
  - hostname: api.yourdomain.com
    service: http://localhost:8000
  - service: http_status:404
```

### Configuration Explanation:

- `tunnel`: Your tunnel ID from step 3
- `credentials-file`: Path to the credentials file (created automatically)
- `hostname`: Your desired subdomain (e.g., `api.mapster.com`)
- `service`: Your local FastAPI service URL

## Step 5: Create DNS Record

```bash
# Create a DNS record pointing your subdomain to the tunnel
cloudflared tunnel route dns YOUR_TUNNEL_ID api.yourdomain.com
```

Replace:

- `YOUR_TUNNEL_ID` with your actual tunnel ID
- `api.yourdomain.com` with your actual domain

## Step 6: Test the Tunnel

```bash
# Start your FastAPI application
uvicorn app:app --host 0.0.0.0 --port 8000

# In another terminal, run the tunnel
cloudflared tunnel run YOUR_TUNNEL_ID
```

Your API should now be accessible at `https://api.yourdomain.com`

## Step 7: Set Up as a System Service (Recommended)

To run the tunnel automatically on server startup:

```bash
# Install cloudflared as a system service
sudo cloudflared --config /home/ubuntu/.cloudflared/config.yml service install

# Start the service
sudo systemctl start cloudflared

# Enable auto-start on boot
sudo systemctl enable cloudflared

# Check service status
sudo systemctl status cloudflared
```

## Step 8: Update Your FastAPI CORS Settings

Update your FastAPI application to allow requests from your new domain:

```python
from fastapi.middleware.cors import CORSMiddleware

app.add_middleware(
    CORSMiddleware,
    allow_origins=[
        "https://yourdomain.com",           # Your frontend domain
        "https://api.yourdomain.com",       # Your API domain
        "http://localhost:3000",            # Local development
        "http://localhost:5174"             # Vite dev server
    ],
    allow_credentials=True,
    allow_methods=["*"],
    allow_headers=["*"],
)
```

## Step 9: Update Frontend Configuration

Update your frontend to use the new HTTPS API endpoint:

```javascript
// Replace localhost with your Cloudflare domain
const API_BASE = process.env.NODE_ENV === 'production' 
    ? 'https://api.yourdomain.com' 
    : 'http://localhost:8000';

// Use in your API calls
const response = await fetch(`${API_BASE}/create_user_uuid`);
```

## Troubleshooting

### Check tunnel status:

```bash
cloudflared tunnel list
```

### View tunnel logs:

```bash
sudo journalctl -u cloudflared -f
```

### Test local connectivity:

```bash
curl http://localhost:8000/create_user_uuid
```

### Restart the service:

```bash
sudo systemctl restart cloudflared
```

## Security Considerations

1. **Firewall**: With Cloudflare Tunnel, you don't need to open ports 80/443 on your server
2. **Origin certificates**: Consider using Cloudflare Origin Certificates for additional security
3. **Access policies**: Set up Cloudflare Access for additional authentication if needed

## Advanced Configuration

### Multiple services:

```yaml
tunnel: YOUR_TUNNEL_ID
credentials-file: /home/ubuntu/.cloudflared/YOUR_TUNNEL_ID.json

ingress:
  - hostname: api.yourdomain.com
    service: http://localhost:8000
  - hostname: admin.yourdomain.com
    service: http://localhost:9000
  - service: http_status:404
```

### Custom headers:

```yaml
ingress:
  - hostname: api.yourdomain.com
    service: http://localhost:8000
    originRequest:
      httpHostHeader: api.yourdomain.com
      originServerName: api.yourdomain.com
```

## Benefits of Cloudflare Tunnel

✅ **No port forwarding required** - Keeps your server secure  
✅ **Automatic HTTPS** - SSL certificates managed by Cloudflare  
✅ **DDoS protection** - Built-in Cloudflare security  
✅ **Global CDN** - Fast worldwide access  
✅ **Easy setup** - No complex reverse proxy configuration  
✅ **High availability** - Automatic failover and load balancing

Your FastAPI application is now securely accessible over HTTPS through Cloudflare!