# Week 3 Lab Guide: Installing and Configuring Active Directory

Complete guide showing both GUI (Server Manager) and Terminal (PowerShell) methods.

---

## Lab Overview

### What is Week 3 About:
**Installing and Configuring Active Directory Domain Services (AD DS)**

### PC Requirements:
- **Windows Server 2019 or 2022** (VM or physical)
- Administrator privileges
- Static IP configuration

### What You'll Do:
- **Activity 1:** Install AD DS and create a domain (lab.local)
- **Activity 2:** Create Organizational Units (OUs) and apply Group Policy Objects (GPOs)
- **Activity 3:** Configure AD Sites and verify replication

---

## Important Terms

| Term | Definition |
|------|------------|
| Active Directory (AD) | Microsoft's centralized system for managing users, computers, and resources |
| Domain Controller (DC) | Server that manages authentication and authorization for a domain |
| AD DS | Active Directory Domain Services - the main AD component |
| Forest | Top-level container in AD (can contain multiple domains) |
| Domain | A security boundary containing users, computers, and resources |
| Organizational Unit (OU) | Container for organizing users, groups, and computers within a domain |
| Group Policy Object (GPO) | Set of rules that control user and computer settings |
| DSRM | Directory Services Restore Mode - recovery password for AD |
| Replication | Process of syncing AD data between domain controllers |
| Site | Physical location in AD (for replication management) |

---

## Background Knowledge: Static IP Configuration

**Why Static IP?** Domain controllers need consistent IP addresses so other computers can always find them.

### Typical Network Configuration:

| Device | IP Address | Subnet Mask | Gateway | DNS |
|--------|------------|-------------|---------|-----|
| Server (DC) | 192.168.1.10 | 255.255.255.0 | 192.168.1.1 | 8.8.8.8 |
| Client PC | 192.168.1.11 | 255.255.255.0 | 192.168.1.1 | 192.168.1.10 |

**Network Range:** 192.168.1.0/24
- Network ID: 192.168.1.0
- Usable IPs: 192.168.1.1 - 192.168.1.254
- Subnet Mask: 255.255.255.0

---

## ACTIVITY 1: INSTALL AND CONFIGURE AD DS

**Do on:** Windows Server (will become domain controller)

**Goal:** Deploy a domain controller by installing AD DS and promoting the server to a new forest.

**Time:** 20 minutes

---

## TASK 1: INSTALL AD DS ROLE

### GUI METHOD (Server Manager)

1. Log into Windows Server with Administrator account
2. **Server Manager** opens automatically (or click Start → Server Manager)
3. Click **Manage** (top-right corner)
4. Click **Add Roles and Features**
5. "Add Roles and Features Wizard" opens
6. Click **Next** (Before You Begin page)
7. Select **Role-based or feature-based installation**
8. Click **Next**
9. Select your server from the server pool
10. Click **Next**
11. On "Server Roles" page, check the box for **Active Directory Domain Services**
12. A popup appears "Add features that are required for Active Directory Domain Services?"
13. Click **Add Features**
14. Click **Next**
15. Click **Next** (Features page)
16. Click **Next** (AD DS page)
17. Click **Install**
18. Wait for installation to complete (may take a few minutes)
19. **Do NOT close** the wizard yet

### TERMINAL METHOD (PowerShell)

1. Right-click **Start** → Click **Windows PowerShell (Admin)**
2. Copy and run this command:

```powershell
Install-WindowsFeature AD-Domain-Services -IncludeManagementTools
```

This installs AD DS role and all management tools.

**Wait for completion.** You'll see:
```
Success: True
Restart Needed: No
```

---

## TASK 2: PROMOTE SERVER TO DOMAIN CONTROLLER

### GUI METHOD (Server Manager)

1. After AD DS installation completes, in the Add Roles wizard, you'll see a notification flag (yellow triangle with !)
2. Click the **notification flag** (top-right, next to Manage)
3. Click **Promote this server to a domain controller** (blue link)
4. "Active Directory Domain Services Configuration Wizard" opens
5. Select **Add a new forest**
6. In **Root domain name:** Type **lab.local**
7. Click **Next**
8. On "Domain Controller Options" page:
   - Forest functional level: **Windows Server 2016** (or latest)
   - Domain functional level: **Windows Server 2016** (or latest)
   - Check: **Domain Name System (DNS) server**
   - Check: **Global Catalog (GC)**
