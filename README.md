# Ansible-Powered Secure Docker Compose Environment

## Overview
This repository provides a comprehensive, Ansible-automated setup for deploying a multi-VM environment with various containerized services. It's designed to create a robust, secure, and monitored infrastructure suitable for small to medium-sized applications, with a focus on flexible routing, independent operation of each VM, enhanced security, and unified logging.

## Purpose
The main goals of this project are:
1. To provide an easily deployable, secure ingress point for web applications
2. To set up centralized authentication and DNS services
3. To establish a centralized logging and monitoring solution
4. To demonstrate best practices in containerized application deployment
5. To ensure each VM can operate independently while still being part of a cohesive system
6. To implement strong security measures across all components
7. To provide unified logging and metrics collection

## Key Features
- Ansible-lint compliant for best practices in Ansible usage
- Enhanced security with non-root users for Docker containers
- User namespace remapping (userns-remap) to non-root users for additional container isolation
- Unified logging and metrics collection using Promtail and Loki

## Architecture

Our setup consists of three main Virtual Machines, each with its own Traefik instance and Promtail agent:

1. **demo-VM1-Ingress**: Acts as the main entry point and security layer
   - Main Traefik: Primary reverse proxy and load balancer, connected to Cloudflare
   - CrowdSec & CrowdSec-Traefik-Bouncer: Intrusion detection and prevention
   - Authelia & Authentik: Authentication services
   - AdGuard / PowerDNS: DNS services
   - Promtail Agent: Log collection

2. **demo-VM2-Metrics**: Handles logging and monitoring
   - Traefik: Local reverse proxy for metrics services
   - Loki: Log aggregation system
   - Grafana: Visualization and monitoring
   - Other metrics tools
   - Promtail Agent: Log collection

3. **demo-VM3-Application-1**: Hosts the actual applications
   - Traefik: Local reverse proxy for applications
   - Vaultwarden: Password manager
   - WikiJS: Wiki system
   - Other containerized applications
   - Promtail Agent: Log collection

The main Traefik instance on demo-VM1-Ingress acts as the entrypoint for external traffic, routing requests to the appropriate VM based on the application. Each VM's local Traefik instance then handles internal routing to the specific services within that VM.

## Security Measures
- All Docker containers run as non-root users with specific UID:GID mappings
- User namespace remapping (userns-remap) is implemented for additional container isolation
- Ansible playbooks follow ansible-lint best practices for secure and efficient configuration

## Logging and Monitoring
- Each VM has its own Promtail agent for log collection
- All logs are forwarded to the centralized Loki instance on demo-VM2-Metrics
- Grafana provides visualization and alerting based on the collected logs and metrics

## Traffic Flow
1. External requests come through Cloudflare to the main Traefik instance on demo-VM1-Ingress.
2. The main Traefik instance routes the traffic to the appropriate VM based on the requested application.
3. The local Traefik instance on the target VM then directs the traffic to the specific service.

This setup allows for:
- Centralized external access point and security
- Independent operation of each VM if needed
- Flexibility in routing and service management
- Comprehensive logging and monitoring across all services

## Technologies Used
- Ansible: For automating the deployment and configuration
- Docker & Docker Compose: For containerization
- Traefik: For both main and local reverse proxying
- Promtail & Loki: For unified logging
- Grafana: For metrics visualization and alerting
- Proxmox: As the virtualization platform

## Prerequisites
- Proxmox server
- Basic understanding of Ansible, Docker, and networking
- Cloudflare account for DNS management and additional security

## Setup Guide
1. Set up Proxmox and create the required VMs
2. Configure Cloudflare for your domain
3. Clone this repository
4. Modify the Ansible inventory and variables as needed
5. Run the Ansible playbooks to deploy the environment

Detailed setup instructions can be found in the `SETUP.md` file.

## Contributing
Contributions are welcome! Please read our `CONTRIBUTING.md` file for guidelines on how to submit changes.

## License
This project is licensed under the GPL-3.0 License - see the `LICENSE` file for details.

## Acknowledgments
- Community contributions and feedback are greatly appreciated

For more detailed information on each component, please refer to the individual documentation in the `docs/` directory.
