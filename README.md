# FortiSOAR-Upgrade
\% FortiSOAR Staging Upgrade Runbook % Version 7.4.6 → 7.6.5 % Generated
on 2026-02-15

------------------------------------------------------------------------

# 1. Document Overview

This document provides a structured, step‑by‑step upgrade procedure for
the Staging FortiSOAR instance.

**Current Version:** 7.4.6\
**Target Version:** 7.6.5\
**Upgrade Path:**\
7.4.6 → 7.5.0 (OS Migration Included) → 7.6.5

Environment Type: Standalone (Staging)

------------------------------------------------------------------------

# 2. Pre‑Upgrade Checklist

## 2.1 Mandatory Preparations

-   [ ] Take full VM snapshot
-   [ ] Stop all scheduled playbooks
-   [ ] Confirm no active playbooks running
-   [ ] Export built‑in connectors
-   [ ] Verify repository connectivity
-   [ ] Confirm sufficient disk space
-   [ ] Unmount external NFS (if mounted)

------------------------------------------------------------------------

# 3. Upgrade 7.4.6 → 7.5.0 (Includes OS Migration)

## 3.1 Download Upgrade Package

``` bash
cd /
wget https://repo.fortisoar.fortinet.com/7.5.0/fortisoar-inplace-upgrade-7.5.0.bin
chmod +x fortisoar-inplace-upgrade-7.5.0.bin
```

## 3.2 Execute Upgrade

``` bash
sh fortisoar-inplace-upgrade-7.5.0.bin
```

------------------------------------------------------------------------

## 3.3 Reboot Phase

After execution completes, system will prompt:

"A reboot is required to continue the FortiSOAR upgrade"

During reboot:

-   System switches from Rocky Linux 8 → Rocky Linux 9
-   OS migration takes approximately 15--20 minutes
-   FortiSOAR 7.5.0 installation starts automatically
-   Configuration restoration runs automatically

------------------------------------------------------------------------

## 3.4 Monitoring During 7.5.0 Upgrade

After SSH access is restored:

``` bash
tail -f /var/log/leapp/leapp-upgrade.log
```

Important:

-   Do NOT execute other CLI commands during upgrade
-   Wait until FortiSOAR installation completes fully

Pre-upgrade report locations:

    /var/log/leapp/leapp-preupgrade.log
    /var/log/leapp/leapp-report.json
    /var/log/leapp/leapp-report.txt

------------------------------------------------------------------------

## 3.5 Post‑Upgrade Verification (7.5.0)

``` bash
cat /etc/redhat-release
uname -r
rpm -qi cyops | grep Version
csadm services --status
```

Expected Results:

-   Rocky Linux 9.x
-   Kernel 5.14.x.el9
-   FortiSOAR 7.5.0
-   All services running

------------------------------------------------------------------------

# 4. Stabilization Validation (After 7.5.0)

-   [ ] GUI login successful
-   [ ] Playbooks accessible
-   [ ] Manual workflow test executed
-   [ ] All services healthy
-   [ ] No critical errors in logs

------------------------------------------------------------------------

# 5. Upgrade 7.5.0 → 7.6.5

## 5.1 Run Readiness Check

``` bash
csadm upgrade check-readiness --version 7.6.5
```

Readiness report location:

    /opt/fsr-elevate/elevate/outputs/

------------------------------------------------------------------------

## 5.2 Execute Upgrade

``` bash
csadm upgrade execute --target-version 7.6.5
```

------------------------------------------------------------------------

## 5.3 Monitor Upgrade Progress

Primary Log:

``` bash
tail -f /var/log/cyops/upgrade-fortisoar-7.6.5-<timestamp>.log
```

Elevate Framework Log:

``` bash
tail -f /var/log/cyops/fsr-elevate/fsr-elevate-7.6.5-<timestamp>.log
```

------------------------------------------------------------------------

# 6. Post‑Upgrade Validation (7.6.5)

``` bash
rpm -qi cyops | grep Version
csadm services --status
```

Confirm:

-   FortiSOAR Version: 7.6.5
-   All services running
-   No HTTP 500 errors
-   Playbooks execute successfully
-   Background jobs functional

------------------------------------------------------------------------

# 7. Rollback Procedure

If failure occurs:

1.  Power off VM
2.  Revert to snapshot
3.  Boot system
4.  Validate original 7.4.6 environment restored

------------------------------------------------------------------------

# 8. Activity Tracking Log

  Step               Date   Engineer   Status   Notes
  ------------------ ------ ---------- -------- -------
  Snapshot Taken                                
  7.5.0 Upgrade                                 
  OS Verified                                   
  7.6.5 Readiness                               
  7.6.5 Upgrade                                 
  Final Validation                              

------------------------------------------------------------------------

# 9. Log File Reference Summary

  -----------------------------------------------------------------------------------------------------
  Phase                         Log File
  ----------------------------- -----------------------------------------------------------------------
  Pre‑Upgrade                   /var/log/leapp/leapp-preupgrade.log
  
  OS Migration                  /var/log/leapp/leapp-upgrade.log
  
  7.6.5 Upgrade                 /var/log/cyops/upgrade-fortisoar-7.6.5-*.log
-----------------------------------------------------------------------------------------------------
