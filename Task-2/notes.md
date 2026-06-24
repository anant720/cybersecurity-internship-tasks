# Task 2 — Research Notes

## Build Your Personal Cybersecurity Lab

**Intern:** Anant Suthar | **ID:** SG2210  
**Domain:** Cyber Security & Ethical Hacking  
**Duration:** 05 June 2026 – 05 July 2026 | **Organization:** Sumerix Global  

Quick notes on the lab build process, key decisions, what I configured, and what I took away from this task.

---

## What This Task Required

Build a fully isolated, working cybersecurity lab on a personal machine using virtualization — covering:
- 1 attacker machine (Kali Linux 2025.4)
- 1 vulnerable target application (OWASP Juice Shop via Docker)
- Proper network segmentation (NAT + Host-Only)
- Connectivity validation and basic tool usage (Nmap, Burp Suite, Wireshark)

---

## Lab Architecture — My Setup

```
Host Machine (Windows)
│
├── VirtualBox
│   └── Kali Linux 2025.4 (Attacker VM)
│       ├── Adapter 1 → NAT          (10.0.2.15/24)       ← internet/updates
│       ├── Adapter 2 → Host-Only    (192.168.56.101/24)  ← isolated lab traffic
│       └── docker0 bridge           (172.17.0.1)         ← container networking
│
└── Docker (inside Kali)
    └── OWASP Juice Shop → localhost:3000
```

**Networks:**
- **NAT** — lets Kali reach the internet for `apt` updates and Docker pulls. Isolated from the target traffic.
- **Host-Only (192.168.56.0/24)** — completely isolated subnet for lab attack traffic. Attack traffic never touches the real LAN.

---

## What I Actually Did (Step by Step)

### 1. Host-Only Network Configuration
Set up a VirtualBox Host-Only adapter at `192.168.56.1/24` with DHCP enabled. This creates the isolated segment the attacker VM talks on.

### 2. Kali VM — Dual Adapter Setup
- **Adapter 1** → NAT (internet)
- **Adapter 2** → Host-Only Ethernet Adapter (lab segment)

Confirmed with `ip a` inside Kali:
```
eth0: 10.0.2.15/24     ← NAT
eth1: 192.168.56.101/24 ← Host-Only
docker0: 172.17.0.1    ← Docker bridge
```

### 3. Target Deployment — OWASP Juice Shop
Deployed inside Kali using Docker. Commands used:
```bash
sudo apt install -y docker.io
sudo systemctl enable --now docker
sudo docker pull bkimminich/juice-shop
sudo docker run -d -p 3000:3000 --name juice bkimminich/juice-shop
```
Verified with `docker ps` — container running, port 3000 mapped.  
Accessed at `http://localhost:3000/` — Juice Shop storefront loaded successfully.

### 4. Connectivity Validation
Ran Nmap host discovery against the Host-Only subnet:
```bash
nmap -sn 192.168.56.0/24 -oN ~/lab/scans/host_discovery.txt
```
Found **3 live hosts**: `.1` (VirtualBox host adapter), `.100` and `.101` (VirtualBox virtual NICs). Attacker VM confirmed reachable across the isolated lab segment.

### 5. Basic Tool Validation

**Nmap — Port/Service Scan:**
```bash
nmap -sV -p 3000 localhost -oN ~/lab/scans/juice_shop_scan.txt
```
TCP 3000 confirmed open, service fingerprint identified as OWASP Juice Shop HTTP.

**Burp Suite Community Edition:**  
Configured as intercepting proxy. Captured live HTTP requests and WebSocket frames (Socket.IO connection) between the browser and Juice Shop on port 3000. Intercept mode confirmed working.

**Wireshark:**  
Captured loopback traffic with filter `tcp.port == 3000`. Confirmed visibility into the full TCP session and WebSocket frames at the packet level.

---

## Key Takeaways

**Dual-adapter setup is the right default for any lab.**  
Using NAT + Host-Only gives you internet for package management while keeping attack traffic completely off the real LAN. One misconfigured adapter can expose your home network to whatever you're running — so this matters.

**Docker inside Kali is surprisingly clean for web app targets.**  
No separate VM needed for the target. One `docker run` command and Juice Shop is live. The trade-off is both attacker and target live on the same machine, but for a personal lab this is fine — they're still isolated at the network level via the Host-Only adapter.

**Host discovery before anything else.**  
Running `nmap -sn` on the subnet first confirmed the lab was wired correctly before starting any exploitation work. Simple step, saves a lot of confusion later if connectivity is broken.

**Wireshark on loopback is underrated.**  
Filtering `tcp.port == 3000` gave full visibility into the HTTP session and WebSocket handshake that Burp was intercepting. Seeing the same traffic from two different tools — proxy layer and packet layer — gave a much better mental model of what's actually happening on the wire.

**OWASP Juice Shop is the right first target.**  
It's modern (Node.js, REST API, WebSockets), intentionally vulnerable, and realistic. Much better starting point than Metasploitable for web application security work.

---

## Evidence Collected

| Deliverable | Status |
|---|---|
| VirtualBox Host-Only network config | ✅ Captured (Figure 1) |
| Kali Adapter 1 — NAT | ✅ Captured (Figure 2a) |
| Kali Adapter 2 — Host-Only | ✅ Captured (Figure 2b) |
| `ip a` output (eth0, eth1, docker0) | ✅ Captured (Figure 3) |
| VirtualBox VM list (Kali running) | ✅ Captured (Figure 4) |
| `docker ps` — Juice Shop container | ✅ Captured (Figure 5) |
| Juice Shop in browser (localhost:3000) | ✅ Captured (Figure 6) |
| Nmap host discovery scan | ✅ Captured (Figure 7) |
| Nmap `-sV` port scan on port 3000 | ✅ Captured (Figure 8) |
| Burp Suite HTTP/WebSocket intercept | ✅ Captured (Figure 9) |
| Wireshark `tcp.port == 3000` capture | ✅ Captured (Figure 10) |

Full evidence documented in: `Anant_Suthar_SG2210_Cybersecurity_Lab_Build_Report.pdf`

---

## What Comes Next

This lab is now the foundation for future tasks — vulnerability assessment, web app exploitation on Juice Shop, and potentially expanding to an Active Directory lab setup (Windows Server DC + domain-joined workstation for AD attack practice).