9. Type **DSRM password:** **P@ssword123**
10. Confirm password: **P@ssword123**
11. Click **Next**
12. DNS Options page - Click **Next** (ignore any warnings)
13. Additional Options page - NetBIOS name shows **LAB** - Click **Next**
14. Paths page - Keep default paths - Click **Next**
15. Review Options page - Review settings - Click **Next**
16. Prerequisites Check page - Wait for validation (ignore warnings, must have 0 errors)
17. Click **Install**
18. **Server will automatically restart** when done

### TERMINAL METHOD (PowerShell)

1. Open **Windows PowerShell (Admin)**
2. Copy and run this command:

```powershell
Install-ADDSForest -DomainName "lab.local" -SafeModeAdministratorPassword (ConvertTo-SecureString "P@ssword123" -AsPlainText -Force) -InstallDns -Force
```

**What this does:**
- Creates new forest with domain "lab.local"
- Sets DSRM password to P@ssword123
- Installs DNS server
- `-Force` skips confirmation prompts

**Server will restart automatically.**

---

## TASK 3: VERIFY AD INSTALLATION

**Do after server restarts**

### GUI METHOD

1. Log back in (you'll now log in as **LAB\Administrator**)
2. Open **Server Manager**
3. Click **Tools** (top-right)
4. You should see new tools:
   - Active Directory Users and Computers
   - Active Directory Domains and Trusts
   - Active Directory Sites and Services
   - DNS
   - Group Policy Management
5. Click **Active Directory Users and Computers**
6. Expand **lab.local**
7. You should see default containers:
   - Builtin
   - Computers
   - Domain Controllers
   - ForeignSecurityPrincipals
   - Managed Service Accounts
   - Users

### TERMINAL METHOD

1. Open **Windows PowerShell**
2. Copy and run these verification commands:

**Check domain:**

```powershell
Get-ADDomain
```

Shows domain information (Name, NetBIOSName, DNSRoot, etc.)

**Check forest:**

```powershell
Get-ADForest
```

Shows forest information

**Check domain controller:**

```powershell
Get-ADDomainController
```

Shows your DC name, IP, and role

---

## ACTIVITY 2: CREATE OUs AND APPLY GPO

**Do on:** Domain Controller

**Goal:** Organize users into OUs and enforce a Group Policy (e.g., password complexity).

**Time:** 20 minutes

---

## TASK 4: CREATE ORGANIZATIONAL UNITS (OUs)

### GUI METHOD (Active Directory Users and Computers)

1. Open **Server Manager** → **Tools** → **Active Directory Users and Computers**
2. Or press **Win + R** → Type **dsa.msc** → Press Enter
3. In the left tree, right-click **lab.local**
4. Hover over **New** → Click **Organizational Unit**
5. A "New Object - Organizational Unit" window appears
6. **Name:** Type **ITDepartment**
7. Leave "Protect container from accidental deletion" checked
8. Click **OK**
9. **ITDepartment** appears under lab.local
10. Now create a child OU:
11. **Right-click ITDepartment** → **New** → **Organizational Unit**
12. **Name:** Type **Staff**
13. Click **OK**
14. You now have: lab.local → ITDepartment → Staff

### TERMINAL METHOD (PowerShell)

1. Open **Windows PowerShell**
2. Copy and run these commands:

**Create parent OU:**

```powershell
New-ADOrganizationalUnit -Name "ITDepartment" -Path "DC=lab,DC=local"
```

**Create child OU:**

```powershell
New-ADOrganizationalUnit -Name "Staff" -Path "OU=ITDepartment,DC=lab,DC=local"
```

**Verify:**

```powershell
Get-ADOrganizationalUnit -Filter * | Select-Object Name, DistinguishedName
```

---

## TASK 5: MOVE USERS INTO OU (Optional)

### GUI METHOD

1. In **Active Directory Users and Computers**
2. Click on **Users** container (under lab.local)
3. Find a user (e.g., Administrator)
4. Right-click the user → Click **Move...**
5. Select **ITDepartment** → **Staff**
6. Click **OK**
7. User is now in lab.local → ITDepartment → Staff

### TERMINAL METHOD

```powershell
Move-ADObject -Identity "CN=UserName,CN=Users,DC=lab,DC=local" -TargetPath "OU=Staff,OU=ITDepartment,DC=lab,DC=local"
```

(Replace UserName with actual user)

---

## TASK 6: CREATE A GROUP POLICY OBJECT (GPO)

### GUI METHOD (Group Policy Management)

1. Open **Server Manager** → **Tools** → **Group Policy Management**
2. Or press **Win + R** → Type **gpmc.msc** → Press Enter
3. Expand **Forest: lab.local**
4. Expand **Domains**
5. Expand **lab.local**
6. Right-click **lab.local** (or right-click **Group Policy Objects**)
7. Click **New**
8. **Name:** Type **PasswordPolicy**
9. Click **OK**
10. **PasswordPolicy** appears under Group Policy Objects

### TERMINAL METHOD (PowerShell)

```powershell
New-GPO -Name "PasswordPolicy" -Comment "Enforces password complexity and length"
```

**Verify:**

```powershell
Get-GPO -Name "PasswordPolicy"
```

---

## TASK 7: EDIT GPO - SET PASSWORD POLICY

### GUI METHOD

1. In **Group Policy Management**
2. Expand **Group Policy Objects**
3. Right-click **PasswordPolicy**
4. Click **Edit**
5. "Group Policy Management Editor" opens
6. Navigate to:
   - **Computer Configuration**
   - → **Policies**
   - → **Windows Settings**
   - → **Security Settings**
   - → **Account Policies**
   - → **Password Policy**
7. Double-click **Minimum password length**
8. Check **Define this policy setting**
9. Set **Password must be at least:** **10** characters
10. Click **OK**
11. Close Group Policy Management Editor

### TERMINAL METHOD (PowerShell)

```powershell
Set-ADDefaultDomainPasswordPolicy -Identity "lab.local" -MinPasswordLength 10 -ComplexityEnabled $true
```

**Verify:**

```powershell
Get-ADDefaultDomainPasswordPolicy -Identity "lab.local"
```

---

## TASK 8: LINK GPO TO OU

### GUI METHOD

1. In **Group Policy Management**
2. Find **ITDepartment** → **Staff** OU in the left tree
3. Right-click **Staff** OU
4. Click **Link an Existing GPO...**
5. Select **PasswordPolicy** from the list
6. Click **OK**
7. The GPO is now linked to Staff OU (you'll see it listed under Staff)

### TERMINAL METHOD (PowerShell)

```powershell
New-GPLink -Name "PasswordPolicy" -Target "OU=Staff,OU=ITDepartment,DC=lab,DC=local"
```

**Verify:**

```powershell
Get-GPInheritance -Target "OU=Staff,OU=ITDepartment,DC=lab,DC=local"
```

---

## TASK 9: APPLY AND TEST GPO

### GUI METHOD

1. Open **Command Prompt** or **PowerShell**
2. Run:

```cmd
gpupdate /force
```

This forces Group Policy to apply immediately.

3. Check results:

```cmd
gpresult /r
```

Shows applied policies

### TERMINAL METHOD

```powershell
Invoke-GPUpdate -Force
```

**Verify GPO applied:**

```powershell
Get-GPResultantSetOfPolicy -ReportType Html -Path C:\gpreport.html
```

Then open C:\gpreport.html in browser to see detailed report.

---

## ACTIVITY 3: CONFIGURE SITES AND VERIFY REPLICATION

**Do on:** Domain Controller

**Goal:** Create a new site, associate a subnet, and verify replication.

**Time:** 20 minutes

---

## TASK 10: CREATE A NEW AD SITE

### GUI METHOD (AD Sites and Services)

1. Open **Server Manager** → **Tools** → **Active Directory Sites and Services**
2. Or press **Win + R** → Type **dssite.msc** → Press Enter
3. In the left tree, expand **Sites**
4. Right-click **Sites**
5. Click **New Site...**
6. **Name:** Type **BranchOffice**
7. Select **DEFAULTIPSITELINK** (or available site link)
8. Click **OK**
9. A message appears about completing site configuration - Click **OK**
10. **BranchOffice** site is created

### TERMINAL METHOD (PowerShell)

```powershell
New-ADReplicationSite -Name "BranchOffice"
```

**Verify:**

```powershell
Get-ADReplicationSite -Filter *
```

---

## TASK 11: ASSOCIATE SUBNET WITH SITE

### GUI METHOD

1. In **Active Directory Sites and Services**
2. Expand **Sites**
3. Right-click **Subnets**
4. Click **New Subnet...**
5. **Prefix:** Type **192.168.20.0/24**
6. Select **BranchOffice** site
7. Click **OK**
8. Subnet is now associated with BranchOffice site

### TERMINAL METHOD (PowerShell)

```powershell
New-ADReplicationSubnet -Name "192.168.20.0/24" -Site "BranchOffice"
```

**Verify:**

```powershell
Get-ADReplicationSubnet -Filter * | Select-Object Name, Site
```

---

## TASK 12: VERIFY SITE CONFIGURATION

### GUI METHOD

1. In **Active Directory Sites and Services**
2. Expand **Sites** → Expand **BranchOffice**
3. You should see:
   - NTDS Settings
   - Servers (may be empty if no DCs in this site yet)
4. Click **Subnets**
5. Verify **192.168.20.0/24** is listed with Site = BranchOffice

### TERMINAL METHOD (PowerShell)

**Check which site your computer is in:**

```cmd
nltest /dsgetsite
```

**View site links:**

```powershell
Get-ADReplicationSiteLink -Filter *
```

**Check replication status:**

```cmd
repadmin /showrepl
```

**Replication summary:**

```cmd
repadmin /replsummary
```

---

## Additional Tasks: Create Test User and Test Domain Login

### TASK 13: CREATE A DOMAIN USER

#### GUI METHOD

1. Open **Active Directory Users and Computers** (dsa.msc)
2. Navigate to **lab.local** → **ITDepartment** → **Staff**
3. Right-click **Staff** → **New** → **User**
4. Fill in:
   - **First name:** Test
   - **Last name:** User
   - **User logon name:** testuser
5. Click **Next**
6. **Password:** P@ssword123
7. **Confirm password:** P@ssword123
8. Uncheck "User must change password at next logon"
9. Check "Password never expires" (for lab only!)
10. Click **Next**
11. Click **Finish**

#### TERMINAL METHOD

```powershell
New-ADUser -Name "Test User" -GivenName "Test" -Surname "User" -SamAccountName "testuser" -UserPrincipalName "testuser@lab.local" -Path "OU=Staff,OU=ITDepartment,DC=lab,DC=local" -AccountPassword (ConvertTo-SecureString "P@ssword123" -AsPlainText -Force) -Enabled $true -PasswordNeverExpires $true
```

**Verify:**

```powershell
Get-ADUser -Identity testuser
```

---

## TASK 14: CREATE ADDITIONAL OU (TempStaff)

**For validation exercise**

### GUI METHOD

1. In **Active Directory Users and Computers**
2. Right-click **lab.local**
3. **New** → **Organizational Unit**
4. **Name:** TempStaff
5. Click **OK**

### TERMINAL METHOD

```powershell
New-ADOrganizationalUnit -Name "TempStaff" -Path "DC=lab,DC=local"
```

---

## TASK 15: APPLY GPO TO TempStaff OU

### GUI METHOD

1. Open **Group Policy Management** (gpmc.msc)
2. Find **TempStaff** OU
3. Right-click **TempStaff**
4. Click **Link an Existing GPO...**
5. Select **PasswordPolicy**
6. Click **OK**

### TERMINAL METHOD

```powershell
New-GPLink -Name "PasswordPolicy" -Target "OU=TempStaff,DC=lab,DC=local"
```

---

## Verification Commands Summary

### Check Domain Configuration

```powershell
Get-ADDomain | Select-Object Name, DNSRoot, DomainMode
```

```powershell
Get-ADForest | Select-Object Name, ForestMode, RootDomain
```

### Check Domain Controller

```powershell
Get-ADDomainController | Select-Object Name, IPv4Address, IsGlobalCatalog
```

### Check OUs

```powershell
Get-ADOrganizationalUnit -Filter * | Select-Object Name, DistinguishedName
```

### Check Users

```powershell
Get-ADUser -Filter * | Select-Object Name, SamAccountName, Enabled
```

### Check GPOs

```powershell
Get-GPO -All | Select-Object DisplayName, GpoStatus
```

### Check Replication

```cmd
repadmin /showrepl
```

```cmd
repadmin /replsummary
```

### Check Site Membership

```cmd
nltest /dsgetsite
```

---

## QUICK REFERENCE CHEAT SHEET

| Action | PowerShell Command | GUI Location |
|--------|-------------------|--------------|
| Install AD DS | `Install-WindowsFeature AD-Domain-Services -IncludeManagementTools` | Server Manager → Add Roles → AD DS |
| Promote to DC | `Install-ADDSForest -DomainName "lab.local"` | Server Manager → Notifications → Promote |
| Create OU | `New-ADOrganizationalUnit -Name "OUName" -Path "DC=lab,DC=local"` | dsa.msc → Right-click domain → New → OU |
| Create user | `New-ADUser -Name "User" -SamAccountName "user"` | dsa.msc → Right-click OU → New → User |
| Create GPO | `New-GPO -Name "PolicyName"` | gpmc.msc → Right-click domain → New |
| Link GPO to OU | `New-GPLink -Name "GPOName" -Target "OU=OUName,DC=lab,DC=local"` | gpmc.msc → Right-click OU → Link Existing GPO |
| Apply GPO | `gpupdate /force` | Command Prompt |
| Check GPO results | `gpresult /r` | Command Prompt |
| Create site | `New-ADReplicationSite -Name "SiteName"` | dssite.msc → Right-click Sites → New Site |
| Create subnet | `New-ADReplicationSubnet -Name "IP/Mask" -Site "SiteName"` | dssite.msc → Right-click Subnets → New Subnet |
| Check site | `nltest /dsgetsite` | Command Prompt |
| Check replication | `repadmin /showrepl` | Command Prompt |
| Get domain info | `Get-ADDomain` | PowerShell |
| Get forest info | `Get-ADForest` | PowerShell |
| Get DC info | `Get-ADDomainController` | PowerShell |
| List all OUs | `Get-ADOrganizationalUnit -Filter *` | PowerShell |
| List all users | `Get-ADUser -Filter *` | PowerShell |
| List all GPOs | `Get-GPO -All` | PowerShell |

---

## Screenshots to Capture for PLR

Take these screenshots for your Personal Learning Record:

**Activity 1:**
1. Server Manager showing AD DS role installed
2. Domain Controller promotion wizard completion
3. Server restart prompt
4. Login screen showing LAB\Administrator
5. `Get-ADDomain` PowerShell output
6. Active Directory Users and Computers showing lab.local domain

**Activity 2:**
7. OU structure (ITDepartment and Staff)
8. Group Policy Management showing PasswordPolicy GPO
9. GPO Edit window showing password length = 10
10. GPO linked to Staff OU
11. `gpresult /r` output showing applied policies

**Activity 3:**
12. AD Sites and Services showing BranchOffice site
13. Subnet 192.168.20.0/24 configuration
14. `repadmin /showrepl` output
15. `nltest /dsgetsite` output

---

## Assessment Notes

### For Your PLR, Document:

**Introduction:**
- What Active Directory is and why it's needed
- Difference between workgroup (Week 2) and domain
- Business benefits of centralized management

**Discussion:**
- Installation steps and choices made
- OU structure design rationale
- GPO configuration and testing
- Replication setup and verification

**Challenges:**
- Any errors encountered
- How you troubleshooted
- Why DSRM password is critical

**Summary:**
- What you achieved
- How AD improves on workgroup model
- Next steps (adding more DCs, configuring replication)

---

## Tips for Success

**Before Starting:**
- Take a snapshot of your VM (if using virtual machine)
- Ensure server has static IP
- Have at least 2GB RAM available
- Server should not be on a production network

**During Lab:**
- Take screenshots as you go
- Note any warnings or errors
- Write down the DSRM password!
- Don't skip verification steps

**After Lab:**
- Document everything in PLR immediately
- Keep VM snapshot for future reference
- Turn off VM when not in use

---

## Common Issues and Solutions

**Issue:** Prerequisites check fails  
**Solution:** Ensure static IP configured, server name is valid

**Issue:** DNS delegation warning  
**Solution:** Safe to ignore if this is the first DC in forest

**Issue:** Can't install AD DS  
**Solution:** Check Windows Server edition (must be Standard or Datacenter, not Essentials)

**Issue:** "Domain controller not found" after installation  
**Solution:** Wait 5-10 minutes after restart for services to fully start

**Issue:** GPO not applying  
**Solution:** Run `gpupdate /force` and wait, check GPO is linked to correct OU

---

**Practice both GUI and PowerShell methods to become proficient!**

