# BIND9 Installation Log

**Date:** 2025-11-14
**Server:** web.homelab.local (192.168.20.11)
**OS:** AlmaLinux 9.x

## Commands Executed
```bash
sudo dnf update -y
sudo dnf install -y bind bind-utils
```

## Packages Installed
- bind-9.16.23-1.el9
- bind-libs-9.16.23-1.el9
- bind-utils-9.16.23-1.el9

## Verification
```bash
named -v
# Output: BIND 9.16.23 (Extended Support Version)

systemctl status named
# Output: Loaded: loaded (/usr/lib/systemd/system/named.service; disabled)
#         Active: inactive (dead)
```

## Next Steps
- Configure /etc/named.conf
- Create zone files
- Start named service

**Status:** ✅ Installation Complete