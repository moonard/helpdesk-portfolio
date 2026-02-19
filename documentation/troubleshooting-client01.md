Cloud Client Workstation Troubleshooting Documentation

Overview
This document details the issues encountered while setting up the Windows Server 2025 workstation (Client01) for Active Directory help desk ticket simulations, and the systematic troubleshooting process used to resolve them. They are listed in chronological order as I encountered them.

Step 1: Unable to Test Multiple Domain Users on Client01

Description: Attempted to simulate help desk scenarios by testing different domain user logins on Client01. Found that I was unable to switch between domain users.

Troubleshooting Procedure:
1. Confirmed that Client01's DNS settings pointed to HL01's private IP address
2. Verified Client01 was joined to correct domain
3. Confirmed AWS security group allowed traffic between instances
4. Verified required AD ports were open

Conclusion: All preliminary checks returned correct information, but domain user authentication was still failing.

Step 2: Network Connectivity Failure - Cannot Reach Domain Controller

Description: During troubleshooting process, found that pinging the domain controller resulted in failure. Indicated fundamental connectivity problem between Client01 and HL01.

Step 3: DNS Resolution Failure

Description: Despite correct DNS settings, running the following command via powershell 
    -nslookup HL01
resulted in the following message: "Error - 'Non-existent domain'". This indicated an issue not on the client, but on the domain controller.

Troubleshooting Procedure:
1. Connect to HL01 to investigate DNS server status
2. Verify DNS server running via
    Get-Service DNS
3. Verified DNS zone exists via
    Get-DnsServerZone
4. Checked for HL01's A record in DNS

Resolution: No A record was found. HL01 never registered itself in the DNS zone, failing to create its own host record. A manual DNS A record was created and successful creation was verified via powershell. Returned to Client01 to test resolution. Flushed DNS cache then tested resolution, resulting in a successful ping to HL01.

Step 4: Domain Users Cannot Authenticate via RDP

Description: After resolving issues, I attempted to log into Client01 as a domain user to begin ticket simulations. After attempting an RDP connection, the credentials were rejected. 

Troubleshooting Procedure:
1. Verified user and password on domain controller via AD Users and Computers
2. Searched online via Google for potential guidance

Resolution: Found that default security configurations restrict RDP access to Administrators. Domain users were valid but lacked appropriate permissions on Client01 to establish an RDP session. Granted RDP permissions to all domain users and made a successful login.

Step 5: Account Lockout Policy Not Configured

Description: Began testing Ticket #01 scenario (password reset after account lockout) by intentionally entering wrong password multiple times. Expected lockout, but failed to trigger it.

Troubleshooting Procedure:
1. Checked domain password policy on HL01
2. Found LockoutThreshold was 0

Resolution: Found that account lockout policy was never configured, despite setting password complexity requirements (as described in the readme). Configured account lockout policy to:
- Lockout Threshold 5
- LockoutDuration 00:30
Returned to Client01 to update Group Policy then tested lockout again. Successfully locked out of account, and began ticketing simulations.

Learning Resources Used
AI-Assisted Learning
Primarily used Anthropic's Claude.ai as primary learning resource throughout troubleshooting. One Google search for "domain users cannot RDP to Windows Server" displayed Gemini AI's response explaining that users msut be added to the Remote Desktop Users group.

Important Distinction
AI tools provided explanations, guidance, and suggested commands, but all commands were executed and outputs analyzed by me. Decisions on implementation were mine. AI guidance at times proved irrelevant, and I adjusted questions accordingly. Technical problems were real, hands-on work was real, and genuine learning was gained.

Diagnostic Commands Used
DNS Troubleshooting
# Test DNS resolution
nslookup HL01

# Check DNS server configuration
ipconfig /all

# Clear DNS cache
ipconfig /flushdns

# On DNS server - check zones
Get-DnsServerZone

# On DNS server - check specific record
Get-DnsServerResourceRecord -ZoneName "preston-lab.local" -Name "HL01"

# On DNS server - create A record
Add-DnsServerResourceRecordA -Name "HL01" -ZoneName "preston-lab.local" -IPv4Address "IP"

Network Connectivity
# Basic connectivity test
ping HL01

# Test specific port connectivity
Test-NetConnection -ComputerName HL01 -Port 389  # LDAP
Test-NetConnection -ComputerName HL01 -Port 88   # Kerberos
Test-NetConnection -ComputerName HL01 -Port 445  # SMB

Active Directory
# Verify user account
Get-ADUser -Identity username

# Check domain password policy
Get-ADDefaultDomainPasswordPolicy

# Set password policy
Set-ADDefaultDomainPasswordPolicy -Identity domain -LockoutThreshold X

# Check local group membership
Get-LocalGroupMember -Group "Remote Desktop Users"

# Add user to local group
Add-LocalGroupMember -Group "Remote Desktop Users" -Member "DOMAIN\user"

Group Policies
# Force policy update
gpupdate /force

# View applied policies
gpresult /r

# Generate detailed HTML report
gpresult /h report.html

Reflection
1. Understanding and troubleshooting DNS is key to Active Directory management
2. Do not assume appropriate user access - verify and grant explicitly
3. Security policies require explicit configuration
4. Systematic troubleshooting saves time and energy
5. Documentation serves as a knowledge base for future issues, proving valuable to maintain efficiency and reduce frustration