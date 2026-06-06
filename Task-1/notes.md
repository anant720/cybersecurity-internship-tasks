# Task 1 — Research Notes

## Threat Intelligence Report (2024–2025)

Quick notes on research process, key findings, and things I learned while putting this together.

---

## Research Process

Started with CISA's Known Exploited Vulnerabilities catalog and the IBM Cost of a Data Breach 2024 report to get the baseline numbers. Cross-referenced with Verizon's DBIR 2024 for breach pattern data. Used KrebsOnSecurity and Wired for real incident coverage, and Wiz's security blog for the cloud misconfiguration case study.

For each threat, I looked for:
1. A concrete definition (not just textbook)
2. A real 2023–2024 incident with documented impact
3. Defenses that actually address the specific attack vector (not generic advice)

---

## Key Stats That Surprised Me

- The average cost of a data breach in 2024 hit **$4.88 million** — up from $4.45M in 2023
- The Change Healthcare ransomware attack cost an estimated **$870 million+** in total losses, not just the $22M ransom
- MOVEit exposed **93 million individuals** across 2,700+ organizations — from a single zero-day
- Mirai's source code was open-sourced by its creator, which is why IoT botnets are still a massive problem 8 years later

---

## Threat Rankings (My Take)

After researching all five, here is how I would rank them by current real-world danger:

1. **RaaS** — The most immediately devastating. Criminal ecosystem is mature, well-funded, and hitting critical infrastructure.
2. **AI Phishing** — Hardest to defend against because it exploits humans, not software. Getting worse fast.
3. **Zero-Days** — High severity but requires more technical sophistication or money to exploit.
4. **Cloud Misconfigs** — Massive scale of exposure but often passive (data sitting exposed vs. active attack).
5. **IoT** — Huge attack surface but often used as DDoS cannon rather than targeted intrusion.

---

## Defenses I Found Most Underrated

- **Immutable backups** against ransomware — most organizations talk about backups but don't test recovery or use immutable storage
- **Out-of-band verification** for BEC/phishing — so simple, basically free, yet most companies don't have a formal policy
- **IoT VLANs** — basic network hygiene that most home and small business setups skip entirely

---

## Sources Used

| Source | Used For |
|--------|----------|
| CISA KEV Catalog | Zero-day and active exploitation data |
| IBM Cost of a Data Breach 2024 | Financial impact statistics |
| Verizon DBIR 2024 | Breach patterns and vectors |
| KrebsOnSecurity | RaaS group activity, BEC incidents |
| Wiz Security Blog | Azure misconfiguration case study |
| Emsisoft Ransomware Report | MOVEit impact figures |
| Cloudflare DDoS Threat Report | Mirai and IoT botnet data |
| OWASP Top 10 | Web application security context |
| NVD / CVE Database | Vulnerability scoring and details |
| Reuters | Change Healthcare coverage |

---

## What I Would Do Differently

- Add a section on **supply chain attacks** (SolarWinds model) — it sits between zero-days and cloud misconfigs and is increasingly common
- Include a **severity scoring matrix** (CVSS-style) for each threat instead of just a qualitative impact level
- Add IOCs (Indicators of Compromise) for each threat to make the report more operationally useful

---

*These are personal research notes, not part of the formal deliverable.*
