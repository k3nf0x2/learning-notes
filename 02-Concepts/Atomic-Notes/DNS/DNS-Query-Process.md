# 🔄 DNS Query Resolution Process

## Query Types

### 1. Recursive Query
**Client → Resolver:** "Give me the answer or an error"
- Resolver does all the work
- Most common for end users

### 2. Iterative Query
**Resolver → Nameserver:** "Give me the answer or a referral"
- Each server returns best answer it has
- Used between DNS servers

### 3. Non-Recursive Query
- Answer from cache
- No external queries needed

---

## Resolution Process (Step-by-Step)

**Scenario:** Client queries www.example.com
```
Step 1: Client checks local cache
├─ Hit → Return answer ✅
└─ Miss → Continue to Step 2

Step 2: Query Recursive Resolver (e.g., 8.8.8.8)
├─ Check cache
│  ├─ Hit → Return answer ✅
│  └─ Miss → Continue to Step 3
│
Step 3: Query Root Server (.)
├─ Response: "Ask .com TLD server"
└─ Returns: a.gtld-servers.net (TLD NS)

Step 4: Query TLD Server (.com)
├─ Response: "Ask example.com authoritative server"
└─ Returns: ns1.example.com (Authoritative NS)

Step 5: Query Authoritative Server
├─ Response: "www.example.com = 192.0.2.1"
└─ Returns: A record

Step 6: Resolver caches result
└─ Returns answer to client

Step 7: Client caches result
└─ Uses IP to connect
```

---

## Detailed Example with Commands
```bash
# Full trace query
dig www.google.com +trace

# Output breakdown:
; <<>> DiG 9.16.23 <<>> www.google.com +trace
;; global options: +cmd
. 518400 IN NS a.root-servers.net.
# ↑ Root servers

com. 172800 IN NS a.gtld-servers.net.
# ↑ TLD servers for .com

google.com. 172800 IN NS ns1.google.com.
# ↑ Authoritative nameservers

www.google.com. 300 IN A 142.250.185.36
# ↑ Final answer
```

---

## Caching & TTL

### Time To Live (TTL)
- Specifies how long to cache a record
- Lower TTL = More queries, but faster updates
- Higher TTL = Fewer queries, but slower updates

**Example:**
```
www.example.com. 300 IN A 192.0.2.1
                  ↑
                TTL (5 minutes)
```

### Cache Hierarchy
```
Browser Cache (short, ~1 min)
    ↓
OS Cache (longer, ~few mins)
    ↓
Resolver Cache (configurable, ~hours)
    ↓
Authoritative Server (source of truth)
```

---

## DNS Query Flags
```bash
dig example.com

# Header flags:
;; flags: qr rd ra
```

**Flags explained:**
- **qr** (Query Response): This is a response
- **rd** (Recursion Desired): Client wants recursion
- **ra** (Recursion Available): Server supports recursion
- **aa** (Authoritative Answer): Answer from authoritative source
- **tc** (Truncated): Response was truncated (use TCP)

---

## Interview Points

**Q: Explain recursive vs iterative queries**
**A:** "Recursive query means the resolver does all the work and returns 
the final answer. Iterative means each server returns either the answer 
or a referral to the next server. Clients typically use recursive, while 
DNS servers use iterative when resolving."

**Q: What happens if DNS fails?**
**A:** "If the local resolver fails, the client cannot resolve domains. 
Browsers may use DNS-over-HTTPS as fallback. If authoritative servers 
fail, cached records are still served until TTL expires. This is why 
having multiple nameservers (NS records) is crucial."

**Q: How does DNS caching improve performance?**
**A:** "Caching reduces query load on authoritative servers and decreases 
response time. For example, if google.com is cached with a 300-second 
TTL, subsequent queries within 5 minutes return instantly from cache 
instead of traversing the entire DNS hierarchy."