# Active Directory Lab - AWS Cloud Implementation

## Overview

Built a fully functional Active Directory domain environment on AWS EC2 to demonstrate IT infrastructure management, user lifecycle administration, access control, Group Policy implementation, and systematic troubleshooting methodology.

**Key Achievement:** Created production-ready lab environment demonstrating hands-on capability with Windows Server administration, Active Directory management, and cloud infrastructure deployment via AWS.

**Environment:**
- **Domain Controller:** Windows Server 2025 running AD DS, DNS
- **Client Workstation:** Domain-joined Windows machine
- **Cloud Platform:** AWS EC2 (Free Tier)
- **Domain:** PRESTON-LAB.local
- **Network:** VPC with custom security groups

## Skills Demonstrated

### Active Directory Creation and Management
- Domain controller deployment and promotion
- Organizational Unit structure design
- User account lifecycle management (10+ accounts)
- Security group creation and membership administration
- NTFS permissions and shared folder access control
- Group Policy Object creation and linking

### Cloud Infrastructure
- AWS EC2 instance management
- Security group configuration for AD traffic
- VPC networking for domain environment
- Cost optimization using Free Tier resources

### Troubleshooting & Problem Solving
- DNS configuration and verification
- Network connectivity troubleshooting
- Systematic debugging methodology

### Documentation
- Step-by-step troubleshooting documentation
- Root cause analysis

## Project

### Phase 1: Infrastructure Setup

Deployed two Windows Server 2025 instances on AWS EC2:
- Domain Controller
- Client Workstation

Configured security groups to allow required AD traffic:
- RDP (port 3389)
- DNS (port 53)
- All traffic within VPC subnet (172.31.0.0/16)

### Phase 2: Domain Controller Promotion

Installed Active Directory Domain Services role and promoted server to domain controller:
- Created new forest: PRESTON-LAB.local
- Configured DNS services
- Set DSRM recovery password

**Challenge 1:** Initial installation included unnecessary AD Certificate Services, blocking DC promotion.

**Solution:** Removed unnecessary roles, reinstalled with only AD DS, resulted in successful promotion.

### Phase 3: Domain Join & DNS Configuration

Configured Client Workstation to use Domain Controller as DNS server (Private IP: 172.31.X.X):
- Updated network adapter DNS settings
- Verified resolution with nslookup
- Successfully joined client to domain

**Challenge 2:** DNS queries timed out despite correct DNS configuration.

**Solution:** Added ICMP and DNS rules to security group inbound rules in EC2, enabling proper name resolution.

**Challenge 3:** Domain join failed with "AD DC could not be contacted" error.

**Solution:** Opened all traffic within VPC subnet to ensure complete AD protocol communication.

### Phase 4: Organizational Structure

Created 5 Organizational Units:
- IT_Staff (2 users)
- Sales (2 users)
- HR (2 users)
- Finance (2 users)
- Disabled_Users (for offboarding)

Created corresponding security groups for each department and configured group memberships.

### Phase 5: Shared Resources & Permissions

Implemented departmental shared folders:
- Created C:\Shared\ with department subdirectories
- Configured NTFS permissions
- Removed default "Everyone" permissions
- Assigned security group access (Read/Write for respective departments)
- Tested access control from client workstation

### Phase 6: Group Policy Implementation

Created and deployed Password Policy GPO:
- Password history: 5 passwords
- Maximum age: 90 days
- Minimum age: 1 day
- Minimum length: 10 characters
- Complexity requirements: Enabled

Linked GPO to domain and verified application via cmd:
```bash
gpupdate /force
gpresult /r
```

## Challenges & Solutions Summarized

| Challenge | Consequence | Resolution |
|-----------|--------|-----------|
| AD CS blocking DC promotion | Installation failure | Removed unnecessary roles |
| DNS timeout on client | Domain join blocked | Added ICMP/DNS to security group |
| Domain controller unreachable | Authentication failed | Opened all VPC subnet traffic |

## Future Additions
- [ ] Create ticket documentation of common user issues (forgot password, locked out of account)
- [ ] Setup additional GPOs (software deployment, desktop restrictions)
- [ ] Write scripts to optimize workflow
- [ ] Configure automated backup solution
- [ ] Add monitoring and alerting
- [ ] Deploy second domain controller for redundancy
- [ ] Integrate with Azure

## Repository Structure
```
aws-active-directory-lab/
├── README.md
├── documentation/
│   ├── infrastructure-diagram.png
│   └── screenshots/

```

## Technologies Used

- Windows Server 2025
- Active Directory Domain Services
- AWS EC2
- AWS VPC
- Group Policy Management
- Remote Desktop Protocol

## Contact

Joseph Preston Kang
- LinkedIn: [[Joseph Kang]](https://www.linkedin.com/in/joseph-kang-845162224/)
- Email: kangpreston@gmail.com

---

*This lab demonstrates foundational skills for Help Desk Tier 1 and IT Support Specialist roles, including Active Directory administration, systematic troubleshooting, documentation practices, and automation mindset.*
