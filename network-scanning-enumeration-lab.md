# Network Scanning and Service Enumeration Lab

## Project Overview

This lab demonstrates how Nmap can be used to identify open ports, enumerate services, and analyze the attack surface of a Windows workstation in a controlled home lab environment.

The purpose of this project was to practice basic cybersecurity assessment techniques, document exposed services, review firewall behavior, and provide remediation recommendations based on scan results.

## Tools Used

Nmap  
Windows Command Prompt  
Windows PowerShell  
Windows Defender Firewall  
Windows 11 Workstation  

## Lab Environment

Target IP Address: `192.168.50.211`  
Target System: Windows Workstation  
Network Type: Home lab network  

## Step 1: Identify the Target IP Address

I first used the `ipconfig` command to identify the IPv4 address of the Windows workstation.

```cmd
ipconfig
```

Screenshot:

![IP Configuration](network-scanning-enumeration-lab/screenshots/ipconfig.png)

## Step 2: Run a Basic Nmap Scan

I performed a basic Nmap scan against the Windows workstation to identify open and filtered ports.

```cmd
nmap 192.168.50.211
```

Screenshot:

![Nmap Basic Scan](network-scanning-enumeration-lab/screenshots/nmap-basic-scan.png)

## Step 3: Analyze Scan Results

The scan identified the following open ports:

| Port | State | Service | Description |
|---|---|---|---|
| 135/tcp | Open | msrpc | Microsoft Remote Procedure Call |
| 139/tcp | Open | netbios-ssn | NetBIOS Session Service |
| 445/tcp | Open | microsoft-ds | SMB File Sharing |
| 3389/tcp | Open | ms-wbt-server | Remote Desktop Protocol |
| 5357/tcp | Open | wsdapi | Web Services Discovery |
| 8090/tcp | Open | opsmessaging | Application specific service |

Several additional ports appeared as filtered, which means traffic was likely being blocked or restricted by firewall rules.

## Step 4: Review SMB Exposure

Port `445` was open, which indicates that SMB file sharing was accessible on the local network.

SMB is commonly used for file sharing, printer sharing, and Windows administrative communication. However, it is also commonly targeted by attackers for lateral movement and exploitation when systems are misconfigured or unpatched.

I checked whether SMBv1 was enabled using PowerShell:

```powershell
Get-WindowsOptionalFeature -Online -FeatureName SMB1Protocol
```

Screenshot:

![SMBv1 Status](network-scanning-enumeration-lab/screenshots/smbv1-status.png)

Recommended remediation:

Disable SMBv1 if enabled.  
Restrict SMB access to trusted networks only.  
Keep Windows fully patched.  
Disable file sharing if it is not required.

## Step 5: Review Remote Desktop Exposure

Port `3389` was open, which indicates that Remote Desktop Protocol was accessible.

RDP allows remote access to a Windows system, but it can become a security risk if exposed to untrusted networks or protected with weak credentials.

To check Remote Desktop settings, I opened:

```text
Settings → System → Remote Desktop
```

I verified whether Remote Desktop was enabled and confirmed whether Network Level Authentication was required.

Screenshot:

![Remote Desktop Settings](network-scanning-enumeration-lab/screenshots/remote-desktop-settings.png)

Recommended remediation:

Disable Remote Desktop if not needed.  
Enable Network Level Authentication.  
Use strong passwords.  
Restrict RDP access using firewall rules.  
Avoid exposing RDP directly to the internet.

## Step 6: Review Windows Defender Firewall Rules

I reviewed Windows Defender Firewall inbound rules to understand why certain services were open or filtered.

I opened the firewall management console using:

```cmd
wf.msc
```

Then I reviewed inbound rules related to:

SMB  
Remote Desktop  
NetBIOS  
RPC  
Web Services Discovery  

Screenshot:

![Firewall Inbound Rules](network-scanning-enumeration-lab/screenshots/SMB related inbound rules.png) 

Important rules reviewed:

| Rule Type | Related Port | Security Concern |
|---|---|---|
| File and Printer Sharing | 445 | SMB exposure |
| Remote Desktop | 3389 | Remote access exposure |
| NetBIOS | 139 | Legacy Windows networking |
| RPC | 135 | Windows service enumeration |

The firewall review helped determine which services were allowed through the host firewall and which services were being filtered.

## Step 7: Investigate Port 8090

Port `8090` was open and labeled as `opsmessaging`. Since this was not a standard Windows service I immediately recognized, I investigated which process was using the port.

I used the following command:

```cmd
netstat -ano | findstr :8090
```

Then I used the process ID to identify the application:

```cmd
tasklist | findstr <PID>
```

Screenshot:

![Port 8090 Investigation](screenshots/port-8090-investigation.png)

Recommended remediation:

Identify the application using the port.  
Disable the service if it is not needed.  
Restrict access using firewall rules.  
Document why the service is running.

## Step 8: Perform Service Version Detection

To gather more detail about the detected services, I ran a service version scan.

```cmd
nmap -sV 192.168.50.211
```

Screenshot:

![Nmap Service Version Scan](screenshots/nmap-service-version-scan.png)

This helped identify service details that could be useful during vulnerability analysis.

## Step 9: Run a Vulnerability Script Scan

I used Nmap vulnerability scripts to check for known issues associated with the exposed services.

```cmd
nmap --script vuln 192.168.50.211
```

Screenshot:

![Nmap Vulnerability Scan](screenshots/nmap-vulnerability-scan.png)

This scan helped support basic vulnerability assessment and validation.

## Findings

| Finding | Risk Level | Recommendation |
|---|---|---|
| SMB exposed on port 445 | High | Disable SMBv1 and restrict SMB access |
| RDP exposed on port 3389 | High | Enable NLA and restrict RDP access |
| NetBIOS exposed on port 139 | Medium | Limit exposure to trusted networks |
| RPC exposed on port 135 | Medium | Restrict access where possible |
| Unknown service on port 8090 | Medium | Identify the process and disable if unnecessary |
| Multiple filtered ports detected | Low | Firewall filtering appears to be active |

## Remediation Recommendations

Based on the scan results, I recommended the following hardening steps:

Disable unnecessary services.  
Disable SMBv1 if enabled.  
Restrict SMB and RDP access to trusted networks.  
Keep Windows updated.  
Review and disable unnecessary inbound firewall rules.  
Investigate unknown services running on non standard ports.  
Use strong passwords and Network Level Authentication for RDP.  
Avoid exposing remote access services directly to the internet.

## Validation

After applying remediation steps, I would run another Nmap scan to verify that unnecessary ports were closed or restricted.

```cmd
nmap 192.168.50.211
```

The before and after results can be compared to confirm that the system’s attack surface was reduced.

Screenshot:

![Validation Scan](screenshots/validation-scan.png)

## Skills Demonstrated

Network scanning  
Service enumeration  
Windows attack surface analysis  
Firewall rule review  
SMB security analysis  
RDP exposure review  
Basic vulnerability assessment  
Security documentation  
Remediation planning  
Validation scanning  

## Summary

This lab demonstrated how to perform basic network scanning and service enumeration against a Windows workstation using Nmap. The scan identified open services such as SMB, RDP, RPC, NetBIOS, Web Services Discovery, and an unknown application service on port 8090.

By reviewing the scan results and Windows Defender Firewall inbound rules, I was able to analyze potential security risks and recommend hardening steps to reduce the workstation’s attack surface.
