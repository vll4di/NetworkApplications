# Week 4 Lab Guide: DFS Namespace & Replication

Complete guide showing both GUI (DFS Management) and Terminal (PowerShell) methods.

---

## Lab Overview

### What is Week 4 About:
**Creating and Configuring Distributed File System (DFS) Namespace and Replication**

### PC Requirements:
- **Two Windows Server 2022 VMs:** LondonFS and ManchesterFS
- Both servers joined to **lab.local** domain (from Week 3)
- Administrator privileges on both servers
- Active Directory Domain Services (AD DS) already installed
- Network connectivity between both servers

### What You'll Do:
- **Activity 1:** Create a domain-based DFS namespace (\\lab.local\CompanyData) and add shared folder targets
- **Activity 2:** Configure DFS Replication between servers to synchronize the Projects folder
- **Activity 3:** Verify, monitor, and troubleshoot DFS replication

---

## Important Terms

| Term | Definition |
|------|------------|
| DFS Namespace | A virtual view of shared folders on different servers, providing a single logical path |
| DFS Replication | Service that synchronizes folders between multiple servers automatically |
| Domain-based Namespace | DFS namespace stored in Active Directory (e.g., \\domain\Namespace) |
| Standalone Namespace | DFS namespace stored on a single server (e.g., \\Server\Namespace) |
| Folder Target | Physical location (UNC path) that a DFS folder points to |
| Replication Group | Collection of servers that replicate one or more folders |
| Topology | Network structure for replication (Full Mesh, Hub and Spoke, etc.) |
| Referral | List of folder targets returned to clients when accessing DFS namespace |
| Site-Aware | DFS automatically directs clients to the nearest server based on AD sites |
| RDC | Remote Differential Compression - reduces bandwidth by only replicating changes |

---

## Background Knowledge: Prerequisites

**Before Starting:**
- Ensure both servers (LondonFS and ManchesterFS) are domain-joined to lab.local
- Both servers should have static IP addresses
- Create shared folders: **D:\Shares\Projects** on both servers
- Verify network connectivity between servers (ping test)
- Ensure AD replication is working (from Week 3)

### Typical Server Configuration:

| Server | Role | Domain | Shared Folder |
|--------|------|--------|---------------|
| LondonFS | File Server | lab.local | D:\Shares\Projects |
| ManchesterFS | File Server | lab.local | D:\Shares\Projects |

**Network Requirements:**
- Both servers must be able to resolve each other's hostnames
- DNS must be working correctly
- Firewall rules should allow DFS traffic (ports 135, 445, 5722, 49152-65535)

---

## ACTIVITY 1: CREATE A DOMAIN-BASED DFS NAMESPACE

**Do on:** LondonFS (primary namespace server)

**Goal:** Create a domain-based DFS namespace (\\lab.local\CompanyData) and add a shared folder (Projects) with targets on both servers.

**Time:** 30 minutes

---

## TASK 1: INSTALL DFS NAMESPACES ROLE

### GUI METHOD (Server Manager)

1. Log into **LondonFS** with Administrator account (LAB\Administrator)
2. **Server Manager** opens automatically (or click Start → Server Manager)
3. Click **Manage** (top-right corner)
4. Click **Add Roles and Features**
5. "Add Roles and Features Wizard" opens
6. Click **Next** (Before You Begin page)
7. Select **Role-based or feature-based installation**
8. Click **Next**
9. Select **LondonFS** from the server pool
10. Click **Next**
11. On "Server Roles" page, expand **File and Storage Services**
12. Expand **File and iSCSI Services**
13. Check the box for **DFS Namespaces**
14. A popup appears "Add features that are required for DFS Namespaces?"
15. Click **Add Features**
16. Click **Next**
17. Click **Next** (Features page)
18. Click **Install**
19. Wait for installation to complete (may take a few minutes)
20. Click **Close** when done

### TERMINAL METHOD (PowerShell)

1. Right-click **Start** → Click **Windows PowerShell (Admin)**
2. Copy and run this command:

