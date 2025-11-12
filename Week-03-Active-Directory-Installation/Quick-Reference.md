# Week 3 Quick Reference - Active Directory Commands

Quick command reference for Active Directory installation and configuration.

---

## Installation Commands

### Install AD DS Role

```powershell
Install-WindowsFeature AD-Domain-Services -IncludeManagementTools
```

### Promote to Domain Controller

```powershell
Install-ADDSForest -DomainName "lab.local" -SafeModeAdministratorPassword (ConvertTo-SecureString "P@ssword123" -AsPlainText -Force) -InstallDns -Force
```

---

## Verification Commands

### Check Domain

```powershell
Get-ADDomain
```

### Check Forest

```powershell
Get-ADForest
```

### Check Domain Controller

```powershell
Get-ADDomainController
```

---

## Organizational Unit (OU) Commands

### Create OU

```powershell
New-ADOrganizationalUnit -Name "ITDepartment" -Path "DC=lab,DC=local"
```

### Create Child OU

```powershell
New-ADOrganizationalUnit -Name "Staff" -Path "OU=ITDepartment,DC=lab,DC=local"
```

### List All OUs

```powershell
Get-ADOrganizationalUnit -Filter * | Select-Object Name, DistinguishedName
```

### Delete OU

```powershell
Remove-ADOrganizationalUnit -Identity "OU=OUName,DC=lab,DC=local" -Confirm:$false
```

---

## User Management Commands

### Create User

```powershell
New-ADUser -Name "Test User" -GivenName "Test" -Surname "User" -SamAccountName "testuser" -UserPrincipalName "testuser@lab.local" -Path "OU=Staff,OU=ITDepartment,DC=lab,DC=local" -AccountPassword (ConvertTo-SecureString "P@ssword123" -AsPlainText -Force) -Enabled $true
```

### List All Users

```powershell
Get-ADUser -Filter * | Select-Object Name, SamAccountName, Enabled
```

### Move User to OU

```powershell
Move-ADObject -Identity "CN=UserName,CN=Users,DC=lab,DC=local" -TargetPath "OU=Staff,OU=ITDepartment,DC=lab,DC=local"
```

### Get User Details

```powershell
Get-ADUser -Identity testuser -Properties *
```

### Delete User

```powershell
Remove-ADUser -Identity testuser -Confirm:$false
```

---

## Group Policy (GPO) Commands

### Create GPO

```powershell
New-GPO -Name "PasswordPolicy" -Comment "Enforces password rules"
```

### Link GPO to OU

```powershell
New-GPLink -Name "PasswordPolicy" -Target "OU=Staff,OU=ITDepartment,DC=lab,DC=local"
```

### Unlink GPO

```powershell
Remove-GPLink -Name "PasswordPolicy" -Target "OU=Staff,OU=ITDepartment,DC=lab,DC=local"
```

### List All GPOs

```powershell
Get-GPO -All | Select-Object DisplayName, GpoStatus, CreationTime
```

### Get GPO Details

```powershell
Get-GPO -Name "PasswordPolicy"
```

### Delete GPO

```powershell
Remove-GPO -Name "PolicyName"
```

### Apply GPO Immediately

```cmd
gpupdate /force
```

### Check Applied GPOs

```cmd
gpresult /r
```

### Generate GPO Report

```powershell
Get-GPResultantSetOfPolicy -ReportType Html -Path C:\gpreport.html
```

---

## Password Policy Commands

### Set Domain Password Policy

```powershell
Set-ADDefaultDomainPasswordPolicy -Identity "lab.local" -MinPasswordLength 10 -ComplexityEnabled $true
```

### View Password Policy

```powershell
Get-ADDefaultDomainPasswordPolicy -Identity "lab.local"
```

---

## Sites and Replication Commands

### Create Site

```powershell
New-ADReplicationSite -Name "BranchOffice"
```

### List All Sites

```powershell
Get-ADReplicationSite -Filter *
```

### Create Subnet

```powershell
New-ADReplicationSubnet -Name "192.168.20.0/24" -Site "BranchOffice"
```

### List All Subnets

```powershell
Get-ADReplicationSubnet -Filter * | Select-Object Name, Site
```

### Check Current Site

```cmd
nltest /dsgetsite
```

### View Site Links

```powershell
Get-ADReplicationSiteLink -Filter *
```

### Check Replication Status

```cmd
repadmin /showrepl
```

### Replication Summary

```cmd
repadmin /replsummary
```

### Force Replication

```cmd
repadmin /syncall
```

---

## Diagnostic Commands

### Check AD Services

```powershell
Get-Service NTDS, DNS, Netlogon, W32Time | Select-Object Name, Status
```

All should show **Running**

### Check DNS

```cmd
nslookup lab.local
```

Should resolve to your DC's IP

### Check FSMO Roles

```powershell
Get-ADDomain | Select-Object InfrastructureMaster, RIDMaster, PDCEmulator
Get-ADForest | Select-Object DomainNamingMaster, SchemaMaster
```

### Test Domain Connectivity

```cmd
nltest /dsgetdc:lab.local
```

### Check AD Database

```cmd
ntdsutil
activate instance ntds
files
quit
quit
```

---

## GUI Tool Shortcuts

| Tool | Command | What It Does |
|------|---------|--------------|
| Active Directory Users and Computers | `dsa.msc` | Manage users, groups, OUs |
| Group Policy Management | `gpmc.msc` | Manage GPOs |
| AD Sites and Services | `dssite.msc` | Manage sites, subnets, replication |
| AD Domains and Trusts | `domain.msc` | Manage domain trusts, functional levels |
| DNS Manager | `dnsmgmt.msc` | Manage DNS zones and records |
| Server Manager | `servermanager` | Central management console |

---

## Common Troubleshooting

### Can't Join Client to Domain

```powershell
Test-ComputerSecureChannel -Verbose
```

### Reset Computer Account

```powershell
Reset-ComputerMachinePassword -Server "DC-Name" -Credential LAB\Administrator
```

### Check AD Health

```cmd
dcdiag /v
```

### Check DNS Health

```cmd
dcdiag /test:dns
```

---

## Best Practices

1. **Always take VM snapshot** before installing AD DS
2. **Document DSRM password** securely
3. **Use static IP** for domain controllers
4. **Configure DNS** properly (DC should point to itself: 127.0.0.1)
5. **Test replication** after any changes
6. **Apply GPOs carefully** - test on small OUs first
7. **Name OUs** to reflect business structure
8. **Use PowerShell** for repeatable configurations

---

## For Your PLR

Include these elements:

**Technical Evidence:**
- Screenshots of each step
- PowerShell command outputs
- Verification results

**Explanation:**
- Why Active Directory vs Workgroup?
- How OU structure reflects business needs
- Why GPO improves security and management

**Reflection:**
- What challenges did you face?
- How did you solve them?
- What would you do differently?

---

**Quick access:** Keep this page open during your lab for fast command reference!

