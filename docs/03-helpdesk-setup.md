# Helpdesk Setup — Windows Server 2022 + Pi services

Swap `<TOKENS>` for your fill-in key values.

## 1. Server VM (VirtualBox)
- Adapter: **Bridged** on the host NIC in the VLAN 20 access port (ASW3 Gi1/0/5)
- Static IP `<SRV-IP>` / `<V20-MASK>`, gateway `<V20-GW>`, DNS `127.0.0.1`

## 2. AD DS + DNS
```powershell
Install-WindowsFeature AD-Domain-Services -IncludeManagementTools
Install-ADDSForest -DomainName "lab.local" -InstallDNS
```

## 3. DHCP (one scope per VLAN)
```powershell
Install-WindowsFeature DHCP -IncludeManagementTools

Add-DhcpServerv4Scope -Name "VLAN30-USERS" -StartRange <V30-DHCP-START> -EndRange <V30-DHCP-END> -SubnetMask <V30-MASK>
Set-DhcpServerv4OptionValue -ScopeId <V30-NET> -Router <V30-GW> -DnsServer <SRV-IP>

Add-DhcpServerv4Scope -Name "VLAN40-IT" -StartRange <V40-DHCP-START> -EndRange <V40-DHCP-END> -SubnetMask <V40-MASK>
Set-DhcpServerv4OptionValue -ScopeId <V40-NET> -Router <V40-GW> -DnsServer <SRV-IP>

Add-DhcpServerv4Scope -Name "VLAN10-MGMT" -StartRange <V10-DHCP-START> -EndRange <V10-DHCP-END> -SubnetMask <V10-MASK>
Set-DhcpServerv4OptionValue -ScopeId <V10-NET> -Router <V10-GW> -DnsServer <SRV-IP>
```

## 4. Directory + a policy
```powershell
New-ADOrganizationalUnit -Name "LAB-Users" -Path "DC=lab,DC=local"
New-ADGroup -Name "Helpdesk" -GroupScope Global -Path "OU=LAB-Users,DC=lab,DC=local"
New-ADGroup -Name "Staff"    -GroupScope Global -Path "OU=LAB-Users,DC=lab,DC=local"
```
- Create users in `LAB-Users`, add to groups.
- Create one GPO (mapped drive **or** password policy), link to `LAB-Users`.
- Join the Win11 client to `lab.local` (client DNS → `<SRV-IP>`).

## 5. Pi services
**Pi-Ticket — GLPI** (`<PI-TICKET-IP>` / `<V20-MASK>`, VLAN 20)
```bash
sudo apt update && sudo apt install -y docker.io docker-compose
# bring up GLPI via the official compose file, then browse http://<PI-TICKET-IP>
```
**Pi-Monitor — Uptime Kuma** (`<PI-MONITOR-IP>` / `<V10-MASK>`, VLAN 10)
```bash
sudo docker run -d --restart=always -p 3001:3001 -v uptime-kuma:/app/data --name uptime-kuma louislam/uptime-kuma:1
# browse http://<PI-MONITOR-IP>:3001 and add ping monitors for each SVI + the server
```
