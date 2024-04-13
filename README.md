# Azure-SOC Honeynet (Live traffic)

## Objective
This project aimed to build a mini honeynet in Azure, gathering log data from different sources into a Log Analytics workspace. I then used Microsoft Sentinel to gather the data, create attack maps, initiate alerts, and generate incidents. I assessed security metrics within the vulnerable environment over 24 hours, implementing security measures to harden the environment and then reevaluating metrics over another 24 hours. 

<p align="center">
    <img src="https://github.com/anesum1/Azure-SOC/assets/119237115/775ffaba-4f30-463f-a268-4927b828c9de" alt="Azure-Honeynet-Diagram">
</p>

### Skills Learned

 - Demonstrated competency in setting up a honeynet.
 - Expanded knowledge of firewall configurations
 - Acquired expertise in incident response protocols, effectively mitigating security breaches.
 - Enhanced skills in system hardening techniques to fortify network infrastructure using NIST 800-53.
 - Proficient in creating insightful dashboards for comprehensive security monitoring.
 - Advanced understanding of vulnerability management practices to safeguard against threats proactively.

### Services/Tools Used
- 1 Linux and 2 Windows VM
- Virtual Network (VNet)
- Network Security Group (NSG)
- Microsoft Entra ID
- Log Analytics Workspace
- Microsoft Sentinel
- Microsoft Azure Key Vault
- Microsoft Azure Storage Account
- Microsoft Defender for Cloud

## Project
### STEP 1: Creating a honeynet.
I started this project by creating an Azure Account (Tenant) and a free subscription. Once I had done that, I created two virtual machines (VMs), one Windows (where I downloaded the SQL server) and one Linux, then configured the Network Security Group (Layer 4 Firewall) to allow all traffic inbound. The built-in firewalls on the VMs were wide-open, and all resources were deployed with public endpoints that were visible to the internet. 

### STEP 2: Observing the logs.
In case no one found my vulnerable VMs, I created another Windows VM to act as the attacker. I purposefully used the wrong credentials to try and get into my other two VMs and the SQL server. On checking the logs, I realised I shouldn't have worried about not being discovered because my VMs had been discovered after a few hours, and someone was attempting to log in. What I found fascinating about the Windows logs was that the attacker interchangeably used "ADMIN" or "ADMINISTRATOR" to try and log in.

### STEP 3: Microsoft Entra ID
Microsoft Entra ID is a cloud-based Identity and Access management service that stores user accounts and sets access permissions. It was helpful for me to experiment with user settings and permissions in Azure by logging in as different users and testing their permissions. To understand the different access permissions, I had to refer to the Azure AD hierarchy shown below:

<p align="center">
    <img src="https://github.com/anesum1/Azure-SOC/assets/119237115/33b2a691-aaea-44c4-a348-ed1c226b89d5" width="250" height="300" alt="Azure-Structure">
</p>

1.	**Tenant**- is a top-level organisational unit in Azure.
2.	**Management Groups**- are used to organise subscriptions, and manage access, policy and 
     compliance.
3.	**Subscriptions**- are used to access and organise billing to separate costs 
     for different departments.
4.	**Resource Groups**- These are logical containers for resources in Azure.
5.	**Resource(s)**- An individual resource within a resource group.

After acquiring this information, I proceeded to set user permissions with these specific access rights:
- **Tenant-level global reader:** Read-only access to the entire Azure AD tenant.
- **Subscription-level reader:** Read-only access to specific Azure subscriptions.
- **Resource group-level contributor:** Manage resources within a specific resource group.

### STEP 4- Log Analytics Workspace and Sentinel
My next step was to create a Log Analytics Workspace, my log aggregator. I then created a GeoIP watchlist to help me later when creating maps in Sentinel.  After creating this, I set up Sentinel and connected it to my Log Analytics Workspace. I then enabled Microsoft Defender for Cloud for Log Analytics Workspace, Subscriptions and Continous Export in Environment Settings.

### STEP 5- Generating Logs and KQL Queries
Once everything was connected, I enabled log collection for my VMs, Network Security Groups, Entra ID logs, Azure Activity Logs, Key Vault, and storage account. I then simulated different scenarios to generate logs, such as creating and deleting resource groups, brute-force attacks, and changing NSG settings. When the logs started coming through, I played around with different KQL queries to familiarise myself with the KQL language and see what results would come through.

### STEP 6- Running an Insecure Environment for 24 hours
After setting up everything in Azure, I then left the insecure VMs on for 24 hours to gather security metrics. The VMs had both their Network Security Groups and built-in firewalls wide open, and all other resources were deployed with public endpoints visible to the Internet. Therefore, Private Endpoints (PE) were not utilised. 

