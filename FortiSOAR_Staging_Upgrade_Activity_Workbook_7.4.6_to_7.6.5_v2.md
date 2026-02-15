\% FortiSOAR Upgrade Activity Tracking Workbook % Staging Environment %
Generated on 2026-02-15

------------------------------------------------------------------------

# Document Purpose

This workbook tracks and executes a full FortiSOAR upgrade:

Current Version: 7.4.6\
Target Version: 7.6.5\
Upgrade Path: 7.4.6 → 7.5.0 → 7.6.5

Environment Type: Standalone (Staging)

------------------------------------------------------------------------

# SECTION 1 -- Pre‑Upgrade Checklist (7.4.6 → 7.5.0)

## System Information (Fill Before Upgrade)

  Item                        Value
  --------------------------- -------
  Hostname                    
  IP Address                  
  Current OS Version          
  Current Kernel              
  Current FortiSOAR Version   
  Snapshot Taken (Yes/No)     
  Snapshot Name               
  Date                        
  Engineer                    

------------------------------------------------------------------------

## Mandatory Pre‑Checks

-   [ ] Take full VM snapshot
-   [ ] Stop all scheduled playbooks
-   [ ] Ensure no active playbooks running
-   [ ] Export Built‑in connectors (Settings → Export Wizard)
-   [ ] Confirm repo.fortisoar.fortinet.com reachable
-   [ ] Verify disk space (/, /boot, /opt, /var)
-   [ ] Disable IPv6 if not used
-   [ ] Confirm no duplicate entries in /etc/fstab
-   [ ] Unmount external NFS if mounted

------------------------------------------------------------------------

# SECTION 2 -- Upgrade 7.4.6 → 7.5.0 (Includes OS Migration)

## Download In-Place Upgrade File

wget
https://repo.fortisoar.fortinet.com/7.5.0/fortisoar-inplace-upgrade-7.5.0.bin\
chmod +x fortisoar-inplace-upgrade-7.5.0.bin

## Execute Upgrade

sh fortisoar-inplace-upgrade-7.5.0.bin

This will:

-   Initiate Leapp OS migration (RHEL 8 → RHEL 9)
-   Install new kernel (5.14.x.el9)
-   Reboot system
-   Complete FortiSOAR 7.5.0 installation

------------------------------------------------------------------------

## Post-Reboot Verification (7.5.0)

cat /etc/redhat-release\
uname -r\
rpm -qi cyops \| grep Version\
csadm services --status

Expected:

-   Rocky Linux 9.x
-   Kernel 5.14.x.el9
-   FortiSOAR 7.5.0
-   All services running

------------------------------------------------------------------------

# SECTION 3 -- Stabilization After 7.5.0

-   [ ] GUI login successful
-   [ ] Playbooks accessible
-   [ ] Manual workflow test completed
-   [ ] All services healthy
-   [ ] No errors in /var/log/cyops/

------------------------------------------------------------------------

# SECTION 4 -- Upgrade 7.5.0 → 7.6.5 (Upgrade Framework)

## Run Readiness Check

csadm upgrade check-readiness --version 7.6.5

Review output in:

/opt/fsr-elevate/elevate/outputs/

Ensure all checks return true.

------------------------------------------------------------------------

## Execute Upgrade

csadm upgrade execute --target-version 7.6.5

Monitor log:

tail -f /var/log/cyops/upgrade-fortisoar-7.6.5-\*.log

------------------------------------------------------------------------

# SECTION 5 -- Final Validation (7.6.5)

rpm -qi cyops \| grep Version\
csadm services --status

Confirm:

-   FortiSOAR 7.6.5
-   All services running
-   No 500 errors in GUI
-   Background tasks functional
-   Schedules enabled

------------------------------------------------------------------------

# SECTION 6 -- Rollback Plan

If failure occurs:

1.  Power off VM
2.  Revert to snapshot
3.  Boot system
4.  Validate 7.4.6 restored

------------------------------------------------------------------------

# SECTION 7 -- Upgrade Activity Log

  Step               Date   Engineer   Status   Notes
  ------------------ ------ ---------- -------- -------
  Snapshot                                      
  7.5.0 Upgrade                                 
  OS Verified                                   
  7.6.5 Readiness                               
  7.6.5 Upgrade                                 
  Final Validation                              

------------------------------------------------------------------------

Upgrade Completed By: \_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\
Date: \_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_
