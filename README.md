# Penetration Testing Report: Footprinting & Reconnaissance
 
**Target:** `networkwalks.com`
**Phase:** Week 2 – Footprinting & Reconnaissance (W2-PM1) and Local Network Scanning (W2-PM5)
**Program:** Cybersecurity Internship (Ashrei Tech | NASTP)
**Environment:** Kali Linux (WSL) on Windows
 
---
 
## ⚠️ Liability Disclaimer
 
All activities were performed only on systems and devices where written permission was secured, or on systems I own. This material is for **education and research purposes only**. Do not use anything here to break the law. The instructor, the authors, and Networkwalks are not responsible for what you do with this knowledge. Every action you take is your own responsibility. Misuse can lead to criminal charges, heavy fines, loss of your job, and a permanent record. In most countries, unauthorized access is a crime even when nothing is damaged.
 
---
 
## 📌 Introduction
 
This report documents the reconnaissance and footprinting phases against the domain `networkwalks.com` using multiple Kali Linux tools (W2-PM1), followed by local network scanning with Zenmap (W2-PM5).
 
The goal is to move methodically from passive and active external reconnaissance (domain registration, infrastructure records, web technologies, WAF details) to mapping live hosts, IP addresses, and MAC addresses on a local subnet.
 
---
 
## 🛠️ Tools Used
 
| Tool | Purpose |
| --- | --- |
| Kali Linux & Windows | Operating systems used for reconnaissance, network discovery, and local enumeration |
| `whois` | Public domain registration details, registrant organization, registrar info, name servers |
| `whatweb` | Fingerprints web technologies, CMS versions, server software, exposed email/script headers |
| `nslookup` | Resolves domain names to IP addresses and queries name server mappings |
| `curl -I` | Inspects HTTP response headers, status codes, cookies, and endpoint links |
| `wafw00f` | Detects and identifies Web Application Firewall (WAF) protection |
| `dnsrecon` | Enumerates DNS records: SOA, NS, A, MX, TXT, SRV |
| Zenmap (Nmap GUI) | Identifies local subnets, live hosts, IP/MAC addresses, and topology maps |
 
---
 
## 🔍 Activities Performed
 
### 1. Domain Ownership & Registration (`whois`)
 
- **Registrar:** GoDaddy.com, LLC (IANA ID: 146)
- **Creation Date:** 2019-11-06 | **Expiry Date:** 2027-11-06
- **Registrant Organization:** Domains By Proxy, LLC (privacy protection enabled)
- **Name Servers:** `NS6135.HOSTGATOR.COM`, `NS6136.HOSTGATOR.COM`
- **Status Locks:** `clientDeleteProhibited`, `clientTransferProhibited`, `clientUpdateProhibited`, `clientRenewProhibited`
### 2. Web Technology Fingerprinting (`whatweb`)
 
- **Server:** Apache on `192.232.216.135` (United States)
- **CMS:** WordPress 7.1.4
- **Plugins & Libraries:** WordPress Download Manager 3.3.76, Bootstrap 7.1.4, jQuery 3.7.1
- **Exposed Contact:** `info@networkwalks.com`
- **Analytics:** Google Tag Manager
### 3. DNS Resolution (`nslookup`)
 
- **Target IP:** `192.232.216.135`
- **Name Server IPs:** `50.87.144.87` (ns6135.hostgator.com), `192.232.216.131` (ns6136.hostgator.com)
### 4. HTTP Header Inspection (`curl -I`)
 
- **Status:** `HTTP/2 200 OK`
- **Server Header:** Apache
- **Cookies & Caching:** `__wpdm_client` cookie set with HttpOnly and Secure flags; `x-nginx-cache: WordPress`, `x-endurance-cache-level: 0`
- **Exposed API Endpoint:** `https://networkwalks.com/wp-json/` (`rel="https://api.w.org/"`)
### 5. WAF Detection (`wafw00f`)
 
- The site is protected behind **ModSecurity (SpiderLabs)** WAF.
### 6. DNS Record Enumeration (`dnsrecon`)
 
- **SOA / NS:** HostGator name servers running BIND `9.16.23-RH`
- **A Records:** `192.232.216.135`, `50.87.144.87`, `192.232.216.131`
- **MX Records:** `mail.networkwalks.com` → `192.232.216.135`
- **TXT Records:** SPF configuration (`v=spf1 +a +mx +ip4:50.87.144.87 +include:websitewelcome.com ~all`) and a Google site verification record
- **SRV Records:** cPanel autodiscover email routing (`cpanelemaildiscovery.cpanel.net`)
### 7. Local Network Scanning (Zenmap) – W2-PM5
 
- Identified the local LAN subnet, active live hosts, IP addresses, MAC addresses, and the network topology map.
---
 
## 📊 Risk Analysis
 
| # | Finding | Evidence | Potential Impact | Risk |
| --- | --- | --- | --- | --- |
| 1 | CMS & plugin version exposure | WhatWeb identified WordPress and WordPress Download Manager 3.3.76 | Versions can be cross-referenced with public exploit databases (Exploit-DB / CVEs) to target known plugin vulnerabilities | 🟡 Medium |
| 2 | Server IP infrastructure leakage | `nslookup` and `dnsrecon` resolved multiple backend and mail server IPs | Direct IP exposure can sometimes help bypass edge protections if misconfigured | 🟢 Low |
| 3 | REST API & header enumeration | `curl` exposed the `/wp-json/` REST API link and custom cache headers | Reveals application routing paths and can facilitate user/content enumeration | 🟢 Low |
| 4 | WAF fingerprint visibility | `wafw00f` identified ModSecurity (SpiderLabs) | Tells attackers which defensive tool is in use so they can tailor evasion | 🟢 Low |
| 5 | DNS & SPF configuration disclosure | `dnsrecon` revealed mail servers, BIND version, and the full SPF include chain | Gives an adversary a clear map of email infrastructure and hosting partners | 🟡 Medium |
 
> **Note:** These are information-gathering results only. They do not represent active exploitation or confirmed vulnerabilities.
 
---
 
## ✅ Recommendations
 
1. **Conceal version footprints:** Disable version disclosure in WordPress headers, generator tags, and RSS feeds.
2. **Harden REST API access:** Restrict unnecessary public WordPress REST API endpoints (`/wp-json/wp/v2/`) if they are not needed by the frontend.
3. **Keep plugins & core updated:** Maintain a continuous patch cycle for WordPress core, themes, and plugins (especially WP Download Manager).
4. **Tune WAF rulesets:** Keep ModSecurity in blocking mode with up-to-date signatures to mitigate automated scanners.
5. **Review DNS exposure:** Audit public TXT and SRV records so only essential service endpoints are exposed.
---
 
## 🏁 Conclusion
 
Reconnaissance techniques were successfully performed against `networkwalks.com` using WHOIS, WhatWeb, Nslookup, Curl, Wafw00f, and DNSRecon. Key details about the target's DNS structure, server software, hosting provider, and security controls were gathered without triggering disruptive alarms. This shows how much intelligence can be collected, passively and actively, before any formal security assessment begins.
 
---
 
## 📎 Evidence
 
Screenshots and tool output are available in the full report: [`PENETRATION REPORT.docx`](https://github.com/aleerbaloch/Footprinting-Reconnaissance-and-Network-Discovery/blob/main/PENETRATION%20REPORT.docx)
 
---
 
## 📜 License / Usage
 
For educational purposes only. Use these techniques only on systems you own or have explicit written permission to test.