```powershell
Install-WindowsFeature FS-DFS-Namespace -IncludeManagementTools
```

This installs DFS Namespaces role and management tools.

**Wait for completion.** You'll see:
```
Success: True
Restart Needed: No
```

---

## TASK 2: CREATE SHARED FOLDERS ON BOTH SERVERS

**Do on:** Both LondonFS and ManchesterFS

### GUI METHOD

**On LondonFS:**
1. Open **File Explorer**
2. Navigate to **D:\** (create D: drive if it doesn't exist)
3. Right-click in empty space → **New** → **Folder**
4. Name it **Shares**
5. Open **Shares** folder
6. Right-click in empty space → **New** → **Folder**
7. Name it **Projects**
8. Right-click **Projects** folder → **Properties**
9. Click **Sharing** tab
10. Click **Share...**
11. In the "Network File and Folder Sharing" window:
    - Add **Everyone** (or **Domain Users**)
    - Set permission to **Read/Write**
    - Click **Share**
12. Click **Done**
13. Note the share path: **\\LondonFS\Shares\Projects**

**On ManchesterFS:**
1. Repeat steps 1-12 above
2. Note the share path: **\\ManchesterFS\Shares\Projects**

### TERMINAL METHOD (PowerShell)

**On LondonFS:**

```powershell
# Create folder structure
New-Item -Path "D:\Shares\Projects" -ItemType Directory -Force

# Share the folder
New-SmbShare -Name "Shares" -Path "D:\Shares" -FullAccess "LAB\Domain Users"
```

**On ManchesterFS:**

```powershell
# Create folder structure
New-Item -Path "D:\Shares\Projects" -ItemType Directory -Force

# Share the folder
New-SmbShare -Name "Shares" -Path "D:\Shares" -FullAccess "LAB\Domain Users"
```

**Verify shares:**

```powershell
Get-SmbShare
```

---

## TASK 3: CREATE DOMAIN-BASED DFS NAMESPACE

### GUI METHOD (DFS Management)

1. On **LondonFS**, open **Server Manager**
2. Click **Tools** (top-right) → **DFS Management**
3. Or press **Win + R** → Type **dfsmgmt.msc** → Press Enter
4. In the left pane, right-click **Namespaces**
5. Click **New Namespace...**
6. "New Namespace Wizard" opens
7. **Namespace Server:** Type or browse to **LondonFS**
8. Click **Next**
9. **Namespace Name:** Type **CompanyData**
10. Click **Next**
11. **Namespace Type:** Select **Domain-based namespace**
12. **Enable Windows Server 2008 mode:** Leave checked (for better features)
13. Click **Next**
14. Review settings - namespace path will be **\\lab.local\CompanyData**
15. Click **Create**
16. Click **Close**
17. You should now see **\\lab.local\CompanyData** in DFS Management

### TERMINAL METHOD (PowerShell)

1. Open **Windows PowerShell (Admin)** on LondonFS
2. Copy and run this command:

```powershell
New-DfsnRoot -TargetPath "\\LondonFS\CompanyData" -Type DomainV2 -Path "\\lab.local\CompanyData"
```

**What this does:**
- Creates a domain-based namespace (DomainV2 = Windows Server 2008 mode)
- Namespace path: \\lab.local\CompanyData
- Target server: LondonFS

**Verify:**

```powershell
Get-DfsnRoot
```

---

## TASK 4: ADD SECOND NAMESPACE SERVER (ManchesterFS)

### GUI METHOD

1. In **DFS Management**, expand **Namespaces**
2. Expand **\\lab.local\CompanyData**
3. Click on **\\lab.local\CompanyData** (the namespace itself)
4. In the right pane, click **Namespace Servers** tab
5. Right-click in the empty area → Click **Add Namespace Server...**
6. **Namespace Server:** Type or browse to **ManchesterFS**
7. Click **OK**
8. Wait for synchronization (may take a minute)
9. You should now see both **LondonFS** and **ManchesterFS** listed as namespace servers

### TERMINAL METHOD (PowerShell)

```powershell
New-DfsnRootTarget -Path "\\lab.local\CompanyData" -TargetPath "\\ManchesterFS\CompanyData"
```

**Verify:**

```powershell
Get-DfsnRootTarget -Path "\\lab.local\CompanyData"
```

---

## TASK 5: CREATE DFS FOLDER (Projects)

### GUI METHOD

1. In **DFS Management**, expand **Namespaces**
2. Right-click **\\lab.local\CompanyData**
3. Click **New Folder...**
4. **Name:** Type **Projects**
5. Click **Add...** to add folder targets
6. **Folder Target Path:** Type **\\LondonFS\Shares\Projects**
7. Click **OK**
8. Click **Add...** again
9. **Folder Target Path:** Type **\\ManchesterFS\Shares\Projects**
10. Click **OK**
11. You should see both targets listed
12. Click **OK**
13. **Projects** folder now appears under CompanyData

### TERMINAL METHOD (PowerShell)

**Create folder with first target:**

```powershell
New-DfsnFolder -Path "\\lab.local\CompanyData\Projects" -TargetPath "\\LondonFS\Shares\Projects"
```

**Add second target:**

```powershell
New-DfsnFolderTarget -Path "\\lab.local\CompanyData\Projects" -TargetPath "\\ManchesterFS\Shares\Projects"
```

**Verify:**

```powershell
Get-DfsnFolder -Path "\\lab.local\CompanyData\Projects"
Get-DfsnFolderTarget -Path "\\lab.local\CompanyData\Projects"
```

---

## TASK 6: VERIFY DFS NAMESPACE

### GUI METHOD

1. In **DFS Management**, expand **\\lab.local\CompanyData**
2. Click on **Projects** folder
3. In the right pane, **Folder Targets** tab should show:
   - \\LondonFS\Shares\Projects (Online)
   - \\ManchesterFS\Shares\Projects (Online)
4. Test access: Open **File Explorer**
5. Navigate to **\\lab.local\CompanyData\Projects**
6. You should be able to access the folder
7. Create a test file to verify write access

### TERMINAL METHOD (PowerShell)

**Check namespace:**

```powershell
Get-DfsnRoot -Path "\\lab.local\CompanyData"
```

**Check folder targets:**

```powershell
Get-DfsnFolderTarget -Path "\\lab.local\CompanyData\Projects"
```

**Test access:**

```powershell
Test-Path "\\lab.local\CompanyData\Projects"
```

**List all folders in namespace:**

```powershell
Get-DfsnFolder -Path "\\lab.local\CompanyData" -Recurse
```

---

## TROUBLESHOOTING: DFS Namespace Issues

**Issue:** Namespace creation fails  
**Solution:** 
- Verify DNS resolution: `nslookup lab.local`
- Check AD replication: `repadmin /showrepl`
- Ensure both servers are domain-joined
- Verify firewall allows DFS traffic

**Issue:** Folder targets show as "Offline"  
**Solution:**
- Verify shares exist: `Get-SmbShare`
- Check share permissions
- Test UNC access: `\\LondonFS\Shares\Projects`
- Verify network connectivity: `ping LondonFS`

**Issue:** Can't access namespace from client  
**Solution:**
- Verify client is domain-joined
- Check DNS settings point to domain controller
- Test: `nslookup lab.local`
- Verify DFS client service is running

---

## ACTIVITY 2: CONFIGURE DFS REPLICATION FOR PROJECTS FOLDER

**Do on:** LondonFS (or either server)

**Goal:** Enable DFS Replication between LondonFS and ManchesterFS to synchronize the Projects folder.

**Time:** 25 minutes

---

## TASK 7: INSTALL DFS REPLICATION FEATURE

### GUI METHOD (Server Manager)

**On both LondonFS and ManchesterFS:**

1. Open **Server Manager**
2. Click **Manage** → **Add Roles and Features**
3. Click **Next** (Before You Begin)
4. Select **Role-based or feature-based installation**
5. Click **Next**
6. Select your server
7. Click **Next**
8. On "Features" page (NOT Server Roles), expand **File and Storage Services**
9. Check **DFS Replication**
10. Click **Next**
11. Click **Install**
12. Wait for installation
13. Click **Close**
14. **Repeat on ManchesterFS**

### TERMINAL METHOD (PowerShell)

**On LondonFS:**

```powershell
Install-WindowsFeature FS-DFS-Replication -IncludeManagementTools
```

**On ManchesterFS:**

```powershell
Install-WindowsFeature FS-DFS-Replication -IncludeManagementTools
```

**Verify installation:**

```powershell
Get-WindowsFeature | Where-Object {$_.Name -like "*DFS*"}
```

---

## TASK 8: CREATE REPLICATION GROUP

### GUI METHOD (DFS Management)

1. On **LondonFS**, open **DFS Management**
2. In the left pane, click **Replication**
3. Right-click **Replication** → Click **New Replication Group...**
4. "New Replication Group Wizard" opens
5. **Replication Group Type:** Select **Multipurpose replication group**
6. Click **Next**
7. **Name:** Type **ProjectsReplication**
8. **Description:** (Optional) Type "Replication for Projects folder"
9. Click **Next**
10. **Replication Group Members:** Click **Add...**
11. Type **LondonFS** → Click **OK**
12. Click **Add...** again
13. Type **ManchesterFS** → Click **OK**
14. Both servers should be listed
15. Click **Next**
16. **Topology Selection:** Select **Full mesh**
17. Click **Next**
18. **Replication Group Schedule and Bandwidth:**
    - Select **Replicate during the specified days and times**
    - Click **Edit Schedule...**
    - Set schedule: **Monday-Friday, 20:00-06:00** (8 PM to 6 AM)
    - Click **OK**
19. Click **Next**
20. **Primary Member:** Select **LondonFS** (this will be the authoritative source)
21. Click **Next**
22. **Folders to Replicate:** Click **Add...**
23. **Folder name:** Type **Projects**
24. **Folder path on LondonFS:** Type **D:\Shares\Projects**
25. Click **OK**
26. Click **Next**
27. **Other Members:** ManchesterFS should be listed
28. Click **Edit...** next to ManchesterFS
29. **Folder path on ManchesterFS:** Type **D:\Shares\Projects**
30. Click **OK**
31. Click **Next**
32. Review settings
33. Click **Create**
34. Click **Close**
35. Wait 5-10 minutes for AD synchronization and initial replication

### TERMINAL METHOD (PowerShell)

**Create replication group:**

```powershell
New-DfsReplicationGroup -GroupName "ProjectsReplication" -Description "Replication for Projects folder"
```

**Add members:**

```powershell
Add-DfsrMember -GroupName "ProjectsReplication" -ComputerName "LondonFS"
Add-DfsrMember -GroupName "ProjectsReplication" -ComputerName "ManchesterFS"
```

**Create replicated folder:**

```powershell
New-DfsReplicatedFolder -GroupName "ProjectsReplication" -FolderName "Projects"
```

**Add folder to members:**

```powershell
Set-DfsrMembership -GroupName "ProjectsReplication" -FolderName "Projects" -ContentPath "D:\Shares\Projects" -ComputerName "LondonFS" -PrimaryMember $true
Set-DfsrMembership -GroupName "ProjectsReplication" -FolderName "Projects" -ContentPath "D:\Shares\Projects" -ComputerName "ManchesterFS" -PrimaryMember $false
```

**Set topology (Full Mesh):**

```powershell
Set-DfsrMembership -GroupName "ProjectsReplication" -FolderName "Projects" -ComputerName "LondonFS" -ContentPath "D:\Shares\Projects"
Set-DfsrMembership -GroupName "ProjectsReplication" -FolderName "Projects" -ComputerName "ManchesterFS" -ContentPath "D:\Shares\Projects"
Add-DfsrConnection -GroupName "ProjectsReplication" -SourceComputerName "LondonFS" -DestinationComputerName "ManchesterFS"
Add-DfsrConnection -GroupName "ProjectsReplication" -SourceComputerName "ManchesterFS" -DestinationComputerName "LondonFS"
```

**Note:** PowerShell method is complex. GUI method is recommended for initial setup.

---

## TASK 9: CONFIGURE REPLICATION SCHEDULE

### GUI METHOD

1. In **DFS Management**, expand **Replication**
2. Click on **ProjectsReplication**
3. In the right pane, click **Memberships** tab
4. Right-click a membership → Click **Properties**
5. Click **Schedule** tab
6. Click **Edit Schedule...**
7. Configure:
   - **Monday-Friday:** 20:00-06:00 (replication enabled)
   - **Saturday-Sunday:** 00:00-24:00 (replication enabled, or disabled if preferred)
8. Click **OK**
9. Click **OK**

### TERMINAL METHOD (PowerShell)

**Set replication schedule:**

```powershell
Set-DfsrMembership -GroupName "ProjectsReplication" -FolderName "Projects" -ComputerName "LondonFS" -ScheduleType "Scheduled"
```

**Note:** Detailed schedule configuration via PowerShell requires additional scripting.

---

## TASK 10: VERIFY REPLICATION CONFIGURATION

### GUI METHOD

1. In **DFS Management**, expand **Replication**
2. Click **ProjectsReplication**
3. Check **Memberships** tab - should show:
   - LondonFS: D:\Shares\Projects (Primary)
   - ManchesterFS: D:\Shares\Projects
4. Check **Connections** tab - should show:
   - LondonFS → ManchesterFS (Enabled)
   - ManchesterFS → LondonFS (Enabled)
5. Check **Replicated Folders** tab - should show:
   - Projects folder

### TERMINAL METHOD (PowerShell)

**Check replication group:**

```powershell
Get-DfsReplicationGroup -GroupName "ProjectsReplication"
```

**Check members:**

```powershell
Get-DfsrMember -GroupName "ProjectsReplication"
```

**Check replicated folders:**

```powershell
Get-DfsReplicatedFolder -GroupName "ProjectsReplication"
```

**Check memberships:**

```powershell
Get-DfsrMembership -GroupName "ProjectsReplication"
```

**Check connections:**

```powershell
Get-DfsrConnection -GroupName "ProjectsReplication"
```

---

## TASK 11: FORCE AD POLLING AND INITIAL REPLICATION

### GUI METHOD

1. Wait 5-10 minutes after creating replication group
2. Replication should start automatically
3. Check **Event Viewer** → **Applications and Services Logs** → **DFS Replication**
4. Look for Event ID **4102** (replication started) or **4104** (replication completed)

### TERMINAL METHOD (PowerShell)

**Force AD polling:**

```powershell
dfsrdiag PollAD
```

**Check replication status:**

```powershell
Get-DfsrState -GroupName "ProjectsReplication" -FolderName "Projects"
```

**Force replication:**

```powershell
Sync-DfsReplicationGroup -GroupName "ProjectsReplication" -SourceComputerName "LondonFS" -DestinationComputerName "ManchesterFS"
```

**Check replication health:**

```powershell
Get-DfsrBacklog -GroupName "ProjectsReplication" -FolderName "Projects" -SourceComputerName "LondonFS" -DestinationComputerName "ManchesterFS"
```

**Backlog of 0 means replication is up to date.**

---

## ACTIVITY 3: VERIFY, MONITOR, AND TROUBLESHOOT DFS REPLICATION

**Do on:** Either server

**Goal:** Verify replication is working, monitor status, and troubleshoot any issues.

**Time:** 15 minutes

---

## TASK 12: TEST REPLICATION

### GUI METHOD

1. On **LondonFS**, navigate to **D:\Shares\Projects**
2. Create a test file: **TestFile.txt** with some content
3. Wait 2-3 minutes (or force replication)
4. On **ManchesterFS**, navigate to **D:\Shares\Projects**
5. Verify **TestFile.txt** appears (replication successful!)
6. On **ManchesterFS**, modify the file
7. Wait 2-3 minutes
8. On **LondonFS**, verify changes appear

### TERMINAL METHOD (PowerShell)

**On LondonFS - Create test file:**

```powershell
New-Item -Path "D:\Shares\Projects\TestFile.txt" -ItemType File -Value "Test content from LondonFS"
```

**Wait a few minutes, then check on ManchesterFS:**

```powershell
Test-Path "D:\Shares\Projects\TestFile.txt"
Get-Content "D:\Shares\Projects\TestFile.txt"
```

---

## TASK 13: MONITOR REPLICATION STATUS

### GUI METHOD

1. In **DFS Management**, expand **Replication**
2. Click **ProjectsReplication**
3. Click **Memberships** tab
4. Check status indicators:
   - Green checkmark = Healthy
   - Yellow warning = Warning
   - Red X = Error
5. Open **Event Viewer**
6. Navigate to **Applications and Services Logs** → **DFS Replication**
7. Look for recent events:
   - **4102:** Replication started
   - **4104:** Replication completed successfully
   - **4004:** Replication error

### TERMINAL METHOD (PowerShell)

**Check replication state:**

```powershell
Get-DfsrState -GroupName "ProjectsReplication" -FolderName "Projects" | Format-List
```

**Check backlog (pending files):**

```powershell
Get-DfsrBacklog -GroupName "ProjectsReplication" -FolderName "Projects" -SourceComputerName "LondonFS" -DestinationComputerName "ManchesterFS"
```

**Check replication statistics:**

```powershell
Get-DfsrStatistics -GroupName "ProjectsReplication" -FolderName "Projects"
```

**View recent replication events:**

```powershell
Get-WinEvent -LogName "DFS Replication" -MaxEvents 20 | Format-Table TimeCreated, Id, Message -AutoSize
```

---

## TASK 14: TROUBLESHOOT REPLICATION ISSUES

### Common Issues and Solutions

**Issue:** Replication not starting  
**Solution:**
```powershell
# Force AD polling
dfsrdiag PollAD

# Check service status
Get-Service DFSR

# Restart DFS Replication service
Restart-Service DFSR
```

**Issue:** High backlog (many pending files)  
**Solution:**
```powershell
# Check backlog
Get-DfsrBacklog -GroupName "ProjectsReplication" -FolderName "Projects" -SourceComputerName "LondonFS" -DestinationComputerName "ManchesterFS"

# Force replication
Sync-DfsReplicationGroup -GroupName "ProjectsReplication" -SourceComputerName "LondonFS" -DestinationComputerName "ManchesterFS"
```

**Issue:** Replication errors in Event Viewer  
**Solution:**
- Check Event Viewer for specific error codes
- Verify network connectivity: `Test-Connection ManchesterFS`
- Check firewall rules
- Verify folder permissions
- Check disk space on both servers

**Issue:** Files not syncing  
**Solution:**
```powershell
# Check if files are in use or locked
Get-Process | Where-Object {$_.Path -like "*D:\Shares\Projects*"}

# Check folder permissions
Get-Acl "D:\Shares\Projects" | Format-List

# Verify replication group health
Get-DfsrState -GroupName "ProjectsReplication" -FolderName "Projects"
```

---

## Verification Commands Summary

### DFS Namespace Commands

```powershell
# List all namespaces
Get-DfsnRoot

# Get namespace details
Get-DfsnRoot -Path "\\lab.local\CompanyData"

# List all folders in namespace
Get-DfsnFolder -Path "\\lab.local\CompanyData" -Recurse

# Get folder targets
Get-DfsnFolderTarget -Path "\\lab.local\CompanyData\Projects"

# Test namespace access
Test-Path "\\lab.local\CompanyData\Projects"
```

### DFS Replication Commands

```powershell
# List all replication groups
Get-DfsReplicationGroup

# Get replication group details
Get-DfsReplicationGroup -GroupName "ProjectsReplication"

# List members
Get-DfsrMember -GroupName "ProjectsReplication"

# List replicated folders
Get-DfsReplicatedFolder -GroupName "ProjectsReplication"

# Check memberships
Get-DfsrMembership -GroupName "ProjectsReplication"

# Check connections
Get-DfsrConnection -GroupName "ProjectsReplication"

# Check replication state
Get-DfsrState -GroupName "ProjectsReplication" -FolderName "Projects"

# Check backlog
Get-DfsrBacklog -GroupName "ProjectsReplication" -FolderName "Projects" -SourceComputerName "LondonFS" -DestinationComputerName "ManchesterFS"

# Force AD polling
dfsrdiag PollAD

# Force replication
Sync-DfsReplicationGroup -GroupName "ProjectsReplication" -SourceComputerName "LondonFS" -DestinationComputerName "ManchesterFS"
```

### Diagnostic Commands

```powershell
# Check DFS services
Get-Service DFSR, DFS

# Check DFS replication statistics
Get-DfsrStatistics -GroupName "ProjectsReplication" -FolderName "Projects"

# View replication events
Get-WinEvent -LogName "DFS Replication" -MaxEvents 20

# Test network connectivity
Test-Connection LondonFS
Test-Connection ManchesterFS

# Check shares
Get-SmbShare

# Check DNS resolution
Resolve-DnsName lab.local
Resolve-DnsName LondonFS
Resolve-DnsName ManchesterFS
```

---

## QUICK REFERENCE CHEAT SHEET

| Action | PowerShell Command | GUI Location |
|--------|-------------------|--------------|
| Install DFS Namespaces | `Install-WindowsFeature FS-DFS-Namespace -IncludeManagementTools` | Server Manager → Add Roles → DFS Namespaces |
| Create namespace | `New-DfsnRoot -TargetPath "\\Server\Name" -Type DomainV2 -Path "\\domain\Name"` | DFS Management → New Namespace |
| Add namespace server | `New-DfsnRootTarget -Path "\\domain\Name" -TargetPath "\\Server\Name"` | DFS Management → Namespace → Add Server |
| Create DFS folder | `New-DfsnFolder -Path "\\domain\Name\Folder" -TargetPath "\\Server\Share"` | DFS Management → New Folder |
| Add folder target | `New-DfsnFolderTarget -Path "\\domain\Name\Folder" -TargetPath "\\Server\Share"` | DFS Management → Folder → Add Target |
| Install DFS Replication | `Install-WindowsFeature FS-DFS-Replication -IncludeManagementTools` | Server Manager → Add Features → DFS Replication |
| Create replication group | `New-DfsReplicationGroup -GroupName "Name"` | DFS Management → Replication → New Group |
| Add replication member | `Add-DfsrMember -GroupName "Name" -ComputerName "Server"` | Replication Group → Add Member |
| Create replicated folder | `New-DfsReplicatedFolder -GroupName "Name" -FolderName "Folder"` | Replication Group → Add Folder |
| Set membership | `Set-DfsrMembership -GroupName "Name" -FolderName "Folder" -ContentPath "Path" -ComputerName "Server"` | Replication Group → Membership → Properties |
| Force replication | `Sync-DfsReplicationGroup -GroupName "Name" -SourceComputerName "Server1" -DestinationComputerName "Server2"` | Replication Group → Membership → Replicate Now |
| Check backlog | `Get-DfsrBacklog -GroupName "Name" -FolderName "Folder" -SourceComputerName "Server1" -DestinationComputerName "Server2"` | PowerShell |
| Force AD polling | `dfsrdiag PollAD` | Command Prompt |
| List namespaces | `Get-DfsnRoot` | DFS Management |
| List replication groups | `Get-DfsReplicationGroup` | DFS Management |
| Check replication state | `Get-DfsrState -GroupName "Name" -FolderName "Folder"` | PowerShell |

---

## Screenshots to Capture for PLR

Take these screenshots for your Personal Learning Record:

**Activity 1:**
1. Server Manager showing DFS Namespaces role installed
2. DFS Management showing \\lab.local\CompanyData namespace
3. Namespace Servers tab showing LondonFS and ManchesterFS
4. Projects folder with both targets (LondonFS and ManchesterFS)
5. File Explorer accessing \\lab.local\CompanyData\Projects
6. `Get-DfsnRoot` PowerShell output
7. `Get-DfsnFolderTarget` PowerShell output

**Activity 2:**
8. Server Manager showing DFS Replication feature installed
9. New Replication Group Wizard - Group name and members
10. Replication Group - Topology (Full Mesh)
11. Replication Group - Schedule configuration
12. Replication Group - Memberships showing both servers
13. Replication Group - Connections showing bidirectional links
14. `Get-DfsReplicationGroup` PowerShell output
15. `Get-DfsrMember` PowerShell output
16. Event Viewer showing replication events (4102, 4104)

**Activity 3:**
17. Test file created on LondonFS
18. Same file replicated to ManchesterFS
19. `Get-DfsrBacklog` showing 0 backlog
20. `Get-DfsrState` showing healthy replication
21. Event Viewer replication log showing successful replication

---

## Assessment Notes

### For Your PLR, Document:

**Introduction:**
- What DFS Namespace is and why it was implemented
- How it relates to the needs of the case study (centralized access, site-aware referrals)
- What DFS Replication is and why it's needed (data consistency, high availability)
- Brief overview of tasks performed (namespace creation, replication configuration)

**Discussion:**
- Step-by-step explanation of namespace creation
- Why domain-based namespace was chosen (vs standalone)
- How folder targets enable redundancy and load distribution
- Replication group configuration and topology choice (Full Mesh)
- Why replication schedule was set (bandwidth optimization)
- Commands/tools used (New-DfsnRoot, New-DfsReplicationGroup, dfsrdiag PollAD, Event Viewer)
- How replication behavior supports business needs (data consistency, disaster recovery)

**Challenges:**
- Any errors encountered (DNS resolution, AD replication, firewall)
- How you troubleshooted (dfsrdiag PollAD, Event Viewer, backlog checks)
- Why AD polling is important
- Network and firewall considerations

**Summary:**
- What you achieved (centralized file access, automated replication)
- How DFS improves on simple file shares
- Benefits for the organization (scalability, fault tolerance, site awareness)
- Next steps (monitoring, additional folders, backup strategies)

---

## Tips for Success

**Before Starting:**
- Take a snapshot of both VMs
- Ensure both servers are domain-joined to lab.local
- Verify network connectivity between servers
- Create shared folders on both servers before starting
- Ensure AD replication is working (from Week 3)

**During Lab:**
- Take screenshots as you go
- Note any warnings or errors
- Document the namespace path and folder targets
- Test access from both servers
- Wait for AD synchronization (5-10 minutes after changes)

**After Lab:**
- Document everything in PLR immediately
- Keep VM snapshots for future reference
- Test replication with actual files
- Monitor Event Viewer for replication events
- Verify namespace access from a client computer

---

## Common Issues and Solutions

**Issue:** Namespace creation fails  
**Solution:** Ensure DNS resolution works, check AD replication, verify both servers are domain-joined

**Issue:** Folder targets show as "Offline"  
**Solution:** Verify shares exist and are accessible, check share permissions, test UNC paths

**Issue:** Replication not starting  
**Solution:** Run `dfsrdiag PollAD`, check DFS Replication service is running, verify AD replication

**Issue:** High backlog (many pending files)  
**Solution:** Check network connectivity, verify folder permissions, ensure sufficient disk space, force replication

**Issue:** Files not syncing  
**Solution:** Check if files are in use, verify replication schedule, check Event Viewer for errors, verify connections

**Issue:** Can't access namespace from client  
**Solution:** Verify client is domain-joined, check DNS settings, ensure DFS client service is running

---

**Practice both GUI and PowerShell methods to become proficient!**

