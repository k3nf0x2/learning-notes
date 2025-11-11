---
topic: BIND9 (Berkeley Internet Name Domain)
category: DNS, Linux Administration
tags: [bind9, dns-server, nameserver]
date: 2025-11-11
status: 🌱 seedling
---

# BIND9 - DNS Server Software

## 📝 Brief Summary

BIND9 is the most widely used DNS server software on the internet. It's free, open-source, and incredibly powerful. Think of it as the "Apache of DNS" - it's the standard that everything else is compared to.

---

## 🔍 Detailed Explanation

### What is BIND9?

**BIND** = **B**erkeley **I**nternet **N**ame **D**omain
**Version 9** = Current major version (BIND9)

**Developed by:** Internet Systems Consortium (ISC)
**License:** Mozilla Public License 2.0 (Free!)
**First Release:** 1980s (one of the oldest continuously used software!)

### DNS Server Types

#### 1. Authoritative DNS Server
**Role:** Has the official records for a domain
**Example:** Your BIND9 server for homelab.local
**Characteristics:**
- Stores zone files
- Answers with authority (AA flag)
- Primary (master) or Secondary (slave)

#### 2. Recursive DNS Server
**Role:** Performs full resolution for clients
**Example:** Your ISP's DNS, Google's 8.8.8.8
**Characteristics:**
- Queries other servers on behalf of clients
- Caches answers
- Does not host zones

#### 3. Forwarding DNS Server
**Role:** Passes queries to another DNS server
**Example:** Corporate DNS forwarding to ISP
**Characteristics:**
- Simple configuration
- Reduces external query load
- Useful for specific domain handling

### BIND9 Can Do All Three!

**Today we'll configure:**
1. **Authoritative** for homelab.local (our domain)
2. **Forwarding** for internet domains (to 8.8.8.8)
3. **Caching** for performance

---

## 💻 BIND9 Architecture

### Key Components

#### 1. named (The Daemon)
**Binary:** `/usr/sbin/named`
**Purpose:** The actual DNS server process
**Runs as:** User 'named'

#### 2. Configuration Files

**Main config:** `/etc/named.conf`
**Contains:**
- Global options
- Zone definitions
- Access controls
- Logging configuration

**Zone files:** `/var/named/`
**Contains:**
- Forward zone files (name → IP)
- Reverse zone files (IP → name)

#### 3. named-checkconf
**Purpose:** Validate named.conf syntax
**Usage:** `named-checkconf`

#### 4. named-checkzone
**Purpose:** Validate zone file syntax
**Usage:** `named-checkzone zonename zonefile`

#### 5. rndc (Remote Name Daemon Control)
**Purpose:** Control running named process
**Usage:**
```bash
rndc reload          # Reload configuration
rndc reload zonename # Reload specific zone
rndc flush           # Flush cache
rndc status          # Show status
```

---

## 📁 File Structure
```
/etc/
├── named.conf                 # Main configuration
├── named.conf.local          # Optional: local zones
├── named.conf.options        # Optional: options
└── rndc.key                  # Control key

/var/named/
├── data/                     # Runtime data
├── dynamic/                  # Dynamic zones
├── slaves/                   # Secondary zone files
├── named.ca                  # Root hints
├── homelab.local.zone        # Our forward zone
└── 192.168.20.rev            # Our reverse zone

/var/log/
└── named.log                 # DNS logs (if configured)
```

---

## 🔧 BIND9 Configuration Structure

### /etc/named.conf Sections

#### 1. Options Block
```
options {
    listen-on port 53 { localhost; 192.168.20.11; };
    directory "/var/named";
    allow-query { localhost; 192.168.20.0/24; };
    recursion yes;
    forwarders { 8.8.8.8; 8.8.4.4; };
};
```

#### 2. Zone Definitions
```
zone "homelab.local" IN {
    type master;
    file "homelab.local.zone";
    allow-update { none; };
};
```

#### 3. Logging (Optional)
```
logging {
    channel default_log {
        file "/var/log/named/named.log" versions 3 size 5m;
        severity info;
        print-time yes;
    };
    category default { default_log; };
};
```

---

## 🎯 Zone File Structure

### SOA Record (Start of Authority)
```
@   IN  SOA  ns1.homelab.local. admin.homelab.local. (
        2025111101  ; Serial (YYYYMMDDNN)
        3600        ; Refresh (1 hour)
        1800        ; Retry (30 minutes)
        604800      ; Expire (1 week)
        86400       ; Minimum TTL (1 day)
    )
```

**Fields Explained:**
- **Primary NS:** ns1.homelab.local. (this server)
- **Admin Email:** admin.homelab.local. (means admin@homelab.local)
  - Note: @ replaced with . in DNS
- **Serial:** Version number - MUST increment on changes!
- **Refresh:** How often secondary checks for updates
- **Retry:** If refresh fails, retry after this time
- **Expire:** If unreachable this long, secondary stops answering
- **Minimum TTL:** Default cache time for negative responses

---

## 🔒 Security Considerations

### 1. Access Control Lists (ACLs)
```
acl "trusted" {
    localhost;
    192.168.20.0/24;
};

options {
    allow-query { trusted; };
    allow-transfer { none; };  # Or specific secondaries
};
```

### 2. Recursion Control
```
# Allow recursion only for internal network
options {
    recursion yes;
    allow-recursion { trusted; };
    allow-query-cache { trusted; };
};
```

### 3. Version Hiding
```
options {
    version "DNS Server";  # Don't reveal BIND version
};
```

---

## ⚠️ Common Issues

### 1. Permission Errors
**Symptom:** "permission denied" in logs
**Cause:** Zone files not readable by 'named' user
**Fix:**
```bash
chown root:named /var/named/*.zone
chmod 640 /var/named/*.zone
```

### 2. Syntax Errors
**Symptom:** named won't start
**Fix:** Always check before restart:
```bash
named-checkconf
named-checkzone homelab.local /var/named/homelab.local.zone
```

### 3. Firewall Blocking
**Symptom:** Queries timeout
**Fix:**
```bash
firewall-cmd --permanent --add-service=dns
firewall-cmd --reload
```

### 4. SELinux Denials
**Symptom:** Permission issues despite correct file permissions
**Check:**
```bash
ausearch -m avc -ts recent
```
**Temporary fix:** `setenforce 0` (We'll fix properly later)

---

## 🔗 Related Concepts

- [[DNS-Fundamentals]] - Understanding DNS basics
- [[Zone-Files]] - Creating zone files
- [[DNS-Troubleshooting]] - Debugging DNS issues
- [[DNS-Security]] - DNSSEC implementation
- [[Secondary-DNS]] - Slave server setup

---

**Created:** 2025-11-11 12:30 PM