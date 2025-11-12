# Week 2 Lab Guide: GUI and Terminal Methods

Complete guide showing both GUI (clicking) and Terminal (command line) methods for each task.

---

## Lab Overview

### PC Roles:
- **PC1 = HOST** (Where you create users, groups, and shared folders)
- **PC2 = CLIENT** (Accesses PC1's shared resources)

### Which PC Does What:
- **Tasks 1-8:** Do on BOTH PC1 and PC2 (so they can see each other)
- **Tasks 9-16:** Do ONLY on PC1 (create users, groups, share folder)
- **Tasks 17-23:** Use both PCs (PC2 accesses PC1's folder)

---

## Important Terms

| Term | Definition |
|------|------------|
| Workgroup | A group name for computers that want to share files with each other |
| Network Discovery | Letting your computer find other computers nearby |
| Ping | Sending a "hello" message to another computer to check if it's there |
| Share/Shared Folder | A folder that other computers on the network can access |
| User Account | A username and password that lets someone use the computer |
| Group | A collection of users |
| Permissions | Rules about who can open, edit, or delete files |
| GUI | Graphical User Interface - clicking buttons and windows |
| Terminal/CMD | Command Prompt - typing commands |
| Administrator | A special account that can change important computer settings |

---

## TASK 1: RENAME YOUR COMPUTER

**Do on:** BOTH PC1 and PC2 (give each a unique name)

### GUI METHOD

1. Click **Start** button (bottom-left corner of screen)
2. Click **Settings** icon (gear icon, above the power button)
3. In Settings window, click **System** (first option, top-left, has a laptop icon)
4. On the left sidebar, scroll to the very bottom
5. Click **About** (last option in left menu)
6. On the right side, scroll down to "Device specifications" section
7. Click **Rename this PC** button (blue button near computer name)
8. A small window pops up in the center
9. Type: **PC1** in the text box
10. Click **Next** button (bottom-right of popup)
11. Click **Restart now** button

### TERMINAL METHOD

1. Right-click **Start** button (bottom-left corner)
2. Click **Windows PowerShell (Admin)**
3. Copy and run this command:

```powershell
Rename-Computer -NewName "PC1"
```

4. Then restart:

```powershell
Restart-Computer
```

---

## TASK 2: CHANGE WORKGROUP NAME

**Do on:** BOTH PC1 and PC2 (both must use "NetAppsLab")

### GUI METHOD

1. Right-click **Start** button (bottom-left corner, Windows logo)
2. In the menu that pops up, click **System** (about 4th option from top, has a laptop icon)
3. The Settings window opens to "About" page
4. On the right side, look for "Related settings" section (blue links)
5. Click **Advanced system settings** (blue link on the right side)
6. A "System Properties" window opens
7. At the top of this window, click the **Computer Name** tab
8. At the bottom of the window, click **Change...** button
9. A smaller window opens titled "Computer Name/Domain Changes"
10. In "Member of" section (bottom half), click the **Workgroup** circle/radio button
11. In the text box next to Workgroup, delete old text and type: **NetAppsLab**
12. Click **OK** button (bottom-right)
13. A welcome message appears - click **OK**
14. Another message says restart - click **OK**
15. Click **Close** on System Properties window
16. Click **Restart Now** button

### TERMINAL METHOD

1. Right-click **Start** button
2. Click **Command Prompt (Admin)** or **PowerShell (Admin)**
3. Copy and run this command:

```cmd
wmic computersystem where name="%computername%" call joindomainorworkgroup name="NetAppsLab"
```

4. Restart computer

---

## TASK 3: ENABLE NETWORK DISCOVERY

**Do on:** BOTH PC1 and PC2

### GUI METHOD

1. Click **Start** button (bottom-left, Windows logo)
2. Type: **Control Panel** (start typing, no need to click search box)
3. Press **Enter** - Control Panel window opens
4. In the top-right corner, make sure "View by:" is set to **Category**
5. Click **Network and Internet** (green globe icon, first option)
6. Click **Network and Sharing Center** (second option, has network icon)
7. On the LEFT sidebar, click **Change advanced sharing settings** (blue link)
8. You'll see "Private", "Guest or Public", and "All Networks" sections
9. Click the down arrow next to **Private** (if not already expanded)
10. Under Private section, find "Network discovery"
11. Click the circle next to **Turn on network discovery**
12. Also check the box: **Turn on automatic setup of network connected devices**
13. Scroll down a bit to "File and printer sharing"
14. Click the circle next to **Turn on file and printer sharing**
15. Scroll to the very bottom of the page
16. Click **Save changes** button (bottom-right corner)

### TERMINAL METHOD

1. Right-click **Start** → Click **Command Prompt (Admin)**
2. Copy and run these commands:

```cmd
netsh advfirewall firewall set rule group="Network Discovery" new enable=Yes
```

```cmd
netsh advfirewall firewall set rule group="File and Printer Sharing" new enable=Yes
```

---

## TASK 4: TURN OFF FIREWALL (Temporarily for lab)

**Do on:** BOTH PC1 and PC2

### GUI METHOD

1. Click **Start** button (bottom-left corner)
2. Type: **firewall**
3. In the results, click **Windows Defender Firewall** (has a shield icon)
4. Windows Defender Firewall window opens
5. On the LEFT sidebar, click **Turn Windows Defender Firewall on or off** (second option, has a pencil icon)
6. You'll see two sections: "Private network settings" and "Public network settings"
7. Under **Private network settings** (top section):
8. Click the circle next to **Turn off Windows Defender Firewall (not recommended)**
9. At the bottom of the window, click **OK** button

**Remember: Turn it back ON after lab!**
- Repeat steps 1-6
- Click circle next to **Turn on Windows Defender Firewall**
- Click **OK**

### TERMINAL METHOD

1. Right-click **Start** → Click **Command Prompt (Admin)**
2. Copy and run this command:

```cmd
netsh advfirewall set privateprofile state off
```

**To turn back ON:**

```cmd
netsh advfirewall set privateprofile state on
```

---

## TASK 5: CHECK YOUR COMPUTER NAME

**Do on:** BOTH PC1 and PC2 (verify each has correct name and workgroup)

### GUI METHOD

1. Click **Start** button (bottom-left corner)
2. Click **Settings** icon (gear icon)
3. Click **System** (first big icon, top-left)
4. On the LEFT sidebar, scroll to bottom
5. Click **About** (last option)
6. On the RIGHT side, look at the top section called "Device specifications"
7. Find the line that says **Device name:** - this shows **PC1** or **PC2**
8. Below that, find **Workgroup:** - should show **NetAppsLab**

### TERMINAL METHOD

1. Click **Start** → Type **cmd** → Press **Enter**
2. Copy and run this command:

```cmd
hostname
```

Shows: **PC1** or **PC2**

---

## TASK 6: PING (TEST IF COMPUTERS CAN TALK)

**Do on:** BOTH PCs (PC1 pings PC2, and PC2 pings PC1)

### GUI METHOD (Alternative)

**No direct GUI for ping, but you can check if computer is visible:**

1. Click **File Explorer** icon (folder icon on taskbar at bottom)
   - OR press **Windows key + E**
2. File Explorer window opens
3. On the LEFT sidebar, scroll down
4. Click **Network** (near the bottom, has a network icon)
5. In the main area, you should see icons for:
   - **PC1** (if you're on PC2)
   - **PC2** (if you're on PC1)
6. If you see the other computer = SUCCESS!
7. If you see "Network discovery is turned off" = Go back to Task 3

### TERMINAL METHOD

1. Click **Start** → Type **cmd** → Press **Enter**
2. Copy and run this command:

```cmd
ping PC2
```

(Replace PC2 with the other computer name)

**SUCCESS:** `Reply from PC2... bytes=32`  
**FAILURE:** `Request timed out`

---

## TASK 7: VIEW SHARED FOLDERS ON ANOTHER COMPUTER

**Do on:** BOTH PCs (each PC checks if they can see the other)

### GUI METHOD

1. Click **File Explorer** icon (folder on taskbar)
2. File Explorer opens
3. At the TOP, find the address bar (shows "This PC" or "Quick access")
4. Click inside the address bar (the white/gray bar at top)
5. Type: `\\PC2` (use two backslashes, then the other computer name)
   - If on PC2, type: `\\PC1`
6. Press **Enter**
7. A folder window opens showing all shared folders on that computer
8. You should see **SharedData** folder (after you create it in next tasks)
9. If you see folders = SUCCESS
10. If you get error "Windows cannot access..." = Check firewall, network discovery

### TERMINAL METHOD

1. Open **Command Prompt**
2. Copy and run this command:

```cmd
net view \\PC2
```

(Replace PC2 with the other computer name)

Shows list of shared folders

---

## TASK 8: CHECK IF NETWORK SERVICES ARE RUNNING

**Do on:** BOTH PC1 and PC2

### GUI METHOD

1. Press **Windows key + R** (on keyboard, hold Windows key, press R)
2. A small "Run" box appears at bottom-left
3. Type: **services.msc**
4. Click **OK** button (or press Enter)
5. "Services" window opens with a big list
6. The list is alphabetical - scroll down to the **S** section
7. Look for **Server** (Description says "Supports file, print...")
8. Check the **Status** column (middle) - should say **Running**
9. Scroll down to the **W** section
10. Look for **Workstation** (Description says "Creates and maintains...")
11. Check the **Status** column - should say **Running**

**If either shows "Stopped":**
- Right-click the service name
- Click **Start** from menu
- Wait for Status to change to "Running"

### TERMINAL METHOD

1. Click **Start** → Type **powershell** → Press **Enter**
2. Copy and run these commands:

```powershell
Get-Service LanmanServer
```

```powershell
Get-Service LanmanWorkstation
```

Both should show **Running**

---

## BEFORE ACTIVITY 2: Reinforce Key Concepts

Complete these questions before starting Tasks 9-16:

1. Local Users and Groups only apply to the current computer and are ideal for **W______** environments.
2. **S______** permissions control access over the network.
3. **N______** permissions control access locally and remotely.
4. The most **r________** permission determines what a user can actually do when combining Share and NTFS permissions.
5. The Principle of **L______** Privilege states that users should have only the minimum access required.

**Answers:**
1. Workgroup
2. Share
3. NTFS
4. restrictive
5. Least

---

## TASK 9: CREATE A USER ACCOUNT

**Do on:** ONLY PC1 (the host)

### GUI METHOD

1. Press **Windows key + R** keys together
2. A small "Run" box appears in bottom-left corner
3. Type: **compmgmt.msc**
4. Press **Enter** or click **OK**
5. "Computer Management" window opens (big window with tree on left)
6. On the LEFT panel, look for **Local Users and Groups**
7. Click the arrow next to "Local Users and Groups" to expand it
8. Two items appear below it: "Users" and "Groups"
9. Click **Users**
10. On the RIGHT panel, you see a list of existing users
11. Right-click in the EMPTY WHITE SPACE (not on an existing user)
12. A menu pops up - Click **New User...**
13. A "New User" window opens
14. Fill in the form:
    - **User name:** Type **User1**
    - **Full name:** (leave empty or type Full Name)
    - **Description:** Type **Test User**
    - **Password:** Type **P@ssword123**
    - **Confirm password:** Type **P@ssword123** again
15. Look at the checkboxes below:
    - UNCHECK "User must change password at next logon"
    - CHECK "Password never expires"
16. Click **Create** button (bottom-right)
17. User1 appears in the list!
18. Click **Close** button

### TERMINAL METHOD

1. Right-click **Start** → Click **Command Prompt (Admin)**
2. Copy and run this command:

```cmd
net user User1 P@ssword123 /add
```

**To verify:**

```cmd
net user
```

---

## TASK 10: CREATE A GROUP

**Do on:** ONLY PC1 (the host)

### GUI METHOD

1. Press **Windows key + R**
2. Type: **compmgmt.msc**
3. Press **Enter**
4. Computer Management window opens
5. On the LEFT panel, find **Local Users and Groups**
6. Click the arrow to expand it (if not already expanded)
7. You see two folders: "Users" and "Groups"
8. Click **Groups**
9. On the RIGHT panel, you see existing groups (like Administrators, Users, etc.)
10. Right-click in the EMPTY WHITE SPACE (below the groups list)
11. A menu pops up - Click **New Group...**
12. A "New Group" window opens
13. Fill in:
    - **Group name:** Type **ProjectTeam**
    - **Description:** Type **Project Team Members** (optional)
14. Don't add members yet (we'll do that next)
15. Click **Create** button (bottom-right)
16. ProjectTeam appears in the list!
17. Click **Close** button

### TERMINAL METHOD

1. Open **Command Prompt (Admin)**
2. Copy and run this command:

```cmd
net localgroup ProjectTeam /add
```

**To verify:**

```cmd
net localgroup
```

---

## TASK 11: ADD USER TO A GROUP

**Do on:** ONLY PC1 (the host)

### GUI METHOD

1. Press **Windows key + R**
2. Type: **compmgmt.msc**
3. Press **Enter**
4. Expand **Local Users and Groups** (click arrow)
5. Click **Groups** (not Users)
6. On the RIGHT side, find **ProjectTeam** in the list
7. **Double-click ProjectTeam** (double-click, not single!)
8. "ProjectTeam Properties" window opens
9. In the middle section, you see "Members:" with an empty box below
10. At the bottom, click **Add...** button
11. "Select Users" window pops up
12. In the bottom section, there's a box "Enter the object names to select"
13. Click inside that box and type: **User1**
14. Click **Check Names** button (bottom-left of this window)
15. If correct, "User1" becomes underlined
16. Click **OK** (bottom-right)
17. Back in ProjectTeam Properties, you see **User1** in the Members list!
18. Click **OK** to close

### TERMINAL METHOD

1. Open **Command Prompt (Admin)**
2. Copy and run this command:

```cmd
net localgroup ProjectTeam User1 /add
```

---

## TASK 12: CHECK WHO IS IN A GROUP

**Do on:** ONLY PC1 (the host)

### GUI METHOD

1. Press **Windows key + R**
2. Type: **compmgmt.msc** → Press **Enter**
3. Expand **Local Users and Groups** → Click **Groups**
4. Find **ProjectTeam** in the list on the right
5. **Double-click ProjectTeam**
6. "ProjectTeam Properties" window opens
7. In the middle section, under "Members:", you see the list
8. Should show: **User1**
9. Click **OK** or **Cancel** to close

### TERMINAL METHOD

1. Open **Command Prompt**
2. Copy and run this command:

```cmd
net localgroup ProjectTeam
```

Shows members list

---

## TASK 13: CREATE A FOLDER

**Do on:** ONLY PC1 (the host)

### GUI METHOD

1. Click **File Explorer** icon (folder on taskbar at bottom)
   - OR press **Windows key + E**
2. File Explorer opens
3. On the LEFT sidebar, click **This PC** (has a computer monitor icon)
4. In the main area, you see drives (like "Local Disk (C:)")
5. **Double-click Local Disk (C:)**
6. You're now inside the C: drive (address bar at top shows "This PC > Local Disk (C:)")
7. Right-click in the EMPTY WHITE SPACE (not on an existing folder)
8. A menu pops up → Hover over **New** → A side menu appears
9. Click **Folder** from the side menu
10. A new folder appears named "New folder" with the name highlighted in blue
11. Type: **SharedData** (this replaces "New folder")
12. Press **Enter**
13. Folder created! You should see SharedData folder in C: drive

### TERMINAL METHOD

1. Open **Command Prompt**
2. Copy and run this command:

```cmd
mkdir C:\SharedData
```

**To verify:**

```cmd
dir C:\
```

---

## TASK 14: SHARE A FOLDER (Give Network Access)

**Do on:** ONLY PC1 (the host)

### GUI METHOD

1. Open **File Explorer** → Go to **C:\ drive**
2. Find the **SharedData** folder you just created
3. **Right-click SharedData** folder
4. A big menu appears → Click **Properties** (at the very bottom)
5. "SharedData Properties" window opens with tabs at the top
6. Click the **Sharing** tab (3rd or 4th tab at top)
7. You see "Network File and Folder Sharing" section
8. In the middle of this tab, click **Advanced Sharing** button
9. "Advanced Sharing" window pops up (smaller window)
10. At the top, CHECK the box: **Share this folder**
11. Below that, "Share name:" should show **SharedData** (don't change it)
12. Below that, click **Permissions** button
13. "Permissions for SharedData" window opens
14. In the top section "Group or user names:", you see **Everyone**
15. Click **Everyone** to select it
16. At the bottom, click **Remove** button (Everyone disappears)
17. Now click **Add** button (to add ProjectTeam)
18. "Select Users or Groups" window pops up
19. In the box at bottom, type: **ProjectTeam**
20. Click **Check Names** button (bottom-left) - ProjectTeam gets underlined
21. Click **OK**
22. Back in Permissions window, **ProjectTeam** now appears in the list
23. Click **ProjectTeam** to select it
24. In the bottom section "Permissions for ProjectTeam":
25. CHECK the box under **Allow** for **Change** (Read should auto-check too)
26. Click **OK** (bottom-right)
27. Click **OK** on Advanced Sharing window
28. Click **Close** on SharedData Properties (or keep open for next task)

### TERMINAL METHOD

1. Open **Command Prompt (Admin)**
2. Copy and run this command:

```cmd
net share SharedData=C:\SharedData /grant:ProjectTeam,CHANGE
```

---

## TASK 15: SET NTFS PERMISSIONS (File-Level Security)

**Do on:** ONLY PC1 (the host)

### GUI METHOD

1. If SharedData Properties is still open, skip to step 4
2. Otherwise: Right-click **SharedData** folder → Click **Properties**
3. SharedData Properties window opens
4. At the top, click the **Security** tab (should be 4th tab)
5. You see two sections:
   - Top: "Group or user names:" (lists users/groups)
   - Bottom: "Permissions for [whoever is selected]"
6. In the middle, click **Edit** button
7. "Permissions for SharedData" window pops up
8. Click **Add** button (bottom-left area)
9. "Select Users or Groups" window appears
10. In the bottom box "Enter the object names", type: **ProjectTeam**
11. Click **Check Names** (bottom-left) - ProjectTeam gets underlined
12. Click **OK**
13. Back in Permissions window, in the top list, click **ProjectTeam** to select it
14. In the bottom section "Permissions for ProjectTeam":
15. Find the row that says **Modify** (about 3rd row)
16. CHECK the box under **Allow** column for **Modify**
17. When you check Modify, it automatically checks:
    - Read & execute
    - List folder contents
    - Read
    - Write
18. Click **OK** button (bottom-right)
19. Click **OK** or **Close** to close SharedData Properties

### TERMINAL METHOD

1. Open **Command Prompt (Admin)**
2. Copy and run this command:

```cmd
icacls "C:\SharedData" /grant ProjectTeam:(OI)(CI)M
```

**What the codes mean:**
- `icacls` = change permissions
- `(OI)` = Object Inherit (applies to files)
- `(CI)` = Container Inherit (applies to folders)
- `M` = Modify permission

---

## TASK 16: VIEW EFFECTIVE PERMISSIONS (What User1 Can Actually Do)

**Do on:** ONLY PC1 (the host)

### GUI METHOD

1. Right-click **SharedData** folder → **Properties**
2. Click **Security** tab
3. Click **Advanced** button (bottom area)
4. "Advanced Security Settings for SharedData" window opens
5. At the top, click **Effective Access** tab (last tab on the right)
6. You see "View the effective access for a user, group, or device"
7. Click **Select a user** link (blue link)
8. "Select User or Group" window pops up
9. In the bottom box, type: **User1**
10. Click **Check Names** button - User1 gets underlined
11. Click **OK**
12. Back in Advanced Security Settings
13. Click **View effective access** button
14. A list appears showing exactly what User1 can do:
    - Traverse folder / execute file
    - List folder / read data
    - Read attributes
    - Read extended attributes
    - Create files / write data
    - Create folders / append data
    - Write attributes
    - Write extended attributes
    - Delete
    - Read permissions
15. Click **OK** to close all windows

### TERMINAL METHOD

1. Open **Command Prompt**
2. Copy and run this command:

```cmd
icacls "C:\SharedData"
```

Shows all permissions

**Permission codes:**
- `F` = Full control
- `M` = Modify
- `RX` = Read & execute
- `R` = Read-only

---

## TASK 17: MAP NETWORK DRIVE (Connect to Shared Folder from Other PC)

**Do on:** PC2

**IMPORTANT:** Before mapping the drive, you must first create User1 on PC2:
- Use Task 9 steps on PC2
- Username: User1
- Password: P@ssword123 (same as PC1)

### GUI METHOD

1. Open **File Explorer**
2. Click **This PC** (left sidebar)
3. At the top ribbon, click **Computer** tab (or you might already see the ribbon)
4. In the ribbon, click **Map network drive** button (has a drive icon with network cable)
5. A "Map Network Drive" window pops up
6. **Drive:** Click the dropdown and select **Z:**
7. **Folder:** Click in the box and type `\\PC1\SharedData`
   (Replace PC1 with actual computer name)
8. CHECK the box: **Connect using different credentials**
9. At the bottom, click **Finish** button
10. A login window appears "Windows Security"
11. **User name:** Type **PC1\User1** (computer name backslash username)
12. **Password:** Type **P@ssword123**
13. CHECK the box: **Remember my credentials**
14. Click **OK**
15. Z: drive appears in File Explorer!
16. You can now browse SharedData as if it's a local drive

### TERMINAL METHOD

1. Open **Command Prompt**
2. Copy and run this command:

```cmd
net use Z: \\PC1\SharedData /user:PC1\User1 P@ssword123
```

**IMPORTANT NOTE:** `net use` creates a temporary session. The drive will disappear after logout unless `/persistent:yes` is used.

**To make permanent (reconnect after restart):**

```cmd
net use Z: \\PC1\SharedData /user:PC1\User1 P@ssword123 /persistent:yes
```

**After mapping, test access on PC2:**
- Open Z: drive in File Explorer
- Create a new text file
- Edit it
- Delete it
- Confirm that permissions behave as configured

---

## TASK 18: VIEW ALL MAPPED DRIVES

**Do on:** PC2 (to see the mapped Z: drive)

### GUI METHOD

1. Open **File Explorer**
2. Click **This PC** (left sidebar)
3. In the main area, look under "Network locations" section
4. You'll see drives like: **SharedData (\\PC1) (Z:)**
5. Z: drive is your mapped network drive

### TERMINAL METHOD

1. Open **Command Prompt**
2. Copy and run this command:

```cmd
net use
```

Shows all mapped drives

---

## TASK 19: DISCONNECT/REMOVE MAPPED DRIVE

**Do on:** PC2 (disconnect from PC1's shared folder)

### GUI METHOD

1. Open **File Explorer**
2. Click **This PC**
3. Under "Network locations", find **Z:** drive
4. **Right-click Z:** drive
5. A menu appears → Click **Disconnect**
6. Z: drive disappears!

### TERMINAL METHOD

1. Open **Command Prompt**
2. Copy and run this command:

```cmd
net use Z: /delete
```

**To remove ALL drives:**

```cmd
net use * /delete
```

---

## TASK 20: REMOVE USER FROM GROUP (Test Access Denial)

**Do on:** PC1 (remove User1 from ProjectTeam)

**Purpose:** After removing User1 from ProjectTeam, go to PC2 and attempt to access Z: drive again. You should get **"Access Denied"** error, proving permissions are working correctly.

### GUI METHOD

1. Press **Windows key + R**
2. Type: **compmgmt.msc** → Press **Enter**
3. Expand **Local Users and Groups** → Click **Groups**
4. Find and **double-click ProjectTeam**
5. "ProjectTeam Properties" window opens
6. In the Members list, click **User1** to select it
7. Click **Remove** button (below the Members list)
8. User1 disappears from the list
9. Click **OK** to close

### TERMINAL METHOD

1. Open **Command Prompt (Admin)**
2. Copy and run this command:

```cmd
net localgroup ProjectTeam User1 /delete
```

---

## TASK 21: DELETE A USER ACCOUNT

**Do on:** Either PC (for cleanup after lab)

### GUI METHOD

1. Press **Windows key + R**
2. Type: **compmgmt.msc** → Press **Enter**
3. Expand **Local Users and Groups** → Click **Users**
4. In the right panel, find **User1**
5. **Right-click User1**
6. A menu appears → Click **Delete**
7. A warning appears: "Are you sure you want to delete the user User1?"
8. Click **Yes**
9. User1 disappears from the list

### TERMINAL METHOD

1. Open **Command Prompt (Admin)**
2. Copy and run this command:

```cmd
net user User1 /delete
```

---

## TASK 22: DELETE A GROUP

**Do on:** PC1 (for cleanup after lab)

### GUI METHOD

1. Press **Windows key + R**
2. Type: **compmgmt.msc** → Press **Enter**
3. Expand **Local Users and Groups** → Click **Groups**
4. In the right panel, find **ProjectTeam**
5. **Right-click ProjectTeam**
6. A menu appears → Click **Delete**
7. A warning appears: "Are you sure you want to delete the group ProjectTeam?"
8. Click **Yes**
9. ProjectTeam disappears from the list

### TERMINAL METHOD

1. Open **Command Prompt (Admin)**
2. Copy and run this command:

```cmd
net localgroup ProjectTeam /delete
```

---

## TASK 23: REMOVE SHARE FROM FOLDER (Stop Sharing)

**Do on:** PC1 (stop sharing the folder)

### GUI METHOD

1. Go to **C:\** drive in File Explorer
2. Find **SharedData** folder
3. **Right-click SharedData** → **Properties**
4. Click **Sharing** tab
5. Click **Advanced Sharing** button
6. UNCHECK the box: **Share this folder**
7. Click **OK**
8. Click **OK** or **Close**
9. Folder is no longer shared!

### TERMINAL METHOD

1. Open **Command Prompt (Admin)**
2. Copy and run this command:

```cmd
net share SharedData /delete
```

---

## QUICK REFERENCE CHEAT SHEET

| Action | Terminal Command | GUI Location |
|--------|------------------|--------------|
| Rename computer | `Rename-Computer -NewName "PC1"` | Settings → System → About → Rename this PC |
| Change workgroup | `wmic computersystem where name="%computername%" call joindomainorworkgroup name="NetAppsLab"` | System → Advanced system settings → Computer Name → Change |
| Check computer name | `hostname` | Settings → System → About |
| Enable Network Discovery | `netsh advfirewall firewall set rule group="Network Discovery" new enable=Yes` | Control Panel → Network and Sharing Center → Advanced sharing settings |
| Turn off firewall | `netsh advfirewall set privateprofile state off` | Control Panel → Windows Defender Firewall → Turn off |
| Ping another PC | `ping PC2` | (No GUI - use terminal) |
| View shared folders | `net view \\PC2` | File Explorer address bar: `\\PC2` |
| Check services | `Get-Service LanmanServer` | Win+R → services.msc |
| Create user | `net user User1 Password /add` | Win+R → compmgmt.msc → Users → New User |
| Create group | `net localgroup ProjectTeam /add` | Win+R → compmgmt.msc → Groups → New Group |
| Add user to group | `net localgroup ProjectTeam User1 /add` | compmgmt.msc → Groups → Double-click group → Add |
| Check group members | `net localgroup ProjectTeam` | compmgmt.msc → Groups → Double-click group |
| Create folder | `mkdir C:\SharedData` | File Explorer → Right-click → New → Folder |
| Share folder | `net share SharedData=C:\SharedData /grant:ProjectTeam,CHANGE` | Right-click folder → Properties → Sharing → Advanced Sharing |
| Set NTFS permissions | `icacls "C:\SharedData" /grant ProjectTeam:(OI)(CI)M` | Right-click folder → Properties → Security → Edit → Add |
| View permissions | `icacls "C:\SharedData"` | Right-click folder → Properties → Security → Advanced → Effective Access |
| Map network drive | `net use Z: \\PC1\SharedData /user:PC1\User1 Password` | File Explorer → This PC → Map network drive |
| View mapped drives | `net use` | File Explorer → This PC → Network locations |
| Disconnect drive | `net use Z: /delete` | Right-click drive → Disconnect |
| Remove user from group | `net localgroup ProjectTeam User1 /delete` | compmgmt.msc → Groups → Double-click → Select user → Remove |
| Delete user | `net user User1 /delete` | compmgmt.msc → Users → Right-click user → Delete |
| Delete group | `net localgroup ProjectTeam /delete` | compmgmt.msc → Groups → Right-click group → Delete |
| Stop sharing folder | `net share SharedData /delete` | Right-click folder → Properties → Sharing → Advanced Sharing → Uncheck |

---

## Troubleshooting Common Issues

If you encounter problems during the lab, use these diagnostic commands:

### Check SMB Services

```powershell
Get-Service LanmanServer
```

```powershell
Get-Service LanmanWorkstation
```

Both should show **Status: Running**

### Clear All Network Connections

If mapped drives aren't working:

```cmd
net use * /delete
```

This removes all mapped drives and lets you start fresh.

### View Current Permissions

To check permissions on SharedData folder:

```cmd
icacls "C:\SharedData"
```

Shows all users/groups and their permission levels.

---

## Tips for Choosing a Method

**Use GUI When:**
- You're learning and want to see what options are available
- You need to browse through settings
- You're not sure of the exact command
- You want visual confirmation of changes

**Use Terminal When:**
- You know exactly what you want to do
- You need to repeat the same task multiple times
- You want to create scripts to automate tasks
- You're working on a remote computer
- You're documenting steps for others

---

**Practice both methods to become a networking pro!**