#### Architecture BEFORE hardening: 
<p align="center">
    <img src="https://github.com/anesum1/Azure-SOC-Honeynet/assets/119237115/7107b66e-3841-426e-8866-c3f86a0b8ab1" alt="Architecture-before-hardening">
</p>

After 24 hours these were the results I collected:
| Metric                                                 | Count
| -------------------------------------------------------| -----
| Start time                                             | 09/04/24 18:20
| Stop time                                              | 10/04/24 18:20
| SecurityEvent (Windows VM)                             | 36 875
| Syslog  (Linux VM)                                     | 2 286
| SecurityAlert (Microsoft Defender for Cloud)           | 9
| SecurityIncident (Sentinel)                            | 311
| NSG inbound malicious flow allowed                     | 2 123

Using the data collected, I created attack maps using map queries in Sentinel to show where the traffic was coming from.
#### Attack Map for Windows VM
![windows-rdp-auth-fail-map-24hrs-insecure](https://github.com/anesum1/Azure-SOC-Honeynet/assets/119237115/d79940bf-2055-4ded-8ff3-a26ce41dc0dc)

#### Attack Map for Linux VM
![linux-ssh-auth-fail-map-24hrs](https://github.com/anesum1/Azure-SOC-Honeynet/assets/119237115/381272a7-a1d2-47a5-a254-177394fd6e85)

#### Attack Map for NSG
![nsg-malicious-allowed-in-map-24hrs-insecure](https://github.com/anesum1/Azure-SOC-Honeynet/assets/119237115/0426cf1d-96af-43d8-b557-7c42280b33c7)

#### Attack Map for SQL server
![mssql-auth-fail-map-24hrs insecure](https://github.com/anesum1/Azure-SOC-Honeynet/assets/119237115/7f013fc6-20b4-4d9b-9d44-050cbef87850)

#### Incident response
Because of the custom rules I had set in Sentinel, I generated a lot of incidents during the 24-hour period of running the insecure environments. 
![Screenshot 2024-04-10 064638](https://github.com/anesum1/Azure-SOC-Honeynet/assets/119237115/56f075b0-98cc-46b9-8b80-0d732bfd45cc)

All the medium incidents were generated by live traffic. I had to generate the high incidents to see how they would appear in Sentinel. After that, I sat out to investigate the incidents and respond to them using a custom incident management playbook thanks to the help of ChatGPT. 

### Step 7- Running a Secure Environment for 24 hours
When it was time to gather data from a secure environment, I implemented the National Institute of Standards and Technology (NIST) 800-53 framework to ensure its security. I hardened the NSG by blocking all traffic except for my admin computer and all other resources were secured by their built-in firewalls (FW) and private endpoint (PE).

#### Architecture AFTER hardening:
<p align="center">
    <img src="https://github.com/anesum1/Azure-SOC-Honeynet/assets/119237115/93cb9bc6-3727-4fcf-b08a-02f3c94b3451" alt="Architecture-after-hardening">
</p>

After 24 hours these were the results I collected:
| Metric                                                 | Count
| -------------------------------------------------------| -----
| Start time                                             | 11/04/24 18:16
| Stop time                                              | 12/04/24 18:16
| SecurityEvent (Windows VM)                             | 14 399
| Syslog  (Linux VM)                                     | 3
| SecurityAlert (Microsoft Defender for Cloud)           | 0
| SecurityIncident (Sentinel)                            | 0
| NSG inbound malicious flow allowed                     | 0

I was unable to retrieve any maps because no malicious activity was detected within the 24-hour period after the hardening process. As a result, all map queries returned no results. Since no malicious activity was detected in the environment, Sentinel did not generate any incidents.

#### Results 
| Metric                                                 | Change after security environment
| -------------------------------------------------------| -----
| SecurityEvent (Windows VM)                             | 60.95%
| Syslog  (Linux VM)                                     | 99.87%
| SecurityAlert (Microsoft Defender for Cloud)           | 100%
| SecurityIncident (Sentinel)                            | 100%
| NSG inbound malicious flow allowed                     | 100%

## Conclusion
I created a small honeynet in Microsoft Azure for a project and connected log sources to a Log Analytics workspace. The logs were monitored by Microsoft Sentinel, which detected alerts and generated incidents. The project also measured metrics in an insecure environment before implementing security measures and again after applying the security controls. It's important to note that after implementing the security measures, the number of security events and incidents decreased significantly, indicating their effectiveness. However, if regular users heavily used the network resources, more security events and alerts might have been generated within the 24-hour period following the implementation of the security controls.
