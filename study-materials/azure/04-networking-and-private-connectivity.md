# 04 — Azure Networking and Private Connectivity

## Why networking matters

A cloud application is not useful if the right users and services cannot reach it. Networking decides where traffic can travel, which endpoints are exposed, and how systems are isolated.

Start with the request path: browser or client → public or private entry point → application → database, storage, model endpoint, or other dependency. Draw this path before changing firewall rules.

## 1. Core concepts

- **Virtual Network (VNet):** a private network boundary in Azure.
- **Subnet:** a range of IP addresses within a VNet, used to organise resources and apply network controls.
- **IP address:** identifies a network interface or endpoint.
- **CIDR:** notation for an IP address range, such as `10.10.0.0/16`.
- **DNS:** resolves names to IP addresses.
- **Route:** determines where packets are sent.
- **Network Security Group (NSG):** rules that allow or deny network traffic for supported network interfaces or subnets.
- **Load balancer:** distributes network traffic across backend instances at supported layers.
- **Application Gateway:** application delivery service with layer 7 capabilities.
- **Private endpoint:** a network interface with a private IP in a VNet that connects to a supported Azure service.
- **VPN / ExpressRoute:** ways to connect networks to Azure, with different connectivity characteristics.

A private IP does not automatically make every part of a system secure. Identity, DNS, routing, firewalls, TLS, and service-level authorisation must agree.

## 2. VNets and subnets

Plan address spaces so that they do not overlap with connected networks. Divide the address range into subnets based on workload and platform requirements.

A simple design might separate:
- Application integration subnet.
- Private endpoint subnet.
- Data or platform components where the service requires a dedicated subnet.

Some Azure services require delegated or specially configured subnets. Check service-specific subnet requirements before designing the address plan.

## 3. NSGs and traffic rules

An NSG contains inbound and outbound security rules. Rules have priorities, direction, protocol, source, destination, and port ranges.

Use the smallest required source and destination ranges. Avoid opening broad port ranges to the internet merely to make a connection succeed. Document why every non-default rule exists and review it periodically.

## 4. Public endpoint vs private endpoint

A public endpoint can be reachable over the internet, though access may still be restricted by authentication, firewall rules, or network controls.

A private endpoint maps a supported service to a private IP address in your VNet. Clients must have a valid network path and DNS resolution to use it.

Typical failure scenario: the private endpoint exists, but the app resolves the service's public hostname to a public address or cannot resolve the private DNS zone. The right fix involves DNS, routing, and service configuration—not just recreating the endpoint.

## 5. Application connectivity

For App Service, Container Apps, Functions, and AKS, networking features vary by hosting option and plan. Understand the difference between:
- Inbound access to the application.
- Outbound access from the application to dependencies.
- Private connectivity between workloads.
- Hybrid connectivity to on-premises systems.

Do not assume that connecting an app to a VNet means every outbound request is private. Verify the exact service feature and routing behaviour.

## 6. Troubleshooting method

When a connection fails, investigate in order:
1. Is the destination service healthy and configured to accept the connection?
2. Does DNS resolve the expected address?
3. Is there a valid route between source and destination?
4. Do NSG, firewall, and service rules allow the traffic?
5. Is TLS configured correctly and is the certificate trusted?
6. Does the caller have the right identity and data-plane permission?
7. Are there application-level errors, timeouts, or connection limits?

Capture the source, destination, port, protocol, time, and correlation ID. Change one variable at a time and record the result.

## Practice exercise

Draw a private architecture for an ASP.NET Core API that reads from Azure SQL, Blob Storage, and an approved model endpoint. Mark every public and private boundary, DNS zone, identity, inbound rule, outbound dependency, and TLS connection. Explain which parts require private endpoint support and what operational work DNS introduces.

## Self-check

- What is the difference between a VNet and a subnet?
- What does an NSG rule evaluate?
- Why can a private endpoint still fail because of DNS?
- What is the difference between inbound and outbound application connectivity?
- Why is network isolation not a replacement for identity authorisation?

## Official references

- Virtual networks: https://learn.microsoft.com/azure/virtual-network/virtual-networks-overview
- Network security groups: https://learn.microsoft.com/azure/virtual-network/network-security-groups-overview
- Private Link: https://learn.microsoft.com/azure/private-link/private-link-overview
- Azure DNS: https://learn.microsoft.com/azure/dns/dns-overview
- VPN Gateway: https://learn.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways
