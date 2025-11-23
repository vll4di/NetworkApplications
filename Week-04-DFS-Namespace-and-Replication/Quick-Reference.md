# Week 4 Quick Reference - DFS Namespace & Replication Commands

Quick command reference for DFS Namespace and Replication configuration.

---

## Installation Commands

### Install DFS Namespaces

```powershell
Install-WindowsFeature FS-DFS-Namespace -IncludeManagementTools
```

### Install DFS Replication

```powershell
Install-WindowsFeature FS-DFS-Replication -IncludeManagementTools
```

### Install Both DFS Features

```powershell
Install-WindowsFeature FS-DFS-Namespace, FS-DFS-Replication -IncludeManagementTools
```

### Verify Installation

```powershell
Get-WindowsFeature | Where-Object {$_.Name -like "*DFS*"}
```

---

## Share Management Commands

### Create Shared Folder

```powershell
New-Item -Path "D:\Shares\Projects" -ItemType Directory -Force
New-SmbShare -Name "Shares" -Path "D:\Shares" -FullAccess "LAB\Domain Users"
```

### List All Shares

```powershell
Get-SmbShare
```

### Remove Share

```powershell
Remove-SmbShare -Name "Shares" -Force
```

### Check Share Permissions

```powershell
Get-SmbShareAccess -Name "Shares"
```

---

## DFS Namespace Commands

### Create Domain-Based Namespace

```powershell
New-DfsnRoot -TargetPath "\\LondonFS\CompanyData" -Type DomainV2 -Path "\\lab.local\CompanyData"
```

**Parameters:**
- `-TargetPath`: Physical server and share path
- `-Type DomainV2`: Domain-based namespace (Windows Server 2008 mode)
- `-Path`: Logical namespace path

### List All Namespaces

```powershell
Get-DfsnRoot
```

### Get Namespace Details

```powershell
Get-DfsnRoot -Path "\\lab.local\CompanyData"
```

### Add Namespace Server

```powershell
New-DfsnRootTarget -Path "\\lab.local\CompanyData" -TargetPath "\\ManchesterFS\CompanyData"
```

### List Namespace Servers

```powershell
Get-DfsnRootTarget -Path "\\lab.local\CompanyData"
```

### Remove Namespace Server

```powershell
Remove-DfsnRootTarget -Path "\\lab.local\CompanyData" -TargetPath "\\Server\Path"
```

### Create DFS Folder

```powershell
New-DfsnFolder -Path "\\lab.local\CompanyData\Projects" -TargetPath "\\LondonFS\Shares\Projects"
```

### Add Folder Target

```powershell
New-DfsnFolderTarget -Path "\\lab.local\CompanyData\Projects" -TargetPath "\\ManchesterFS\Shares\Projects"
```

### List All Folders in Namespace

```powershell
Get-DfsnFolder -Path "\\lab.local\CompanyData" -Recurse
```

### Get Folder Targets

```powershell
Get-DfsnFolderTarget -Path "\\lab.local\CompanyData\Projects"
```

### Remove Folder Target

```powershell
Remove-DfsnFolderTarget -Path "\\lab.local\CompanyData\Projects" -TargetPath "\\Server\Share"
```

### Remove DFS Folder

```powershell
Remove-DfsnFolder -Path "\\lab.local\CompanyData\Projects"
```

### Test Namespace Access

```powershell
Test-Path "\\lab.local\CompanyData\Projects"
```

---

## DFS Replication Commands

### Create Replication Group

```powershell
New-DfsReplicationGroup -GroupName "ProjectsReplication" -Description "Replication for Projects folder"
```

### List All Replication Groups

```powershell
Get-DfsReplicationGroup
```

### Get Replication Group Details

```powershell
Get-DfsReplicationGroup -GroupName "ProjectsReplication"
```

### Add Replication Group Member

```powershell
Add-DfsrMember -GroupName "ProjectsReplication" -ComputerName "LondonFS"
Add-DfsrMember -GroupName "ProjectsReplication" -ComputerName "ManchesterFS"
```

### List Replication Group Members

```powershell
Get-DfsrMember -GroupName "ProjectsReplication"
```

### Remove Replication Group Member

```powershell
Remove-DfsrMember -GroupName "ProjectsReplication" -ComputerName "ServerName"
```

### Create Replicated Folder

```powershell
New-DfsReplicatedFolder -GroupName "ProjectsReplication" -FolderName "Projects"
```

### List Replicated Folders

```powershell
Get-DfsReplicatedFolder -GroupName "ProjectsReplication"
```

### Set Replication Membership

```powershell
Set-DfsrMembership -GroupName "ProjectsReplication" -FolderName "Projects" -ContentPath "D:\Shares\Projects" -ComputerName "LondonFS" -PrimaryMember $true
Set-DfsrMembership -GroupName "ProjectsReplication" -FolderName "Projects" -ContentPath "D:\Shares\Projects" -ComputerName "ManchesterFS" -PrimaryMember $false
```

### Get Replication Memberships

```powershell
Get-DfsrMembership -GroupName "ProjectsReplication"
```

### Add Replication Connection (Full Mesh)

```powershell
Add-DfsrConnection -GroupName "ProjectsReplication" -SourceComputerName "LondonFS" -DestinationComputerName "ManchesterFS"
Add-DfsrConnection -GroupName "ProjectsReplication" -SourceComputerName "ManchesterFS" -DestinationComputerName "LondonFS"
```

### List Replication Connections

```powershell
Get-DfsrConnection -GroupName "ProjectsReplication"
```

### Remove Replication Connection

```powershell
Remove-DfsrConnection -GroupName "ProjectsReplication" -SourceComputerName "Server1" -DestinationComputerName "Server2"
```

### Force Replication

```powershell
Sync-DfsReplicationGroup -GroupName "ProjectsReplication" -SourceComputerName "LondonFS" -DestinationComputerName "ManchesterFS"
```

### Check Replication State

```powershell
Get-DfsrState -GroupName "ProjectsReplication" -FolderName "Projects"
```

### Check Replication Backlog

```powershell
Get-DfsrBacklog -GroupName "ProjectsReplication" -FolderName "Projects" -SourceComputerName "LondonFS" -DestinationComputerName "ManchesterFS"
```

**Backlog of 0 means replication is up to date.**

### Get Replication Statistics

```powershell
Get-DfsrStatistics -GroupName "ProjectsReplication" -FolderName "Projects"
```

### Remove Replication Group

```powershell
Remove-DfsReplicationGroup -GroupName "ProjectsReplication" -Force
```

---

## Diagnostic Commands

### Force AD Polling

```cmd
dfsrdiag PollAD
```

**Important:** Run this after creating replication groups to force Active Directory synchronization.

### Check DFS Services

```powershell
Get-Service DFSR, DFS
```

All should show **Running**.

### Start DFS Services

```powershell
Start-Service DFSR, DFS
```

### Restart DFS Services

```powershell
Restart-Service DFSR, DFS
```

### Check Service Status

```powershell
Get-Service DFSR, DFS | Select-Object Name, Status, StartType
```

### View Replication Events

```powershell
Get-WinEvent -LogName "DFS Replication" -MaxEvents 20 | Format-Table TimeCreated, Id, Message -AutoSize
```

**Key Event IDs:**
- **4102:** Replication started
- **4104:** Replication completed successfully
- **4004:** Replication error
- **5014:** Connection established
- **5012:** Connection lost

### View Namespace Events

```powershell
Get-WinEvent -LogName "DFS Namespace" -MaxEvents 20 | Format-Table TimeCreated, Id, Message -AutoSize
```

### Test Network Connectivity

```powershell
Test-Connection LondonFS
Test-Connection ManchesterFS
```

### Check DNS Resolution

```powershell
Resolve-DnsName lab.local
Resolve-DnsName LondonFS
Resolve-DnsName ManchesterFS
```

### Test UNC Path Access

```powershell
Test-Path "\\LondonFS\Shares\Projects"
Test-Path "\\ManchesterFS\Shares\Projects"
Test-Path "\\lab.local\CompanyData\Projects"
```

### Check Folder Permissions

```powershell
Get-Acl "D:\Shares\Projects" | Format-List
```

### Check Disk Space

```powershell
Get-PSDrive D | Select-Object Used, Free
```

### Check Files in Use

```powershell
Get-Process | Where-Object {$_.Path -like "*D:\Shares\Projects*"}
```

---

## Advanced Commands

### Set Replication Schedule

```powershell
Set-DfsrMembership -GroupName "ProjectsReplication" -FolderName "Projects" -ComputerName "LondonFS" -ScheduleType "Scheduled"
```

### Disable Replication

```powershell
Set-DfsrMembership -GroupName "ProjectsReplication" -FolderName "Projects" -ComputerName "ServerName" -Enabled $false
```

### Enable Replication

```powershell
Set-DfsrMembership -GroupName "ProjectsReplication" -FolderName "Projects" -ComputerName "ServerName" -Enabled $true
```

### Set Staging Quota

```powershell
Set-DfsrMembership -GroupName "ProjectsReplication" -FolderName "Projects" -ComputerName "ServerName" -StagingPathQuotaInMB 4096
```

### Get Replication Health Report

```powershell
Get-DfsrHealth -GroupName "ProjectsReplication" -Path "D:\Shares\Projects"
```

### Export Replication Configuration

```powershell
Get-DfsReplicationGroup -GroupName "ProjectsReplication" | Export-Clixml -Path "C:\DFSConfig.xml"
```

### Import Replication Configuration

```powershell
$config = Import-Clixml -Path "C:\DFSConfig.xml"
```

---

## GUI Tool Shortcuts

| Tool | Command | What It Does |
|------|---------|--------------|
| DFS Management | `dfsmgmt.msc` | Manage DFS Namespaces and Replication |
| Server Manager | `servermanager` | Central management console |
| Event Viewer | `eventvwr.msc` | View DFS replication and namespace events |
| File Explorer | `explorer` | Test namespace access |
| Computer Management | `compmgmt.msc` | Manage shares and services |

---

## Common Troubleshooting

### Replication Not Starting

```powershell
# Force AD polling
dfsrdiag PollAD

# Check service status
Get-Service DFSR

# Restart service
Restart-Service DFSR

# Check replication state
Get-DfsrState -GroupName "ProjectsReplication" -FolderName "Projects"
```

### High Backlog

```powershell
# Check backlog
Get-DfsrBacklog -GroupName "ProjectsReplication" -FolderName "Projects" -SourceComputerName "LondonFS" -DestinationComputerName "ManchesterFS"

# Force replication
Sync-DfsReplicationGroup -GroupName "ProjectsReplication" -SourceComputerName "LondonFS" -DestinationComputerName "ManchesterFS"

# Check for errors
Get-WinEvent -LogName "DFS Replication" -MaxEvents 10 | Where-Object {$_.LevelDisplayName -eq "Error"}
```

### Namespace Not Accessible

```powershell
# Test namespace path
Test-Path "\\lab.local\CompanyData\Projects"

# Check namespace servers
Get-DfsnRootTarget -Path "\\lab.local\CompanyData"

# Check folder targets
Get-DfsnFolderTarget -Path "\\lab.local\CompanyData\Projects"

# Verify DNS
Resolve-DnsName lab.local

# Check DFS service
Get-Service DFS
```

### Folder Targets Offline

```powershell
# Test UNC access
Test-Path "\\LondonFS\Shares\Projects"
Test-Path "\\ManchesterFS\Shares\Projects"

# Check shares
Get-SmbShare

# Test network connectivity
Test-Connection LondonFS
Test-Connection ManchesterFS
```

### Replication Errors

```powershell
# View recent errors
Get-WinEvent -LogName "DFS Replication" -MaxEvents 20 | Where-Object {$_.LevelDisplayName -eq "Error"} | Format-List

# Check replication health
Get-DfsrHealth -GroupName "ProjectsReplication" -Path "D:\Shares\Projects"

# Check connections
Get-DfsrConnection -GroupName "ProjectsReplication"
```

---

## Best Practices

1. **Always take VM snapshot** before configuring DFS
2. **Use domain-based namespaces** for better integration with AD
3. **Verify network connectivity** between servers before starting
4. **Create shared folders** on both servers before replication setup
5. **Wait for AD synchronization** (5-10 minutes) after changes
6. **Monitor Event Viewer** for replication events
7. **Check backlog regularly** to ensure replication is working
8. **Use Full Mesh topology** for small deployments (2-3 servers)
9. **Schedule replication** during off-peak hours to reduce bandwidth
10. **Test replication** with actual files after configuration
11. **Document namespace paths** and folder targets
12. **Verify from client computers** to test site-aware referrals

