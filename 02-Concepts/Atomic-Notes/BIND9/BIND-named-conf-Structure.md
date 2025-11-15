# BIND named.conf Structure

## Configuration Sections

### 1. options { }
Global server options

### 2. logging { }
Logging configuration

### 3. zone "." { }
Root hints zone

### 4. zone "localhost" { }
Localhost forward zone

### 5. zone "0.0.127.in-addr.arpa" { }
Localhost reverse zone

### 6. Custom zones
Your domain zones (forward & reverse)

## Key Directives

**listen-on:** Which IPs to listen on
**allow-query:** Who can query this server
**allow-transfer:** Who can transfer zones (AXFR)
**forwarders:** Upstream DNS servers
**recursion:** Allow recursive queries
**dnssec-validation:** DNSSEC validation