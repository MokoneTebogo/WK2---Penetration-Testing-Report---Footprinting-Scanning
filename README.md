# 🛡️ Penetration Testing Report: Footprinting & Network Scanning Phases

**Module:** W2-PM-FINAL | **Program:** Cybersecurity Internship at Networkwalks

---

## 📋 Engagement Metadata

| Field | Details |
| :--- | :--- |
| **Pentester Name** | Tebogo Mokone (Cybersecurity Professional) |
| **Program / Batch** | B084-Networkwalks |
| **Date** | 13 November 2026 |
| **Modules Completed** | • `W2-PM1` (Multiple Kali Tools)<br>• `W2-PM5` (Zenmap Scanning) |
| **Client / Target** | 1. `networkwalks.com` *(secured written permission)*<br>2. Local LAN Virtual Network (`10.0.0.0/24`) |
| **Authorization Status** | ✅ Yes (Written permission secured) |
| **Phases Covered** | • **Phase 1:** Reconnaissance & Footprinting<br>• **Phase 2:** Scanning & Network Discovery<br>• **Phases 3–5:** In Progress |

---

## ⚠️ 1. Liability Disclaimer

> I have performed these activities only on the systems & devices where I had secured written permission or the devices/systems that I own myself. All these materials are for education and research purposes only. Do not use anything from here to break the law. The instructor, the authors, and Networkwalks are not responsible for what you do with this knowledge. Every action you take is your own responsibility. Misuse can lead to criminal charges, heavy fines, loss of your job, and a permanent record. In most countries, unauthorized access is a crime even when nothing is damaged.

---

## 📖 2. Introduction

This repository documents the Week 2 practical deliverables for the **Networkwalks Cybersecurity Internship**, covering footprinting of the `networkwalks.com` domain using multiple Kali Linux tools (`W2-PM1`) and local subnet discovery using Zenmap (`W2-PM5`). 

Together, these modules demonstrate how a security professional transitions from passive and active public intelligence gathering to mapping live hosts on an internal network. All commands were executed inside a Kali Linux VirtualBox environment.

---

## 🛠️ 3. Tools Used

| Tool | Purpose |
| :--- | :--- |
| **Kali Linux (VirtualBox)** | Primary operating environment used for footprinting and scanning activities |
| **`whois`** | Retrieve domain registration details (registrar, creation/expiry dates, name servers) |
| **`whatweb`** | Fingerprint web technologies (web server, CMS, plugins, frameworks, IP) |
| **`nslookup`** | Resolve the target domain name to its IPv4 address using DNS |
| **`curl -I`** | Inspect HTTP response headers and server configurations |
| **`wafw00f`** | Detect whether a Web Application Firewall (WAF) protects the target site |
| **`dnsrecon`** | Enumerate DNS records (`SOA`, `NS`, `MX`, `A`, and `SRV` records) |
| **Zenmap (Nmap GUI)** | Scan the local subnet (`10.0.0.0/24`) to identify live hosts, IP/MAC addresses, and network topology |

---

## 🔍 4. Activities Performed

### 4.1 Footprinting & Reconnaissance (`networkwalks.com`)

Six Kali Linux tools were executed against `networkwalks.com` to collect layered infrastructure and web application intelligence:

* **WHOIS (`whois networkwalks.com`)**
  * **Registrar:** GoDaddy.com, LLC (IANA ID: `146`)
  * **Dates:** Created `2019-11-06` | Expires `2027-11-06`
  * **DNSSEC Status:** Unsigned
  * **Name Servers:** `NS6135.HOSTGATOR.COM`, `NS6136.HOSTGATOR.COM`

* **WhatWeb (`whatweb networkwalks.com`)**
  * **Web Server & Stack:** Apache, Bootstrap `7.1.4`, jQuery `3.7.1`
  * **CMS & Plugins:** WordPress `7.1.4`, WordPress Download Manager `3.3.76`
  * **Contact & Host:** `info@networkwalks.com` | IP `192.232.216.135`

* **Nslookup (`nslookup networkwalks.com`)**
  * Queried Google Public DNS (`8.8.8.8`) and resolved `networkwalks.com` to IPv4 address `192.232.216.135`.

* **Curl (`curl -I https://networkwalks.com`)**
  * Inspected `HTTP/1.1 200 OK` response headers, confirming the `Apache` server banner, `__wpdm_client` cookie configured with `Secure` and `HttpOnly` flags, `X-nginx-cache: WordPress`, and the exposed WordPress REST API endpoint (`/wp-json/`).

* **Wafw00f (`wafw00f networkwalks.com`)**
  * Confirmed that `https://networkwalks.com` is protected behind a **ModSecurity (SpiderLabs)** Web Application Firewall.

* **DNSRecon (`dnsrecon -d networkwalks.com`)**
  * Enumerated `SOA`, `NS`, `A`, `MX` (`mail.networkwalks.com` at `192.232.216.135`), and 8 `SRV` autodiscover records pointing to `cpanelemaildiscovery.cpanel.net`.
  * Noted recursion enabled on the name servers and no DNSSEC record present.

---

### 4.2 Network Scanning with Zenmap (`10.0.0.0/24`)

