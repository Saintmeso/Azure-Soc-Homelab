# Azure SOC Homelab - Honeypot, Microsoft Sentinel / Defender & GeoIP Attack Visualization

<img width="904" height="583" alt="Azure SOC Lab Layout" src="https://github.com/user-attachments/assets/a1432d09-ac42-4180-a791-1bfd9c1c889f" />

## Project Overview
Built a cloud-based SOC lab in Azure using a publicly exposed Windows 11 honeypot to capture real-world authentication attempts, collect Windows Security Events, and investigate suspicious activity using Microsoft Sentinel.

Integrated Azure Monitor Agent, Data Collection Rules, and Log Analytics to centralize security telemetry. Use KQL and a GeoIP watchlist to enrich source IP addresses and developed a custom Sentinel workbook to visualize where failed login attemps (EventID 4625) on a geographic map.

The result of this project is to have a security monitoring environment that captures authenitcation activity and transforms it from logs to a geographic redar of where attack and or authentication attempts are coming from.

## Technologies Used

- Azure : Windows 11 VM, VNets, NSG's, RG
- Azure Monitor Agent (AMA) : Windows event collection
- Data Collection Rules (DCR) : Event Collection Configuration
- Log Analytics Workspace : Centralized log storage and querying
- Microsoft Sentinel / Defender : SIEM Monitoring
- KQL : Security event analysis and GeoIP enrichment
- JSON Workbooks : Custom geographic attack visualization

## Key Results

- Captured over 300+ external authentication attemps against a publicly exposed Windows VM (Honeypot)
- Monitored failed Windows Login using EventID 4625
- Enriched source IP addresses with geograpic data using a GeoIP watchlist
- Used a custom Sentinel workbook displaying the geographic location of failed login attempts
- Observed authentication activity from mulitple geographic regions after exposing the honeypot to the public internet.

## Full Technical and decisional breakdown

(Put link here)

## IMPORTANT DISCLAIMER

This is STRICTLY a LAB and TESTING environment.

Do NOT ever change NSG/Firewall rules to allow inbound traffic from all over the internet.

Do NOT disable windows firewall profiles.

These were all done to document real logs coming from real login attempts.

If you are to attempt doing this, Do it on a VM.

Do NOT do it locally.
