# FortiSOAR-Upgrade# FortiSOAR Upgrade Runbook  
**Upgrade Path:** 7.4.6 → 7.5.0 (OS Migration) → 7.6.5  
**Environment:** Standalone (Staging)  
**Type:** In-Place Upgrade  

---

# 1. Overview

This document provides a structured and validated upgrade procedure for upgrading FortiSOAR from **7.4.6 to 7.6.5** using the required intermediate version **7.5.0** (which includes OS migration from Rocky Linux 8 to Rocky Linux 9).

---

# 2. Pre-Upgrade Checklist (Mandatory)

Before starting:

- [ ] Take full VM snapshot
- [ ] Stop all scheduled playbooks
- [ ] Confirm no active workflows are running
- [ ] Export built-in connectors
- [ ] Verify internet/repo connectivity
- [ ] Confirm sufficient disk space
- [ ] Unmount external NFS mounts (if any)

---

# 3. Phase 1 — Upgrade 7.4.6 → 7.5.0  
⚠ Includes OS Migration (Rocky Linux 8 → 9)

---

## 3.1 Download Upgrade Package

```bash
cd /
wget https://repo.fortisoar.fortinet.com/7.5.0/fortisoar-inplace-upgrade-7.5.0.bin
chmod +x fortisoar-inplace-upgrade-7.5.0.bin
```

---

## 3.2 Execute Upgrade

```bash
sh fortisoar-inplace-upgrade-7.5.0.bin
```

---

## 3.3 Reboot & OS Migration Phase

After execution, system will prompt for reboot.

During reboot:

- OS migrates from Rocky Linux 8 → Rocky Linux 9
- Migration takes approximately 15–20 minutes
- FortiSOAR 7.5.0 installs automatically
- Configuration restores automatically

⚠ Do NOT interrupt the process.

---

## 3.4 Monitor Upgrade Progress

After SSH access is restored:

```bash
tail -f /var/log/leapp/leapp-upgrade.log
```

Additional logs:

```
/var/log/leapp/leapp-preupgrade.log
/var/log/leapp/leapp-report.json
/var/log/leapp/leapp-report.txt
```

⚠ Do NOT execute other CLI commands while upgrade is running.

---

## 3.5 Post-Upgrade Verification (7.5.0)

```bash
cat /etc/redhat-release
uname -r
rpm -qi cyops | grep Version
csadm services --status
```

Expected:

- Rocky Linux 9.x
- Kernel 5.14.x.el9
- FortiSOAR 7.5.0
- All services running

---

# 4. Stabilization Validation (After 7.5.0)

- [ ] GUI login successful
- [ ] Playbooks accessible
- [ ] Manual workflow test successful
- [ ] No HTTP 500 errors
- [ ] All services healthy

---

# 5. Phase 2 — Upgrade 7.5.0 → 7.6.5

---

## 5.1 Readiness Check

```bash
csadm upgrade check-readiness --version 7.6.5
```

Report location:

```
/opt/fsr-elevate/elevate/outputs/
```

All checks must pass before proceeding.

---

## 5.2 Execute Upgrade

```bash
csadm upgrade execute --target-version 7.6.5
```

---

## 5.3 Monitor Upgrade Logs

Primary log:

```bash
tail -f /var/log/cyops/upgrade-fortisoar-7.6.5-<timestamp>.log
```

Elevate framework log:

```bash
tail -f /var/log/cyops/fsr-elevate/fsr-elevate-7.6.5-<timestamp>.log
```

---

# 6. Final Validation (7.6.5)

```bash
rpm -qi cyops | grep Version
csadm services --status
```

Confirm:

- FortiSOAR 7.6.5
- All services running
- GUI accessible
- Playbooks execute successfully
- Background jobs functioning normally

---

# 7. Rollback Procedure

If failure occurs:

1. Power off VM
2. Revert to pre-upgrade snapshot
3. Boot system
4. Validate original 7.4.6 state

---

# 8. Activity Tracking Table

| Step | Date | Engineer | Status | Notes |
|------|------|----------|--------|-------|
| Snapshot Taken | | | | |
| 7.5.0 Upgrade | | | | |
| OS Verified | | | | |
| 7.6.5 Readiness | | | | |
| 7.6.5 Upgrade | | | | |
| Final Validation | | | | |

---

# 9. Log Reference Summary

| Phase | Log Location |
|-------|--------------|
| Pre-Upgrade | /var/log/leapp/leapp-preupgrade.log |
| OS Migration | /var/log/leapp/leapp-upgrade.log |
| 7.6.5 Upgrade | /var/log/cyops/upgrade-fortisoar-7.6.5-<timestamp>.log |
| Elevate Framework | /var/log/cyops/fsr-elevate/fsr-elevate-7.6.5-<timestamp>.log |

---

# Upgrade Completion

Document completion date and validation results before closing the change request.
