# 📋 DNS Record Types - Complete Reference

## Essential Record Types

### 1. A Record (Address)
**Purpose:** Maps hostname to IPv4 address
```
www.example.com. 3600 IN A 192.0.2.1
```

**Use Case:** Primary web server address

---

### 2. AAAA Record (IPv6 Address)
**Purpose:** Maps hostname to IPv6 address
```
www.example.com. 3600 IN AAAA 2001:db8::1
```

**Use Case:** IPv6 connectivity

---

### 3. CNAME Record (Canonical Name)
**Purpose:** Alias one name to another
```
www.example.com.  3600 IN CNAME webserver.example.com.
webserver.example.com. 3600 IN A 192.0.2.1
```

**Rules:**
- Cannot coexist with other records for same name
- Cannot be used for zone apex (example.com)
- Can chain (but shouldn't exceed 2-3 levels)

**Use Case:** Multiple names for same service

---

### 4. MX Record (Mail Exchange)
**Purpose:** Specifies mail servers
```
example.com. 3600 IN MX 10 mail1.example.com.
example.com. 3600 IN MX 20 mail2.example.com.
```

**Priority:** Lower number = higher priority

**Use Case:** Email routing

---

### 5. NS Record (Nameserver)
**Purpose:** Delegates zone to nameservers
```
example.com. 3600 IN NS ns1.example.com.
example.com. 3600 IN NS ns2.example.com.
```

**Use Case:** DNS delegation

---

### 6. PTR Record (Pointer)
**Purpose:** Reverse DNS lookup (IP → hostname)
```
1.2.0.192.in-addr.arpa. 3600 IN PTR www.example.com.
```

**Use Case:** Email validation, logging

---

### 7. SOA Record (Start of Authority)
**Purpose:** Defines zone parameters
```
example.com. 3600 IN SOA ns1.example.com. admin.example.com. (
    2025111401 ; Serial (YYYYMMDDNN)
    3600       ; Refresh (1 hour)
    1800       ; Retry (30 mins)
    604800     ; Expire (1 week)
    86400      ; Minimum TTL (1 day)
)
```

**Parameters Explained:**
- **Serial:** Version number (must increment with changes)
- **Refresh:** How often secondary checks for updates
- **Retry:** Wait time if refresh fails
- **Expire:** When secondary stops serving if no updates
- **Minimum TTL:** Default TTL for negative responses

---

### 8. TXT Record (Text)
**Purpose:** Arbitrary text data
```
example.com. 3600 IN TXT "v=spf1 mx -all"
```

**Use Cases:**
- SPF records (email authentication)
- DKIM keys
- Domain verification
- Site ownership proof

---

### 9. SRV Record (Service)
**Purpose:** Specifies service locations
```
_service._proto.example.com. 3600 IN SRV 10 60 5060 server.example.com.
                                           ↓   ↓   ↓
                                       Priority Weight Port
```

**Use Case:** Service discovery (LDAP, SIP, XMPP)

---

### 10. CAA Record (Certification Authority Authorization)
**Purpose:** Specifies which CAs can issue certificates
```
example.com. 3600 IN CAA 0 issue "letsencrypt.org"
```

**Use Case:** SSL/TLS certificate security

---

## Record Comparison Table

| Record | Purpose | Example | Can Coexist |
|--------|---------|---------|-------------|
| A | IPv4 address | 192.0.2.1 | Yes (multiple A) |
| AAAA | IPv6 address | 2001:db8::1 | Yes |
| CNAME | Alias | webserver.example.com | No (exclusive) |
| MX | Mail routing | mail.example.com | Yes (multiple MX) |
| NS | Nameserver | ns1.example.com | Yes (multiple NS) |
| PTR | Reverse lookup | www.example.com | One per IP |
| SOA | Zone authority | Complex | One per zone |
| TXT | Text data | "text" | Yes (multiple) |
| SRV | Service location | server:port | Yes |

---

## Common Interview Questions

**Q: When would you use CNAME vs A record?**
**A:** Use A record when you have a direct IP address. Use CNAME when 
you want to alias to another hostname that might change IPs. CNAMEs 
provide flexibility but add an extra DNS lookup.

**Q: Why can't CNAME be at the zone apex?**
**A:** The zone apex (example.com) must have SOA and NS records. Since 
CNAME cannot coexist with other record types, it would break these 
required records.

**Q: Explain MX record priority**
**A:** Lower priority number means higher preference. Mail servers try 
the lowest priority first. Same priority = load balancing.

---

## Lab Exercise

Create records for a company:
```
example.com - Points to web server (192.0.2.1)
www.example.com - Alias to example.com
mail.example.com - Mail server (192.0.2.10)
ftp.example.com - FTP server (192.0.2.20)
Set up MX records with primary and backup mail servers
```

[Practice this in your lab]