# 🛡️ Splunk Detection Lab — Building a SOC from Scratch

![Splunk](https://img.shields.io/badge/Splunk_Enterprise-000000?style=for-the-badge&logo=splunk&logoColor=white)
![Azure](https://img.shields.io/badge/Microsoft_Azure-0078D4?style=for-the-badge&logo=microsoftazure&logoColor=white)
![SOC](https://img.shields.io/badge/SOC_Engineering-CC0000?style=for-the-badge)
![Detection](https://img.shields.io/badge/Detection_&_Analysis-1D9E75?style=for-the-badge)

> A hands-on lab series that builds a working Security Operations Center on Splunk from the ground up — cloud deployment, log collection from Windows, Linux, syslog, cloud, and HTTP sources, centralized management, and real security investigation. Every lab is a step-by-step guide with screenshots, written so a beginner can follow along.

**Find it, prove it, govern it.**

---

## 🎯 What This Series Covers

This project takes Splunk from an empty cloud VM all the way to a working SOC that collects logs from every major source and investigates real attacks. It moves through four stages:

1. **Foundation** — deploy and harden Splunk on Azure (Labs 1–7)
2. **Data Collection** — bring in logs from Windows, Linux, network devices, cloud, and apps (Labs 8–17)
3. **Operations** — keep the platform healthy and running (Lab 18)
4. **Detection & Analysis** — investigate alerts and scale to multi-client monitoring (Labs 19–22)

---

## 🏗️ What Gets Built

```
   Windows · Linux · Network devices · Microsoft 365 · Custom apps
        │        │          │              │             │
        └────────┴──────────┴──────────────┴─────────────┘
                              │
                     Splunk Enterprise (Azure)
                     collect · index · search · detect
                              │
                    Investigation & Dashboards
```

---

## 📚 The Labs

### Foundation — Deploy & Secure Splunk
| # | Lab | What you build |
|---|---|---|
| 01 | [Environment Setup](lab-01-environment-setup/) | Provision and harden an Azure Ubuntu VM |
| 02 | [Installing Splunk](lab-02-installing-splunk/) | Install Splunk Enterprise as a secure service |
| 03 | [Performance Tuning](lab-03-tuning-performance/) | Kernel, THP, and ulimit tuning for production |
| 04 | [User Management](lab-04-user-management/) | Create, edit, clone, and delete users |
| 05 | [Role Management](lab-05-role-management/) | Build custom roles with least-privilege access |
| 06 | [Password Policy](lab-06-password-policy/) | Enforce strong password rules |
| 07 | [License Installation](lab-07-license-installation/) | Install a license and understand quotas |

### Data Collection — Get Logs In
| # | Lab | What you build |
|---|---|---|
| 08 | [Receiving Port](lab-08-receiving-port/) | Open port 9997 for forwarder data |
| 09 | [Windows Log Collection](lab-09-windows-log-collection/) | Collect Windows Event Logs with a forwarder |
| 10 | [Linux Log Collection](lab-10-linux-log-collection/) | Collect Linux logs with a forwarder |
| 11 | [App Management](lab-11-app-management/) | Install and manage Splunk apps |
| 12 | [Management Port](lab-12-management-port/) | Open port 8089 for central management |
| 13 | [Deployment Server — Windows](lab-13-deployment-server-windows/) | Centrally manage Windows forwarders |
| 14 | [Deployment Server — Linux](lab-14-deployment-server-linux/) | Centrally manage Linux forwarders |
| 15 | [Syslog Collection](lab-15-syslog-collection/) | Collect from network devices on port 514 |
| 16 | [Microsoft 365 Integration](lab-16-m365-integration/) | Pull cloud audit logs via API |
| 17 | [HTTP Event Collector](lab-17-hec/) | Ingest data over HTTP with tokens |

### Operations — Keep It Healthy
| # | Lab | What you build |
|---|---|---|
| 18 | [Troubleshooting & Health Check](lab-18-troubleshooting-health-check/) | Monitor and maintain the deployment |

### Detection & Analysis — Do the SOC Work
| # | Lab | What you build |
|---|---|---|
| 19 | [Security Event Analysis](lab-19-security-event-analysis/) | Investigate alerts and triage threats |
| 20 | [MSSP Multi-Tenancy](lab-20-mssp-multitenancy/) | Monitor many clients from one platform |
| 21 | Lab 21 | *Coming soon* |
| 22 | Lab 22 | *Coming soon* |

---

## 🧰 Skills Demonstrated

**Splunk Administration** — installation, hardening, indexes, users, roles, licensing, apps, deployment server, health monitoring

**Data Ingestion** — Universal Forwarders (Windows & Linux), syslog, Microsoft 365 API, HTTP Event Collector — every major source type

**Cloud & Infrastructure** — Azure VM provisioning, NSG firewalls, RDP/SSH, Linux administration

**Security Operations** — alert triage, brute-force investigation, impossible-travel detection, SPL analysis, multi-tenant MSSP design

---

## 🚀 How to Use This Repo

Each lab is a self-contained folder with a `README.md` and a `screenshots/` folder. Start at [Lab 01](lab-01-environment-setup/) and work through in order — each builds on the last. You'll need a free [Azure account](https://azure.microsoft.com/free/) and a free [Splunk account](https://www.splunk.com).

---

## 👤 Author

**Nasrin Ismet**
SOC Analyst & GRC Consultant | M.S. Cybersecurity

*"Find it, prove it, govern it."*

[![LinkedIn](https://img.shields.io/badge/LinkedIn-Connect-0A66C2?style=flat-square&logo=linkedin&logoColor=white)](https://www.linkedin.com/in/kismetara/)
[![GitHub](https://img.shields.io/badge/GitHub-Portfolio-181717?style=flat-square&logo=github&logoColor=white)](https://github.com/ismet60)

⭐ *Star this repo if it helped*
