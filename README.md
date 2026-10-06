# Microsoft Sentinel Home SOC Lab - Log Analysis and Threat Investigation

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

Configure the network security group and open it up to the internet

<img width="1507" height="380" alt="Screenshot 2026-10-07 at 8 29 05 am" src="https://github.com/user-attachments/assets/a0bc4e80-63c2-471e-be61-8e4fc92856dc" />

Log into the Virtual Machine and disable the internal window firewall 

<img width="2190" height="406" alt="image" src="https://github.com/user-attachments/assets/407b142b-449c-4382-913b-bf9edc8bd186" />

## 5. Review Windows Security Logs in Event Viewer

Once my Windows virtual machine was running, I tested its logging by deliberately entering incorrect login details. After signing in successfully, I opened Event Viewer and filtered the Windows Security log for Event ID 4625. I inspected a failed login event, which showed the failure reason “Unknown user name or bad password.

<img width="2048" height="630" alt="image" src="https://github.com/user-attachments/assets/f5689c6c-e635-43e2-9585-715fddae6996" />

## 6. Create a Log Analytics Workspace

I can then forward these logs into Azure where I can query and connect them into Microsoft Sentinel SIEM through Log Analytics Workspace


## 7. Enable Microsoft Sentinel 



## 8. Connect Window Security Logs to Sentinel 



## 9. Investigate Failed Logins Using KQL 
