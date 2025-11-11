---
topic: DNS (Domain Name System)
category: Networking, Linux Administration
tags: [dns, networking, bind9, fundamentals]
date: 2025-11-11
status: 🌱 seedling
---

# DNS - Domain Name System

## 📝 Brief Summary (Explain to a 5-year-old)

DNS is like a phonebook for the internet. When you type "google.com" in your browser, your computer doesn't understand that. It needs a number (IP address) like 142.250.185.46. DNS is the system that looks up the name and tells your computer the number.

Think of it like this: You want to call your friend "Google" but you need their phone number. DNS is the phonebook that tells you Google's number is 142.250.185.46.

---

## 🔍 Detailed Explanation

### What Problem Does DNS Solve?

**Without DNS:**
- Users would need to remember: 142.250.185.46 (Google)
- Every IP change would break websites
- No human-friendly internet navigation

**With DNS:**
- Remember: google.com (easy!)
- IP changes? Just update DNS
- Human-readable names everywhere

### DNS Architecture

DNS is **hierarchical** and **distributed**:
```
                     Root (.)
                        |
        ┌───────────────┼───────────────┐
        |               |               |
       .com           .org            .net
        |               |               |
    ┌───┴───┐       ┌───┴───┐       
    |       |       |       |
  google  amazon  wikipedia
    |
   www
```

**Levels:**
1. **Root Level (.)** - 13 root servers worldwide
2. **Top-Level Domain (TLD)** - .com, .org, .net, .in
3. **Second-Level Domain** - google, amazon, wikipedia
4. **Subdomain** - www, mail, ftp

### How DNS Resolution Works

**Query Type 1: Recursive (What we use)**
```
User PC → Recursive Resolver → Root → TLD → Authoritative → Answer
  ↑                                                              |
  └──────────────────────────────────────────────────────────────┘
```

**Step-by-Step:**

1. **User types:** www.example.com
2. **Computer checks:**
   - Browser cache (is it there?)
   - OS cache (is it there?)
   - Hosts file (/etc/hosts)
3. **Not found? Query DNS resolver** (your ISP or 8.8.8.8)
4. **Resolver queries Root servers:** "Where's .com?"
5. **Root responds:** "Ask TLD servers for .com at X.X.X.X"
6. **Resolver queries TLD:** "Where's example.com?"
7. **TLD responds:** "Ask authoritative server at Y.Y.Y.Y"
8. **Resolver queries Authoritative:** "What's www.example.com?"
9. **Authoritative responds:** "It's 93.184.216.34"
10. **Resolver returns answer** to your computer
11. **Computer caches** the answer (TTL period)
12. **Browser connects** to 93.184.216.34

**Query Type 2: Iterative**
- Each DNS server returns best answer it knows
- Client follows references
- More work for client

---

## 💻 Practical Example

### Using dig to See DNS in Action
```bash
# Simple query
dig google.com

# Explanation of output:
# QUESTION SECTION - what we asked
# ANSWER SECTION - the IP address(es)
# Query time - how long it took
# SERVER - which DNS server answered

# Trace full resolution path
dig +trace google.com

# Query specific record type
dig google.com A      # IPv4 address
dig google.com AAAA   # IPv6 address
dig google.com MX     # Mail servers
dig google.com NS     # Name servers

# Query specific DNS server
dig @8.8.8.8 google.com

# Reverse DNS lookup
dig -x 8.8.8.8
```

### Using nslookup
```bash
# Basic query
nslookup google.com

# Interactive mode
nslookup
> server 8.8.8.8    # Change DNS server
> google.com        # Query
> exit

# Specific record type
nslookup -type=MX google.com
```

### Using host command
```bash
# Simple query
host google.com

# Detailed information
host -a google.com

# Query specific server
host google.com 8.8.8.8
```

---

## 🎯 Key DNS Record Types

### A Record (Address)
**Purpose:** Maps hostname to IPv4 address
**Format:** `www.example.com. IN A 93.184.216.34`
**TTL:** Time To Live (how long to cache)

### AAAA Record
**Purpose:** Maps hostname to IPv6 address
**Format:** `www.example.com. IN AAAA 2606:2800:220:1:248:1893:25c8:1946`

### CNAME (Canonical Name)
**Purpose:** Alias - points one name to another
**Format:** `www.example.com. IN CNAME example.com.`
**Use Case:** Multiple names for same server

### MX Record (Mail Exchange)
**Purpose:** Specifies mail servers for domain
**Format:** `example.com. IN MX 10 mail.example.com.`
**Priority:** Lower number = higher priority

### NS Record (Name Server)
**Purpose:** Specifies authoritative DNS servers
**Format:** `example.com. IN NS ns1.example.com.`

