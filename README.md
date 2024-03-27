# Azure-SOC Honeynet (Live traffic)

## Objective
This project aimed to build a mini honeynet in Azure, gathering log data from different sources into a Log Analytics workspace. I then used Microsoft Sentinel to gather the data, create attack maps, initiate alerts, and generate incidents. I assessed security metrics within the vulnerable environment over 24 hours, implementing security measures to harden the environment and then reevaluating metrics over another 24 hours. 

### Skills Learned
 - Demonstrated competency in setting up a honeynet.
 - Expanded knowledge of firewall configurations
 - Acquired expertise in incident response protocols, effectively mitigating security breaches.
 - Enhanced skills in system hardening techniques to fortify network infrastructure.
 - Proficient in creating insightful dashboards for comprehensive security monitoring.
 - Advanced understanding of vulnerability management practices to proactively safeguard against threats.

### Tools Used
- Microsoft Sentinel
- 1 Linux and 2 Windows VM
- Virtual Network (VNet)
- Network Security Group (NSG)
- Log Analytics Workspace
- Microsoft Azure Key Vault
- Microsoft Azure Storage Account

## Project
### STEP 1: Creating a honeynet.
I started this project by creating an Azure Account (Tenant) and a free subscription. Once I had done that I  created two virtual machines (VMs), one Windows (were I downloaded the SQL server)  and one Linux, then configured the Network Security Group (Layer 4 Firewall) to allow all traffic inbound. The built-in firewalls on the VMs were wide-open and all resources were deployed with public endpoints visible to the internet. 

### STEP2: Observing the logs.
<p align="center">
    <img src="https://github.com/anesum1/Azure-SOC/assets/119237115/8a99d15f-3e4a-4998-8ff8-47beece34e93" alt="Attack-Diagram">
</p>

In case no one found my vulnerable VMs, I created another Windows VM to act as the attacker. I purposefully used the wrong credentials to try and get into my other two VMs and the SQL server. On checking the logs, I realised  I shouldn't have worried about not being discovered because after a few hours my VMs had been discovered and someone was attempting to log in. What I found fascinating about the Windows logs was that the attacker interchangeably used "ADMIN" or "ADMINISTRATOR" to try and log in.


<!--# Building an Azure SOC + Honeynet (Live Traffic)
![Cloud Honeynet / SOC](https://i.imgur.com/ZWxe03e.jpg)

## Introduction

In this project, I build a mini honeynet in Azure and ingest log sources from various resources into a Log Analytics workspace, which is then used by Microsoft Sentinel to build attack maps, trigger alerts, and create incidents. I measured some security metrics in the insecure environment for 24 hours, apply some security controls to harden the environment, measure metrics for another 24 hours, then show the results below. The metrics we will show are:

- SecurityEvent (Windows Event Logs)
- Syslog (Linux Event Logs)
- SecurityAlert (Log Analytics Alerts Triggered)
- SecurityIncident (Incidents created by Sentinel)
- AzureNetworkAnalytics_CL (Malicious Flows allowed into our honeynet)

## Architecture Before Hardening / Security Controls
![Architecture Diagram](https://i.imgur.com/aBDwnKb.jpg)

## Architecture After Hardening / Security Controls
![Architecture Diagram](https://i.imgur.com/YQNa9Pp.jpg)

The architecture of the mini honeynet in Azure consists of the following components:

- Virtual Network (VNet)
- Network Security Group (NSG)
- Virtual Machines (2 windows, 1 linux)
- Log Analytics Workspace
- Azure Key Vault
- Azure Storage Account
- Microsoft Sentinel

For the "BEFORE" metrics, all resources were originally deployed, exposed to the internet. The Virtual Machines had both their Network Security Groups and built-in firewalls wide open, and all other resources are deployed with public endpoints visible to the Internet; aka, no use for Private Endpoints.

For the "AFTER" metrics, Network Security Groups were hardened by blocking ALL traffic with the exception of my admin workstation, and all other resources were protected by their built-in firewalls as well as Private Endpoint

## Attack Maps Before Hardening / Security Controls
![NSG Allowed Inbound Malicious Flows](https://i.imgur.com/1qvswSX.png)<br>
![Linux Syslog Auth Failures](https://i.imgur.com/G1YgZt6.png)<br>
![Windows RDP/SMB Auth Failures](https://i.imgur.com/ESr9Dlv.png)<br>

## Metrics Before Hardening / Security Controls

The following table shows the metrics we measured in our insecure environment for 24 hours:
Start Time 2023-03-15 17:04:29
Stop Time 2023-03-16 17:04:29

| Metric                   | Count
| ------------------------ | -----
| SecurityEvent            | 19470
| Syslog                   | 3028
| SecurityAlert            | 10
| SecurityIncident         | 348
| AzureNetworkAnalytics_CL | 843

## Attack Maps Before Hardening / Security Controls

```All map queries actually returned no results due to no instances of malicious activity for the 24 hour period after hardening.```

## Metrics After Hardening / Security Controls

The following table shows the metrics we measured in our environment for another 24 hours, but after we have applied security controls:
Start Time 2023-03-18 15:37
Stop Time	2023-03-19 15:37

| Metric                   | Count
| ------------------------ | -----
| SecurityEvent            | 8778
| Syslog                   | 25
| SecurityAlert            | 0
| SecurityIncident         | 0
| AzureNetworkAnalytics_CL | 0

## Conclusion

In this project, a mini honeynet was constructed in Microsoft Azure and log sources were integrated into a Log Analytics workspace. Microsoft Sentinel was employed to trigger alerts and create incidents based on the ingested logs. Additionally, metrics were measured in the insecure environment before security controls were applied, and then again after implementing security measures. It is noteworthy that the number of security events and incidents were drastically reduced after the security controls were applied, demonstrating their effectiveness.

It is worth noting that if the resources within the network were heavily utilized by regular users, it is likely that more security events and alerts may have been generated within the 24-hour period following the implementation of the security controls.
-->
