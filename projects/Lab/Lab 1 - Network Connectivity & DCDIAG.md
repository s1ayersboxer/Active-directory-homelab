# DC01 Network Connectivity & DCDIAG

## Lab Overview

This lab documents troubleshooting and validation steps for a Windows Server Domain Controller (DC01) running in VMware when the network status shows a yellow triangle (Limited connectivity) despite Active Directory and DNS functioning properly.

This lab also covers why the dcdiag command may be unavailable by default and how to correctly install the required diagnostic tools.

## Environment
- Hypervisor : VMware Workstation Pro
- Server OS : Windows Server (Desktop Experience)
- Role : Domain COntroller (AD DS + DNS)
- Domain login works
- DNS resolution for internal domain succeeds
- DCDIAG command returns: 'dcdiag' is not recognized as an internal or external command.

## Validation Steps Performed

1. Domain Aunthentication Check

Confirmed successful login using a domain account, not a local account:

LAB\\Administrator

This verifies domain authentication and trust health.

2. Network Configuration Verification

Executed: ipconfig /all

Confirmed:
- Valid private IPv4 address assigned
- DNS server points to DC01's own IP
- No public DNS configured on the Domain Controller

This aligns with Microsoft best practices for DCs.

3. DNS Resolution Test

Executed: nslookup lab.local

Confirmed:
- DNS queries resolve to DC01
- DNS service is operational

4. Internet Connectivity Test

Executed: ping 8.8.8.8

Confirmed:
- Network routing is functional
- VMWare NAT adapter is working

## Explanation: Yellow Triangle on Domain Controllers

This Windows network status indicator relies on NCSI (Network Connectivity Status Indicator), which attempts to reach Microsoft public endpoints:
- dns.msftncs1.com
- www.msftconnecttest.com

Because:
- Domain Controller uses internal DNS only
- Public DNS forwarding is not configured

Windows incorrectly reported limited connectivity
This is expected and normal behavior on Domain Controllers.

## DCDIAG Not Recognized - Root Cause

The DCDIAG utility is part of AD DS management tools and is not installed by default on minimal or lab builds.
This does not indicate a broken Domain Controller.

## Resolution: Install AD DS Diagnostic Tools

### Steps
1. Open Server Manager
2. Navigate to: Manage -> Add Roles and Features
3. Proceed to Features
4. Enable: Remote Server Administrator Tools -> AD DS and LDS Tools
5. Install and Reboot if prompted

## Post-Install Validation

Run Command Prompt (CMD) as Administrator:

Executed: DCDIAG

Results: Successful executions confirmed
- AD DS Diagnostics available
- Domain Controller Health can now be validated

## Key Takeaways
- Yellow Network warning on a DC is cosmetic when DNS is internal only
- Domain Controller should not use public DNS servers
- DCDIAG requires AD DS Management tools to be installed
- Proper validation relies on nslookup, domain login, and role health, not the network icon.

## Final Resolution
After validating routing, DNS, and Active Directory Health, the remaining internet connectivity issue was traced to an incorrect DNS configuration on the Domain Controller.

### Root Cause
- Domain Controller was configured to use the loopback address (127.0.01) as its preferred DNS server
- This caused inconsistent external name resolution, Windows update failures, Server manager failures, and persistent NCSI limited connectivity indicators.

### Actions Taken
- Updated DC01's preferred DNS server to its static IPv4 Address
- Configured DNS forwarders to allow recursive resolution for external domains
- Restarted DNS, Netlogon, and Network Location Awareness services
- Verified VMWare NAT routing and default gateway configuration.

### Outcome
Domain Controller now maintains authoritative DNS for internal Active Directory namespace while successfully resolving external domains via forwarders. Active Directory, DNS, Windows Update, and Server Manager telemetry are all functioning as expected.