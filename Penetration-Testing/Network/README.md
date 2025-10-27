# Network Penetration Testing

# External Network Penetration Test — Project 

## Objective
Perform an **external network penetration test** against a public IP address to identify exposed services, routing visibility, and vulnerabilities that could be exploited by remote attackers. The goal was to assess the target's external attack surface and produce prioritized findings for remediation.

> **Authorization:** All testing was performed with explicit permission from the asset owner. Do **not** run these steps against systems you are not authorized to test.



## Tools used
- Whois lookup (domain/IP registration details)  
- BGP Electric Toolkit (prefix/ASN and routing visibility)  
- **Nmap** — discovery, port/service enumeration, version/OS fingerprinting, NSE scripts  
- **Nessus** — vulnerability assessment (unauthenticated and authenticated where credentials available)

---

## Steps taken
1. **Passive reconnaissance**
   - Performed WHOIS on the target IP and related domains to collect ownership/contact and registrar data.
   - Used BGP Electric Toolkit to enumerate announced prefixes, upstream providers, and ASN relationships to understand routing exposure and hijack surface.
   - Gathered public footprint (DNS records, reverse DNS, SSL cert transparency) to build an external asset inventory without touching the target.

2. **Active reconnaissance (external)**
   - Verified reachability (ICMP/traceroute) to understand network path and intermediate filtering.
   - Ran Nmap discovery scans from an external vantage to identify open ports and services:
     - TCP SYN scans, full/targeted port scans as permitted.
     - Service/version detection (`-sV`) and OS fingerprinting (`-O`) where allowed.
     - Safe NSE scripts for information gathering (e.g., `ssl-cert`, `http-title`, `banner`).
   - Example (sanitized) Nmap commands used:
     ```bash
     nmap -Pn -sS -p- -oA external-discovery <target-ip>
     nmap -sV -O --script "safe" -oA external-recon <target-ip>
     ```

3. **Vulnerability assessment**
   - Performed Nessus scans from an external perspective (unauthenticated), and authenticated scans where credentials were provided, to identify known CVEs, misconfigurations, and weak services.
   - Tuned Nessus policies to the external context (reduce noise, limit intrusive checks).
   - Correlated Nmap results with Nessus findings to validate exploitability of exposed services.

4. **Analysis & validation**
   - Triaged findings by severity and likelihood (validated false positives, confirmed reachable/exploitable services).
   - Identified high-risk exposures (public admin interfaces, outdated services, weak TLS).
   - Drafted remediation guidance and prioritized actions for the external attack surface.

---

## Outcome / What I learned
- **Outcome:** Completed an external assessment that enumerated public-facing services, routing/prefix visibility, and produced a prioritized set of vulnerabilities and configuration issues for remediation.
- **Key lessons & recommendations:**
  - **External visibility is critical:** BGP/prefix data can reveal more exposed infrastructure than expected—monitor announced prefixes and upstream changes.
  - **Reduce attack surface:** Restrict or firewall unnecessary public ports and management interfaces; prefer VPN or IP allowlists for admin access.
  - **Patch and configuration management:** Prioritize patching for public-facing services and remediate high-severity Nessus findings.
  - **Harden TLS and protocols:** Disable weak ciphers/protocols and ensure valid certificates for public services.
  - **Asset inventory:** Maintain an accurate external asset inventory and map it to ownership and monitoring.
  - **Continuous external monitoring:** Implement routing and certificate monitoring for anomalous changes (BGP, CT logs).
  - **Safe scanning practice:** Conduct scans within agreed windows, throttle scans to avoid disruption, and adhere to rules of engagement.

---

## Next steps / Recommendations for follow-up
- Schedule authenticated scans and internal network testing for deeper coverage.
- Implement continuous vulnerability scanning and BGP monitoring.
- Run remediation verification (re-scan) after fixes are applied.


> **Note:** This README is a sanitized summary for project/portfolio use. Do not include sensitive evidence (raw logs, IPs, credentials) in public repositories. Always operate under explicit authorization and agreed rules of engagement.

