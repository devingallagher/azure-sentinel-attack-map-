# Azure Sentinel Attack Map: Live Honeypot Lab

Welcome to the **Azure Sentinel Attack Map** project — a real-world honeypot simulation where a vulnerable Azure VM is deliberately exposed to the internet to collect and visualize attacker telemetry using Microsoft Sentinel.

This project is designed to demonstrate skills in:

* Cloud infrastructure setup (Azure)
* Security monitoring and telemetry ingestion (Microsoft Sentinel)
* Network exposure and attacker behavior observation
* SIEM query writing (KQL)
* GeoIP enrichment & visual dashboards

---

## 📌 Project Goal

To intentionally expose a Windows 10 VM to real attackers over the public internet, then capture and analyze failed login attempts (Event ID `4625`) using Microsoft Sentinel.

This setup simulates an attack surface a SOC Analyst or Security Engineer might monitor in production.

---

## 🛠️ Architecture Overview

![Final Architecture](walkthrough/27%20final%20architecture.PNG)

### Components:

* **Azure Subscription**

  * Resource Group
  * Virtual Network (VNet)
  * Network Security Group (NSG)
  * Windows 10 Virtual Machine
* **Microsoft Sentinel**

  * Connected to Log Analytics Workspace
  * Custom Workbook for heatmap dashboard
* **Watchlist**

  * CSV file with GeoIP lookup

---

## 🔓 Security Exposure (Intentional Misconfigurations)

This is what makes the project valuable:

### 1. **Removed Default RDP Rule**

The initial rule that allowed RDP (port 3389) was removed.

### 2. **Added Insecure Inbound Rule**

Created a dangerously permissive NSG rule:

* **Source**: Any
* **Destination**: Any
* **Protocol**: Any
* **Port**: \*
* **Priority**: 100
* **Name**: `DANGER_AllowAnyCustomAnyInbound`

![NSG Rule](walkthrough/08%20creating%20a%20new%20inbound%20rule%20to%20let%20in%20hackers%20easily%20on%20network.PNG)

This allowed **all traffic from the internet** to the VM — ideal for observing brute-force login attempts.

### 3. **Disabled Windows Defender Firewall**

Turned off firewall for all profiles:

* Domain
* Private
* Public

![Firewall Off](walkthrough/10%20-%20turn%20off%20vm%20firewalls%20on%20all.PNG)

---

## 🌍 Live Attack Visualization

After exposing the VM, it began receiving thousands of failed login attempts.

These were captured by:

* Windows Security Log (Event ID 4625)
* Shipped to Log Analytics Workspace
* Queried using KQL
* Enriched with GeoIP data
* Displayed on a custom Sentinel workbook

### Final Heatmap:

![Heatmap](walkthrough/31%20-%20attack%20heatmap.PNG)

---

## 📊 Workbook & GeoIP Watchlist

To show **where** the attackers are coming from:

* Uploaded `geoip-summarized.csv` as a watchlist
* Parsed failed login logs using custom KQL
* Mapped IPs to country & region

Used `map.json` to load a custom workbook showing the attack map.

---

## 📁 Folder Breakdown

```
azure-sentinel-attack-map/
├── README.md
├── sentinel-resources/
    ├──geoip-summarized.csv  ← Uploaded to Sentinel watchlist
    └── map.json
├── sentinel-resources/
    └── geo-enrichment.kql
├── walkthrough/          ← All screenshots (in order)
│   ├── 01_create_resource_group.png
│   ├── 02_create_virtual_network.png
│   └── ...
│   └── 31_attack_heatmap.png
```

---

## 📥 Data Flow Summary

1. VM exposed publicly (NSG + firewall off)
2. Real attackers scanned & brute-forced it
3. Failed logins created Event ID 4625 entries
4. Logs collected via Azure Monitor
5. Parsed in Sentinel with KQL
6. IPs enriched with GeoIP
7. Custom workbook visualized results on a map

---

## ✅ Skills Demonstrated

* Azure cloud networking and NSG management
* Microsoft Sentinel SIEM configuration
* Log Analytics Workspace setup
* KQL scripting and workbook design
* Attack simulation and telemetry analysis
* GeoIP enrichment & data visualization

---

## 💡 Next Steps

* Add detection rules for brute force attacks
* Automate alerting (email, playbooks)
* Add honeypot deception tools (e.g. Canary tokens)
* Integrate with Syslog, Sysmon, or other logs

---

## 🤝 Contributing

Pull requests welcome. This is a learning lab and can be extended in many directions.

---

## 📜 License

[MIT License](LICENSE)

---

> **Created by**: Devin Gallagher — aspiring SOC Analyst / Cloud Security Engineer