---

## Quick Command Sequences

### Complete DFS Namespace Setup

```powershell
# Install feature
Install-WindowsFeature FS-DFS-Namespace -IncludeManagementTools

# Create namespace
New-DfsnRoot -TargetPath "\\LondonFS\CompanyData" -Type DomainV2 -Path "\\lab.local\CompanyData"

# Add second server
New-DfsnRootTarget -Path "\\lab.local\CompanyData" -TargetPath "\\ManchesterFS\CompanyData"

# Create folder
New-DfsnFolder -Path "\\lab.local\CompanyData\Projects" -TargetPath "\\LondonFS\Shares\Projects"

# Add second target
New-DfsnFolderTarget -Path "\\lab.local\CompanyData\Projects" -TargetPath "\\ManchesterFS\Shares\Projects"

# Verify
Get-DfsnRoot
Get-DfsnFolderTarget -Path "\\lab.local\CompanyData\Projects"
```

### Complete DFS Replication Setup

```powershell
# Install feature (on both servers)
Install-WindowsFeature FS-DFS-Replication -IncludeManagementTools

# Create replication group
New-DfsReplicationGroup -GroupName "ProjectsReplication" -Description "Replication for Projects"

# Add members
Add-DfsrMember -GroupName "ProjectsReplication" -ComputerName "LondonFS"
Add-DfsrMember -GroupName "ProjectsReplication" -ComputerName "ManchesterFS"

# Create replicated folder
New-DfsReplicatedFolder -GroupName "ProjectsReplication" -FolderName "Projects"

# Set memberships
Set-DfsrMembership -GroupName "ProjectsReplication" -FolderName "Projects" -ContentPath "D:\Shares\Projects" -ComputerName "LondonFS" -PrimaryMember $true
Set-DfsrMembership -GroupName "ProjectsReplication" -FolderName "Projects" -ContentPath "D:\Shares\Projects" -ComputerName "ManchesterFS" -PrimaryMember $false

# Add connections (Full Mesh)
Add-DfsrConnection -GroupName "ProjectsReplication" -SourceComputerName "LondonFS" -DestinationComputerName "ManchesterFS"
Add-DfsrConnection -GroupName "ProjectsReplication" -SourceComputerName "ManchesterFS" -DestinationComputerName "LondonFS"

# Force AD polling
dfsrdiag PollAD

# Verify
Get-DfsReplicationGroup -GroupName "ProjectsReplication"
Get-DfsrMember -GroupName "ProjectsReplication"
Get-DfsrBacklog -GroupName "ProjectsReplication" -FolderName "Projects" -SourceComputerName "LondonFS" -DestinationComputerName "ManchesterFS"
```

### Verification Sequence

```powershell
# Check namespace
Get-DfsnRoot
Get-DfsnFolderTarget -Path "\\lab.local\CompanyData\Projects"
Test-Path "\\lab.local\CompanyData\Projects"

# Check replication
Get-DfsReplicationGroup
Get-DfsrMember -GroupName "ProjectsReplication"
Get-DfsrState -GroupName "ProjectsReplication" -FolderName "Projects"
Get-DfsrBacklog -GroupName "ProjectsReplication" -FolderName "Projects" -SourceComputerName "LondonFS" -DestinationComputerName "ManchesterFS"

# Check services
Get-Service DFSR, DFS

# Check events
Get-WinEvent -LogName "DFS Replication" -MaxEvents 10
```

---

## For Your PLR

Include these elements:

**Technical Evidence:**
- Screenshots of DFS Management console
- PowerShell command outputs
- Event Viewer replication events
- Namespace access from File Explorer
- Replication verification (files syncing)

**Explanation:**
- Why DFS Namespace vs simple file shares?
- Why domain-based vs standalone namespace?
- How site-aware referrals work
- Why Full Mesh topology was chosen
- How replication schedule optimizes bandwidth

**Reflection:**
- What challenges did you face?
- How did you troubleshoot replication issues?
- How did you verify replication was working?
- What would you do differently?

---

**Quick access:** Keep this page open during your lab for fast command reference!