Host discovery was performed across the local virtual subnet (`10.0.0.0/24`) using Zenmap's **Ping Scan** profile (`nmap -sn 10.0.0.0/24`). The scan completed in **11.02 seconds** across 256 IP addresses and identified **4 active hosts**:

| Host IP Address | Status / Latency | MAC Address & Vendor |
| :--- | :--- | :--- |
| `10.0.0.1` | Host is up (`0.00050s` latency) | `52:54:00:12:35:00` (QEMU virtual NIC) |
| `10.0.0.2` | Host is up *(local scanning host)* | Local interface |
| `10.0.0.9` | Host is up (`0.00055s` latency) | `08:00:27:42:2A:00` (Oracle VirtualBox virtual NIC) |
| `10.0.0.10` | Host is up (`0.0039s` latency) | `08:00:27:6F:8D:8B` (Oracle VirtualBox virtual NIC) |

Following host discovery, the **Topology** tab in Zenmap was utilized to generate a radial network visualization mapping `localhost` to all discovered active hosts.

---

## 📊 5. Risk Analysis & Impact

| # | Risk / Finding | Evidence / Observation | Potential Impact | Risk Level |
| :---: | :--- | :--- | :--- | :---: |
| **1** | **Web technology versions exposed** | WhatWeb identified WordPress `7.1.4` and WP Download Manager `3.3.76` | Attackers may use exact software versions to check for known vulnerabilities | 🟠 **Medium** |
| **2** | **Origin server IP identifiable** | Nslookup and DNSRecon resolved domain to `192.232.216.135` | Reveals hosting location and potential direct target infrastructure | 🔵 **Low** |
| **3** | **HTTP headers & API exposed** | `curl -I` returned server banner and `/wp-json/` REST API link | Assists technology fingerprinting and user/endpoint enumeration | 🔵 **Low** |
| **4** | **WAF technology identifiable** | Wafw00f identified ModSecurity (SpiderLabs) WAF | Reveals defensive architecture and filtering ruleset type | 🔵 **Low** |
| **5** | **DNS configuration & recursion** | DNSRecon showed unsigned DNSSEC and recursion enabled on NS | Enables infrastructure mapping and potential DNS cache/amplification risks | 🟠 **Medium** |
| **6** | **Multiple live hosts on local LAN** | Zenmap Ping Scan discovered 4 live hosts on `10.0.0.0/24` | Unauthorized or rogue virtual hosts could operate undetected if unmonitored | 🟠 **Medium** |

> **Note:** Risk ratings reflect reconnaissance observations rather than confirmed exploitable vulnerabilities, as no active exploitation was conducted.

---

## 🛡️ 6. Security Recommendations

1. **Suppress Public Version Disclosures:** Remove version numbers from HTML meta generator tags and HTTP headers for WordPress core and installed plugins.
2. **Maintain Regular Patch Management:** Keep WordPress core, themes, and third-party plugins (such as WP Download Manager) updated against current security advisories.
3. **Restrict Unnecessary API & Header Exposure:** Limit unauthenticated access to `/wp-json/` REST API endpoints where not required and minimize web server banner details.
4. **Harden DNS Infrastructure:** Disable open recursion on authoritative name servers, implement DNSSEC signing, and periodically audit public `SRV` and `MX` records.
5. **Tune & Monitor the WAF:** Keep ModSecurity (SpiderLabs) active and monitor blocked request logs to detect automated reconnaissance activity.
6. **Conduct Periodic Internal Network Audits:** Regularly scan internal subnets with Nmap/Zenmap to verify authorized MAC/IP mappings and investigate unknown hosts.

---

## 🏁 7. Conclusion

During Week 2 of the Cybersecurity & Ethical Hacking internship, practical exercises in footprinting and network scanning demonstrated how substantial structural, DNS, application, and host-level intelligence can be gathered prior to vulnerability assessment. Clear technical documentation and strict adherence to authorized testing boundaries remain foundational to professional penetration testing.

---

## 📸 8. Evidence Collected

> *Tip: Upload your screenshot files into an `evidence/` folder in your GitHub repository to render them directly below.*

* **Figure 1:** WHOIS domain registration and name server lookup (`evidence/whois.png`)
* **Figure 2:** WhatWeb web technology and CMS fingerprinting output (`evidence/whatweb.png`)
* **Figure 3:** Nslookup DNS resolution identifying `192.232.216.135` (`evidence/nslookup.png`)
* **Figure 4:** HTTP response header inspection via `curl -I` (`evidence/curl.png`)
* **Figure 5:** Wafw00f detection identifying ModSecurity WAF (`evidence/wafw00f.png`)
* **Figure 6:** DNSRecon enumeration of `SOA`, `NS`, `MX`, `A`, and `SRV` records (`evidence/dnsrecon.png`)
* **Figure 7:** Zenmap Ping Scan (`nmap -sn 10.0.0.0/24`) results (`evidence/zenmap-ping.png`)
* **Figure 8:** Zenmap radial network topology visualization (`evidence/zenmap-topology.png`)

---

## 👤 Author & Project Information

* **Author:** Tebogo Mokone
* **LinkedIn:** [linkedin.com/in/tebogo-mokone-ba2b35261](https://www.linkedin.com/in/tebogo-mokone-ba2b35261/)
* **Program:** Cybersecurity Internship at Networkwalks | **Week:** 02 | **Batch:** B084
