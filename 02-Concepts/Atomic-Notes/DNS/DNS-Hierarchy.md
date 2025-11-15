# 🌳 DNS Hierarchy

## Structure Levels

### 1. Root Level (.)
- 13 root server clusters (a-m.root-servers.net)
- Managed by different organizations
- Know where TLD servers are
- Rarely queried directly (cached)

### 2. Top-Level Domains (TLDs)
**Generic TLDs:** .com, .org, .net, .edu
**Country Code TLDs:** .uk, .jp, .de, .us
**New gTLDs:** .tech, .ai, .cloud

### 3. Second-Level Domains
- Registered domains: google.com, example.org
- Owned by organizations/individuals
- Can have own nameservers

### 4. Subdomains
- Created by domain owner
- mail.example.com, www.example.com
- Can delegate to different nameservers

## Authority & Delegation

**Authoritative Nameserver:** Has the actual zone data
**Recursive Resolver:** Queries on behalf of clients
**Forwarder:** Passes queries to another resolver

### Example Query Path:
```
Client → Recursive Resolver → Root → TLD → Authoritative → Answer
```

## Interview Point
"DNS is hierarchical and distributed, allowing delegation of authority 
and preventing any single point of failure. Each level only needs to 
know about the next level down, making the system scalable."