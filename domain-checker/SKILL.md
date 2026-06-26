---
name: domain-checker
description: Check domain availability by resolving DNS records. Returns "available" if no DNS record exists, "taken" if DNS resolves. Works on Windows PowerShell.
---

# domain-checker

Check if a domain is available by attempting DNS resolution. If DNS returns no result, the domain is available (not registered). If DNS resolves, the domain is taken.

## Usage

```powershell
# Check a single domain
Resolve-DnsName example.com -ErrorAction SilentlyContinue

# Check multiple domains in batch
foreach ($domain in @("example.dev","example.io","example.ai")) {
  $r = Resolve-DnsName $domain -ErrorAction SilentlyContinue
  if ($r) { "$domain - TAKEN" } else { "$domain - available" }
}
```

## How it works

- `Resolve-DnsName` queries DNS for the domain
- `-ErrorAction SilentlyContinue` suppresses errors for non-existent domains
- If `$r` has a value → DNS resolved → domain is taken
- If `$r` is null → no DNS record → domain is available

## Notes

- This checks DNS, not WHOIS. A domain might be registered but not have DNS records yet.
- Works on Windows PowerShell 5.1+ (standard on Windows 10/11)
- For Linux/macros, use `dig` or `nslookup` instead
- Batch checking is fast — you can check 50+ domains in seconds
