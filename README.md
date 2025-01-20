
# n8n Deployment with Cloudflare HTTPS

This repository provides a configuration for deploying an **n8n** instance on a local, network-isolated server with HTTPS certificates issued via **Cloudflare**. The setup is designed to ensure secure access to both the n8n dashboard and webhook endpoints using separate subdomains for additional security and flexibility.

## Purpose

This configuration aims to:
- Rapidly deploy an **n8n** instance with HTTPS enabled.
- Separate access to the n8n editor dashboard and webhook endpoints using unique subdomains.
- Allow webhook access without exposing the n8n dashboard directly to the internet.
- Provide a starting point for securely configuring **n8n** with **Cloudflare Tunnel** and **Caddy**.

### Opinionated Configuration

The setup assumes you will use two distinct subdomains (name them anything you want):
- **n8n.example.com**: For accessing the n8n editor/dashboard - this will be only accessible via a VPN or when you are on your local network.
- **n8n-webhook.example.com**: For webhook operations.

This separation allows:
- Enhanced security for the n8n editor by restricting access via an additional authentication layer.
- Unrestricted webhook functionality while ensuring other access attempts to the webhook subdomain are blocked.

**Caddy** enforces this separation by rejecting non-webhook-related requests made to the `WEBHOOK_HOST` subdomain.

*Note:* It is completely resonable to use a IP address for your n8n console subdomain that is non-routable from outside your intranet. This would allow you to use a VPN for external acess rather than relying on cloudflared. Similarly, if you have no intention of using external services that will require webhooks, do the same for that subdomain.

> **Disclaimer:**  
> Ensure that you perform your own security testing to verify the configuration meets your requirements.

---

## Prerequisites

1. **Cloudflare Account**:
   - A valid domain managed via Cloudflare each domain you intend to use.
   - API tokens with appropriate permissions:
     - **CLOUDFLARE_API_TOKEN**: Read and write access to the host domain.
     - **CLOUDFLARE_TUNNEL_TOKEN**: Token for managing the Cloudflare Tunnel.

2. **Environment Variables**:
   - Configure the `.env` file as outlined below.

3. **External Volumes**:
   - Create the necessary volumes for `caddy` and `n8n` to persist data.

4. **Docker & Docker-Compose**:
   - Ensure both are installed on your server.

---

## Environment Configuration

Create a `.env` file in the root of your project directory, based on the example-env file.

---

## Deployment Instructions

1. **Clone the Repository**:
   ```bash
   git clone <repository-url>
   cd <repository-directory>
   ```

2. **Prepare the Environment**:
   - Create and populate the `.env` file as described above.
   - Ensure the required subdomains are set up in your Cloudflare account.

3. **Start the Services**:
   Run the following command to bring up the services:
   ```bash
   docker-compose up -d
   ```

4. **Access Your n8n Instance**:
   - Dashboard: `https://n8n.example.net`
   - Webhooks: `https://n8n-webhook.example.com`

---

## Security Notes

- **Restrict Access to the Dashboard**: Configure secondary authentication (e.g., Cloudflare Access or VPN) for `N8N_HOST` to prevent unauthorized access.

- **Validate the Configuration**: Test the setup in your environment to confirm that only webhook-related requests are processed on `WEBHOOK_HOST`.

---

## Customization

- **Caddy Configuration**: Modify the `Caddyfile` located in `${DATA_FOLDER}/caddy_config` to tailor the reverse proxy behavior.

- **n8n Configuration**: Add or adjust environment variables in `.env` to meet your specific requirements.

---

## Troubleshooting

- **Logs**: View logs for debugging:
  ```bash
  docker-compose logs -f
  ```
- **Configuration Issues**: Ensure all environment variables are set correctly and match your intended setup.

---

## Acknowledgments

This configuration utilizes:
- [n8n](https://n8n.io) for workflow automation.
- [Caddy](https://caddyserver.com) as a reverse proxy and HTTPS provider.
- [Cloudflare Tunnel](https://developers.cloudflare.com/cloudflare-one/connections/connect-apps/) for secure tunneling.
