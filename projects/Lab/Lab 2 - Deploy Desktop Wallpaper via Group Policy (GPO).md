# **Objective**

User Group Policy in Active Directory domain to centrally deploy a desktop wallpaper to all domain users.

This demonstrates:
- Creating and linking a GPO
- Using a UNC path from SYSVOL
- Forcing policy updates on a domain client
- Troubleshooting black/failed wallpapers

## Lab Environment

| Machine | Role | OS |
|---------|------|----|
| DC01 | Domain Controller | Windows Server 2019 |
| CLIENT01 | Domain-joined Client Workstation | Windows 11 Pro |
| Domain | LAB.local | Active Directory |
| Hypervisor | VMWare Workstation Pro | Local Homelab |

## Key Concepts

The wallpaper must be accessible from the client's perspective, not just exist on the domain controller's local disk.

Using the built-in SYSVOL share ensures all domain computers can read the file.

Final working path: **\\DC01\SYSVOL\LAB.local\scripts\img0.jpg**

## Step 1 - Place Wallpaper in SYSVOL

On DC01, copy image to: \

\\DC01\SYSVOL\LAB.local\scripts\img0.jpg

## Step 2 - Create and Link a GPO

On DC01:
1. Open Group Policy Management
2. Expand Forest -> Domain -> LAB.local
3. Right Click LAB.local
4. Select Create a GPO in this domain, and Link it here
5. Name it to LAB - Desktop Wallpaper

## Step 3 - Configure Wallpaper Policy

Edit the new GPO and go to:
- User Configuration
- Policies
- Administrative Templates
- Desktop
- Desktop
- Desktop Wallpaper

Set:
- Enabled
- Wallpaper Name: \\DC01\SYSVOL\LAB.local\scripts\img0.jpg
- Wallpaper Style: Fill

Apply and close the editor

## Step 4 - Apply Policy on Client

On CLIENT01, log in as domain user and run GPUPDATE /FORCE

Then:
1. Sign out
2. Sign back in

The desktop wallpaper should now update to the deployed image.

## Validation Tests

From CLIENT01, verify the file is reachable:
1. Press Win + R
2. Open \\DC01\SYSVOL\LAB.local\scripts
3. Double Click img0.jpg

If the image opens, the path and permissions are correct.

To confirm the GPO applied: GPRESULT /R

Check under **Applied Gropu Policy Objects** for:

LAB - Desktop Wallpaper

## Troubleshooting Notes

**Symptom: Black Wallpaper after Logon**

Cause: Client cannot read the image from the configured path.

*Fix*:
- Use a UNC path in SYSVOL instead of a local C:\path.
- Confirm the image opens directly from: \\DC01\SYSVOL\Lab.Local\Scripts\img0.jpg

**Symptoms: "You need a new app to open this" when clicking Personalize**

Cause: Another GPO disabled access to Control Panel/Settings.

*Fix*:
Set the following policy to Not Configured if testing personalization

## Outcome

Group policy wallpaper paths must be accessible at user logon.
Storing the image in SYSVOL provides a reliable, domain-wide, read-accessible location.

- Successfully deployed a domain-wide desktop wallpaper via GPO
- Verified Client access using a UNC path from SYSVOL
- Resolved black wallpaper issue by correcting the file path location

This lab demonstrates practical Gropu Policy Management and troubleshooting in an Active Directory environment, suitable for entry-level SysAdmin, NOC, or Help Desk Portfolio projects.