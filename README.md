# Microsoft Sentinel Home SOC Lab - Log Analysis and Threat Investigation
## Acknowledgements

This project was guided by a [Microsoft Sentinel SOC Lab YouTube tutorial](https://youtu.be/g5JL2RIbThM), with additional hands-on investigation, troubleshooting, and findings documented independently.

## 1. Create an Azure Account
I created an Azure free account to use for this lab.

## 2. Create a Resource Group and Virtual Network 

I created a resource group to keep my lab resources, such as the virtual machine and virtual network, organised in one place. This makes them easier to manage and delete when I finish the lab.

I created a virtual network for the lab’s virtual machine to connect to and communicate over.

<img width="1872" height="720" alt="image" src="https://github.com/user-attachments/assets/88361044-742d-4885-8ad5-cc687f8df0d4" />


## 3. Create a Virtual Machine

I created a virtual machine to use as a honeypot, which I would later expose to the internet to observe suspicious activity and collect security logs.

<img width="1112" height="466" alt="Screenshot 2026-10-07 at 8 21 47 am" src="https://github.com/user-attachments/assets/a9b5c44b-7aa5-44fc-90e8-cc89ef817cd2" />

## 4. Configure the Honeypot's Network and Firewall Settings 

I temporarily configured the Network Security Group (NSG) to allow inbound internet traffic for the honeypot experiment. This was an intentionally insecure configuration and is not recommended for production environments.

<img width="1507" height="380" alt="Screenshot 2026-10-07 at 8 29 05 am" src="https://github.com/user-attachments/assets/a0bc4e80-63c2-471e-be61-8e4fc92856dc" />

Log into the Virtual Machine and disable the internal window firewall 

<img width="2190" height="406" alt="image" src="https://github.com/user-attachments/assets/407b142b-449c-4382-913b-bf9edc8bd186" />

## 5. Review Windows Security Logs in Event Viewer

Once my Windows virtual machine was running, I tested its logging by deliberately entering incorrect login details. After signing in successfully, I opened Event Viewer and filtered the Windows Security log for Event ID 4625. I inspected a failed login event, which showed the failure reason “Unknown user name or bad password.

<img width="2048" height="630" alt="image" src="https://github.com/user-attachments/assets/f5689c6c-e635-43e2-9585-715fddae6996" />

## 6. Create a Log Analytics Workspace

I created a Log Analytics workspace to store the VM’s security logs so I could query them with KQL and investigate them using Microsoft Sentinel.

<img width="1191" height="374" alt="Screenshot 2026-10-07 at 9 18 46 am" src="https://github.com/user-attachments/assets/9e4ab47e-89e0-43f5-86b7-56c4dc6e52e4" />

## 7. Enable Microsoft Sentinel on the Log Analytics Workspace 

I enabled Microsoft Sentinel on my Log Analytics workspace to prepare for monitoring and investigating the VM’s security logs.

<img width="1505" height="243" alt="Screenshot 2026-10-07 at 9 25 23 am" src="https://github.com/user-attachments/assets/aa733455-717e-4d32-9324-bdb1a51deec8" />

## 8. Connect Windows Security Event to Sentinel 

I installed Windows Security Events from the Microsoft Sentinel Content Hub and configured the Windows Security Events via AMA data connector.

I then created a Data Collection Rule (DCR) to collect Windows Security Event logs from the virtual machine and send them to my Log Analytics Workspace for analysis in Microsoft Sentinel.

<img width="1134" height="672" alt="Screenshot 2026-10-07 at 9 54 54 am" src="https://github.com/user-attachments/assets/15127725-f10d-4377-bfbd-950050a9f202" />

I confirmed that the Azure Monitor Agent (AMA) was installed on the virtual machine and showed “Provisioning succeeded."

<img width="2048" height="962" alt="image" src="https://github.com/user-attachments/assets/fce96536-5604-4ae3-ba48-07090cac8abf" />


## 9. Investigate Failed Logins Using KQL

After configuring the Windows Security Events data connector, I used KQL (Kusto Query Language) in the Log Analytics Workspace to verify that security logs from the virtual machine were being successfully collected.

I queried the SecurityEvent table and confirmed that events from my CORP-NET-EAST-1 virtual machine were appearing in the workspace.

<img width="2048" height="1303" alt="image" src="https://github.com/user-attachments/assets/41a0554b-5e2c-40ee-900b-25599e72d6c7" />

## 10. Import IP Geolocation Data as a Sentinel Watchlist 

TO add geographic context to security events, I imported IP geolocation data into Microsoft Sentinel as a watchlist.

<img width="2048" height="515" alt="image" src="https://github.com/user-attachments/assets/3a0495a2-c459-4281-924d-b04ca0039a46" />

I queried the GeoIP watchlist using "_GetWatchlist("geoip")" to verify that the geographic data was successfully imported and could be retrieved using KQL.

<img width="1736" height="1188" alt="image" src="https://github.com/user-attachments/assets/24f1f450-9ae4-4619-8942-196a19031496" />

## 11. Enrich Failed Login Logs with Geolocation Using KQL

I used KQL to enrich failed login attempts (Event ID 4625) with GeoIP data from my Sentinel watch list. This allowed me to identify the source IP address and its approximate geographic location 

<img width="1690" height="1330" alt="image" src="https://github.com/user-attachments/assets/663292b8-f3c7-4ebc-a6ba-6e471c08be51" />

## 12. Create an Attack Map Using a Sentinel WorkBook

I created a Microsoft Sentinel attack map to visualise the approximate locations and volume of failed login attempts using enriched GeoIP data.

<img width="2048" height="907" alt="image" src="https://github.com/user-attachments/assets/c8c5e33d-b0b2-4071-9723-56cc835c39d5" />

## 13. Review Results and Document Findings 

**Finding 1: High-volume failed authentication activity**

I observed a high amount of failed authentication login activity (Event ID 4625) on the Windows VM through querying with KQL.

<img width="2048" height="1229" alt="image" src="https://github.com/user-attachments/assets/9c403476-d7d3-4a49-9b61-6cb3d38c6b5f" />

The results showed that "NOUSER", which may be a placeholder associated with failed OpenSSH authentication, had the highest number of failed login events at **6,292**, followed by "WORKGROUP\Administrator" with **1,585**. Several other account names recorded **21 failed attempts** each, suggesting possible automated username guessing or brute-force activity.

The repeated attempts against administrator account suggest that potentially privileged account were being targeted

**Finding 2: Top source IP + geolocation**

Over the last 24 hours, Using KQL and my GeoIP watchlist, I identified that IP address 45.202.0.25 geolocated to Zambia, recorded the highest number of failed login attempts at 889, followed by 103.57.254.252 geolocated to India, with 791 failed attempts.

<img width="2048" height="1226" alt="image" src="https://github.com/user-attachments/assets/8cf2cb7b-65bc-4ff9-82e7-a554f508419b" />

**Data Quality Investigation:** **Data Quality Investigation:** I identified overlapping GeoIP watchlist ranges ("172.0.0.0/8" and "172.216.0.0/16") that caused duplicate enrichment matches for "172.216.11.33". As a result, 164 original failed login events appeared as 328 enriched results. Other enriched IP counts may also be affected by overlapping ranges, so these results should be validated before drawing conclusions.

<img width="2047" height="646" alt="image" src="https://github.com/user-attachments/assets/40d7cfb7-927f-4592-aae0-d4ba652fc2c6" />

The high volume of failed login attempts across multiple IP addresses suggests possible automated attacks. Interestingly, three IP addresses geolocated to different countries each recorded exactly 396 failed attempts. This pattern warrants further investigation but does not prove coordinated activity.

**Finding 3: Check for successful access**

I investigated successful authentication events (Event ID 4624) to determine whether any suspicious logins occurred. Using KQL, I identified 7,172 service logons (Type 5), one network logon (Type 3) and one session unlock (Type 7). 

<img width="2048" height="684" alt="image" src="https://github.com/user-attachments/assets/a390b07c-5a6f-4936-92c0-725dd000059c" />

Further investigation revealed that the Type 3 and Type 7 events were associated with my own Windows account and occurred when I accessed the VM.

<img width="1620" height="971" alt="image" src="https://github.com/user-attachments/assets/e537c33e-1b57-4b7e-9839-5eff8d91cd28" />

Most Type 5 events were associated with Windows and OpenSSH services rather than confirmed user authentication. The OpenSSH logs also showed an invalid username attempting to connect, with the connection closed before authentication.

<img width="1254" height="878" alt="image" src="https://github.com/user-attachments/assets/45c6dbae-2a39-46fe-8641-33a0220c2a35" />

No confirmed unauthorised access was identified during the investigation. The Type 3 and Type 7 logons were consistent with my own VM activity and session times, while OpenSSH logs showed unsuccessful authentication attempts. However, these findings do not completely rule out a compromise.


## 14. Cost Management + Lab Deletion

**Azure Cost Management**
Before deleting the lab, I reviewed Azure Cost Management to monitor resource usage and expenses. The recorded cost was approximately AU$6.43 with most charges coming from virtual machine and storage.

<img width="2048" height="655" alt="image" src="https://github.com/user-attachments/assets/5bb51e42-e382-403f-ac37-39412272c1ab" />

After completing the security investigations and saving the relevant screenshots and KQL queries, I deleted the "OS-SOC-Lab" resource group to clean up the temporary honeypot environment and prevent unnecessary ongoing Azure charges.

<img width="1313" height="760" alt="Screenshot 2026-10-09 at 8 26 05 am" src="https://github.com/user-attachments/assets/0478c727-748d-43f9-bd0f-c28f7a97c2f8" />

**Important** This removed the virtual machine, network resources, Log Analytics Workspace, Microsoft Sentinel configuration and other resources contained within the resource group 

## 15. Key Takeaways

Through this Microsoft Sentinel SOC Lab, I gained hands-on experience in:

- **Security Monitoring:** Collecting and analysing Windows security events using Microsoft Sentinel.
- **KQL:** Querying logs to identify suspicious authentication activity and source IP addresses.
- **Threat Investigation:** Investigating potential brute-force and automated authentication activity while distinguishing legitimate from suspicious logons.
- **Data Validation:** Identifying duplicate GeoIP results and understanding the importance of accurate security data.
- **Cloud Security:** Configuring Azure resources, security monitoring, and network security settings.

**Biggest Takeaway:** Security analysis involves more than detecting threats. It requires investigating evidence, validating data, and drawing conclusions based on the available logs.

