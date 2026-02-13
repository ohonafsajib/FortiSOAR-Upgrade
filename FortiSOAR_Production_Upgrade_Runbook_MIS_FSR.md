# FortiSOAR Production Upgrade Runbook

**Environment:** MIS FSR (2-Node HA -- Active/Passive Cluster)\
**Current Version:** 7.4.4-3350\
**Target Version:** 7.4.5\
**Document Generated On:** 2026-02-13

------------------------------------------------------------------------

## Cluster Details

  Role                  Hostname       IP
  --------------------- -------------- ----------------
  Primary (Active)      mis-mrfsr-p    172.16.209.129
  Secondary (Passive)   mis-mrfsr2-p   172.16.209.120

------------------------------------------------------------------------

# PHASE 1 -- Pre-Upgrade Preparation

1.  Notify stakeholders and announce maintenance window.
2.  Freeze automation changes and stop all active schedules from GUI
    (Automation → Schedules).
3.  Take full VM snapshots of both nodes (Primary and Secondary).
4.  Export built-in connector configurations (Settings → Export Wizard).
5.  Verify disk space availability (minimum 500MB free in /boot).

``` bash
df -h
```

6.  Verify latest database backup exists:

``` bash
ls -ltr /backup/missecBackups/prodfsr-129
```

7.  Optional: Take manual backup before upgrade.

``` bash
csadm db --backup /backup/manual_backup --exclude-workflow
```

------------------------------------------------------------------------

# PHASE 2 -- Upgrade Secondary Node (172.16.209.120)

1.  SSH into Secondary node.
2.  Confirm node role:

``` bash
csadm ha list-nodes
```

3.  Start persistent session:

``` bash
tmux
```

4.  Suspend cluster:

``` bash
csadm ha suspend-cluster
```

5.  Download upgrade package:

``` bash
cd /
wget https://repo.fortisoar.fortinet.com/7.4.5/upgrade-fortisoar-7.4.5.bin
chmod +x upgrade-fortisoar-7.4.5.bin
```

6.  Execute upgrade (\~20 minutes):

``` bash
sh upgrade-fortisoar-7.4.5.bin
```

7.  Verify upgrade:

``` bash
csadm version
csadm ha list-nodes
```

------------------------------------------------------------------------

# PHASE 3 -- Upgrade Primary Node (Downtime Phase)

1.  SSH into Primary node (172.16.209.129).
2.  Confirm node role:

``` bash
csadm ha list-nodes
```

3.  Unmount NFS:

``` bash
umount /mnt/missecLockbox
```

4.  Comment NFS entry in `/etc/fstab`:

``` bash
vi /etc/fstab
#172.16.213.60 ...
```

5.  Start tmux session:

``` bash
tmux
```

6.  Download upgrade package:

``` bash
cd /
wget https://repo.fortisoar.fortinet.com/7.4.5/upgrade-fortisoar-7.4.5.bin
chmod +x upgrade-fortisoar-7.4.5.bin
```

7.  Execute upgrade (\~20 minutes downtime):

``` bash
sh upgrade-fortisoar-7.4.5.bin
```

------------------------------------------------------------------------

# PHASE 4 -- Resume Cluster

After both nodes are upgraded:

``` bash
csadm ha resume-cluster
csadm ha list-nodes
```

------------------------------------------------------------------------

# PHASE 5 -- Re-Mount NFS

``` bash
vi /etc/fstab
mount -a
systemctl restart autofs
cd /backup/missecBackups
ls
```

------------------------------------------------------------------------

# PHASE 6 -- Post-Upgrade Validation

-   Verify version: `csadm version`
-   Verify HA status: `csadm ha list-nodes`
-   Login to GUI and confirm access.
-   Test sample playbook execution.
-   Verify connectors (SSH, Email, DB).
-   Re-enable required schedules.

------------------------------------------------------------------------

# Rollback Plan

-   Power off both nodes.
-   Revert to VM snapshots.
-   Power on Primary first, then Secondary.
-   Verify HA synchronization.

------------------------------------------------------------------------

**End of Document**