### PTR Record (Pointer)
**Purpose:** Reverse DNS - IP to hostname
**Format:** `34.216.184.93.in-addr.arpa. IN PTR www.example.com.`
**Use:** Email servers often check this

### TXT Record
**Purpose:** Arbitrary text data
**Uses:** 
- SPF (email validation)
- DKIM (email signing)
- Domain verification
**Format:** `example.com. IN TXT "v=spf1 include:_spf.google.com ~all"`

### SOA Record (Start of Authority)
**Purpose:** Zone information
**Contains:**
- Primary name server
- Admin email
- Serial number (version)
- Refresh/Retry/Expire timers

### SRV Record (Service)
**Purpose:** Specifies location of services
**Format:** `_service._proto.name. TTL IN SRV priority weight port target.`
**Use:** Active Directory, SIP, XMPP

---

## 🎯 Use Cases

### When to use DNS:
- **Always!** Every internet connection uses it
- Custom internal domain (homelab.local)
- Load balancing (multiple A records)
- Failover (changing DNS quickly)
- Service discovery (SRV records)

### When NOT to use DNS:
- Time-critical applications requiring instant updates
- When /etc/hosts is simpler (few machines)
- Security-critical verification (DNS can be spoofed)

---

## ⚠️ Common Mistakes

### 1. Forgetting the Trailing Dot

**Mistake:**
```
www.example.com IN A 1.2.3.4
```

**Why it's wrong:**
Without trailing dot, DNS appends zone name:
`www.example.com.example.com.`

**Correct:**
```
www.example.com. IN A 1.2.3.4
```

### 2. Incorrect Serial Number Format

**Mistake:** Not updating serial after zone changes

**Correct:** Use format YYYYMMDDNN
- 2025111101 (Nov 11, 2025, version 01)
- Increment for each change

### 3. Mixing CNAME with Other Records

**Mistake:**
```
www IN CNAME example.com.
www IN A 1.2.3.4          # CONFLICT!
```

**Rule:** CNAME must be alone

### 4. TTL Too High or Too Low

**Too High (86400):** Changes take 24 hours to propagate
**Too Low (60):** Increases DNS query load
**Sweet Spot:** 3600-14400 (1-4 hours) for most cases

---

## 🔗 Related Concepts

- [[Network-Basics]] - Understanding IP addresses
- [[TCP-vs-UDP]] - DNS uses UDP port 53
- [[BIND9-Configuration]] - Implementing DNS
- [[DNS-Security]] - DNSSEC, DNS-over-HTTPS
- [[Troubleshooting-DNS]] - Common issues

---

## 📚 Resources

**Official:**
- RFC 1035: Domain Names - Implementation and Specification
- BIND9 Documentation: https://bind9.readthedocs.io

**Learning:**
- TLDP DNS HOWTO: https://tldp.org/HOWTO/DNS-HOWTO.html
- DigitalOcean DNS Tutorial
- DNS Made Easy blog

**Tools:**
- dig, nslookup, host commands
- DNS Lookup websites: dnschecker.org
- WHOIS lookup tools

---

## 🧪 Lab Exercise

**Objective:** Understand DNS resolution process

**Steps:**
1. Clear local DNS cache:
```bash
   # Linux
   sudo systemd-resolve --flush-caches
   # Or
   sudo systemctl restart systemd-resolved
```

2. Query a domain with trace:
```bash
   dig +trace google.com
```

3. Analyze each step in the output

4. Query different record types:
```bash
   dig google.com A
   dig google.com AAAA
   dig google.com MX
   dig google.com NS
   dig google.com TXT
```

5. Perform reverse lookup:
```bash
   dig -x 8.8.8.8
```

**Expected Result:** 
Understanding of complete DNS resolution path

**My Result:** 
[Test this now and document your findings]

---

## ❓ Questions to Explore

- [ ] How does DNS caching work at different levels?
- [ ] What is DNS poisoning/spoofing?
- [ ] How does DNSSEC provide security?
- [ ] What is the difference between authoritative and non-authoritative answers?
- [ ] How do round-robin DNS and load balancing work?

---

## 🎤 Explain It Out Loud

**Record yourself explaining:**

"DNS is a hierarchical, distributed system that translates human-readable domain names into IP addresses. When I type google.com, my computer first checks its cache. If not found, it queries a recursive resolver, which systematically queries root servers, TLD servers, and finally authoritative name servers to find the IP address. The answer is then cached with a TTL value to reduce future query load. This system enables the internet to use memorable names instead of numeric IP addresses."

**Practice until you can explain in under 2 minutes without notes.**

---

**Created:** 2025-11-11 12:15 PM
**Last Modified:** 2025-11-11 12:45 PM
**Status:** 🌿 growing → 🌳 mature (after practical implementation)