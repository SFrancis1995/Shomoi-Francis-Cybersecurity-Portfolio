# SMB Enumeration and Anonymous Share Access

## Lab Overview

This lab demonstrates how to enumerate SMB services, identify available network shares, test for anonymous authentication, and retrieve files from a misconfigured SMB share. The scenario is based on the Hack The Box Dancing machine and focuses on a common internal network security issue: overly permissive SMB share access.

SMB, or Server Message Block, is commonly used in Windows environments for file and printer sharing. When SMB shares are not properly secured, an attacker may be able to browse directories or download files without valid credentials.

## Lab Details

| Category | Details |
|---|---|
| Platform | Hack The Box |
| Machine | Dancing |
| Difficulty | Easy |
| Operating System | Windows |
| Main Service | SMB |
| Port | TCP 445 |
| Primary Tools | Nmap, smbclient |
| Skill Area | Enumeration, SMB, Misconfiguration Analysis |

## Objectives

| Objective | Description |
|---|---|
| Identify open services | Use Nmap to discover services running on the target |
| Enumerate SMB shares | Use smbclient to list available SMB shares |
| Test anonymous access | Attempt login without valid credentials |
| Browse accessible shares | Navigate directories inside the SMB share |
| Retrieve files | Download accessible files from the target share |
| Document findings | Summarize the risk and recommend mitigations |

## Tools Used

| Tool | Purpose |
|---|---|
| Nmap | Service discovery and version detection |
| smbclient | SMB share enumeration and file retrieval |
| Linux Terminal | Command execution and file review |

## Methodology

The lab followed a basic penetration testing workflow:

1. Reconnaissance
2. Service enumeration
3. SMB share discovery
4. Anonymous authentication testing
5. File enumeration and retrieval
6. Findings and recommendations

## Step 1: Service Discovery with Nmap

The first step was to scan the target and identify open services.

```bash
sudo nmap -sV TARGET_IP
```

### Command Breakdown

| Option | Description |
|---|---|
| `sudo` | Runs the command with elevated privileges |
| `nmap` | Starts the Nmap scanner |
| `-sV` | Attempts to identify service versions |
| `TARGET_IP` | Target machine IP address |

### Example Output

```bash
PORT    STATE SERVICE      VERSION
135/tcp open  msrpc        Microsoft Windows RPC
445/tcp open  microsoft-ds Microsoft Windows SMB
```

### Analysis

The scan identified TCP port 445 as open. Port 445 is commonly associated with SMB. Since SMB can expose shared folders across a network, this service became the primary focus for further enumeration.

### Screenshot Placeholder

Add screenshot here:

```text
/images/labs/smb-enumeration/nmap-scan.png
```

## Step 2: Enumerating SMB Shares

After confirming SMB was available, I used smbclient to list available shares.

```bash
smbclient -L //TARGET_IP
```

When prompted for a password, I pressed Enter to test whether anonymous or guest access was allowed.

### Command Breakdown

| Option | Description |
|---|---|
| `smbclient` | Connects to SMB services from Linux |
| `-L` | Lists shares on the target host |
| `//TARGET_IP` | Specifies the target SMB server |

### Example Output

```bash
Sharename       Type      Comment
---------       ----      -------
ADMIN$          Disk      Remote Admin
C$              Disk      Default share
IPC$            IPC       Remote IPC
WorkShares      Disk      Custom Share
```

### Analysis

The output showed multiple SMB shares. The administrative shares, such as `ADMIN$` and `C$`, are normally restricted. The custom share named `WorkShares` was more interesting because custom shares are often created by administrators and may have weaker permissions.

### Screenshot Placeholder

Add screenshot here:

```text
/images/labs/smb-enumeration/smb-share-listing.png
```

## Step 3: Testing Administrative Shares

I attempted to connect to the administrative shares first.

```bash
smbclient //TARGET_IP/ADMIN$
```

```bash
smbclient //TARGET_IP/C$
```

### Example Output

```bash
tree connect failed: NT_STATUS_ACCESS_DENIED
```

### Analysis

Access was denied for both administrative shares. This showed that those shares were properly restricted and required valid credentials.

### Screenshot Placeholder

Add screenshot here:

```text
/images/labs/smb-enumeration/access-denied-admin-shares.png
```

## Step 4: Accessing the Custom SMB Share

Next, I attempted to connect to the custom `WorkShares` share.

```bash
smbclient //TARGET_IP/WorkShares
```

When prompted for a password, I pressed Enter.

### Example Output

```bash
Try "help" to get a list of possible commands.
smb: \>
```

### Analysis

The prompt changed to `smb: \>`, which confirmed that access to the share was successful. This means the share allowed access without valid user credentials. From a security perspective, this is a misconfiguration because unauthorized users could browse and retrieve files.

### Screenshot Placeholder

Add screenshot here:

```text
/images/labs/smb-enumeration/workshares-login-success.png
```

## Step 5: Enumerating Files and Directories

Inside the SMB shell, I listed the available directories.

```bash
ls
```

### Example Output

```bash
Amy.J
James.P
```

### Analysis

The share contained two user directories. User directories can contain notes, configuration files, credentials, or other sensitive information, so I continued enumerating each directory.

### Screenshot Placeholder

Add screenshot here:

```text
/images/labs/smb-enumeration/workshares-directory-listing.png
```

## Step 6: Retrieving Files from the Share

I navigated into the first directory and found a text file.

```bash
cd Amy.J
ls
get worknotes.txt
```

Then I returned to the parent directory and checked the second user folder.

```bash
cd ..
cd James.P
ls
get flag.txt
```

### SMB Commands Used

| Command | Description |
|---|---|
| `ls` | Lists files and directories |
| `cd` | Changes directories |
| `get` | Downloads a file from the share |
| `exit` | Closes the SMB session |

### Analysis

The `get` command downloaded files from the SMB share to my local machine. This confirmed that the permissions were not limited to browsing only. The share also allowed file retrieval.

### Screenshot Placeholder

Add screenshot here:

```text
/images/labs/smb-enumeration/file-retrieval.png
```

## Step 7: Reviewing Retrieved Files

After exiting the SMB shell, I reviewed the downloaded files locally.

```bash
exit
cat worknotes.txt
cat flag.txt
```

### Example Output

```bash
cat worknotes.txt
start apache server on the linux machine
secure the ftp server
setup winrm on dancing
```

```bash
cat flag.txt
FLAG_VALUE_HERE
```

### Analysis

The retrieved notes showed information that could support further enumeration or lateral movement in a larger environment. The flag file confirmed successful access to sensitive content through the misconfigured SMB share.

### Screenshot Placeholder

Add screenshot here:

```text
/images/labs/smb-enumeration/flag-output.png
```

## Findings

| Finding | Description | Risk |
|---|---|---|
| SMB exposed on TCP 445 | The target had SMB available over the network | Medium |
| Anonymous share listing allowed | Shares could be listed without valid credentials | Medium |
| Custom share allowed anonymous access | The `WorkShares` share allowed unauthenticated access | High |
| Files could be downloaded | Users without credentials could retrieve files | High |

## Risk Summary

The main issue discovered in this lab was improper access control on an SMB share. The `WorkShares` share allowed unauthenticated access, which exposed internal files to unauthorized users. In a real enterprise environment, this type of misconfiguration could lead to sensitive data disclosure, credential exposure, and further compromise of internal systems.

## Remediation Recommendations

| Recommendation | Reason |
|---|---|
| Disable anonymous SMB access | Prevents unauthenticated users from connecting to shares |
| Apply least privilege permissions | Ensures users only access files required for their role |
| Review all custom shares | Custom shares are commonly misconfigured |
| Enable SMB access auditing | Helps detect unauthorized access attempts |
| Restrict SMB at the firewall | Limits SMB access to trusted internal hosts |
| Disable SMBv1 if enabled | Reduces exposure to legacy SMB vulnerabilities |
| Remove unnecessary shares | Reduces the attack surface |

## MITRE ATT&CK Mapping

| Technique ID | Technique Name | Lab Relevance |
|---|---|---|
| T1135 | Network Share Discovery | Used smbclient to identify available SMB shares |
| T1083 | File and Directory Discovery | Listed directories and files inside the accessible share |
| T1005 | Data from Local System | Retrieved files from the exposed SMB share |
| T1021.002 | SMB/Windows Admin Shares | Tested SMB share access over the network |

## Lessons Learned

This lab reinforced the importance of SMB enumeration during internal security assessments. Even when administrative shares are locked down, custom shares may still expose sensitive files if permissions are not configured correctly. Anonymous access should be disabled, and administrators should regularly audit SMB share permissions.

## Commands Cheat Sheet

```bash
sudo nmap -sV TARGET_IP
```

```bash
smbclient -L //TARGET_IP
```

```bash
smbclient //TARGET_IP/ADMIN$
```

```bash
smbclient //TARGET_IP/C$
```

```bash
smbclient //TARGET_IP/WorkShares
```

```bash
ls
```

```bash
cd DIRECTORY_NAME
```

```bash
get FILE_NAME
```

```bash
exit
```

```bash
cat FILE_NAME
```

## Portfolio Summary

In this lab, I performed SMB enumeration against a Windows target, identified available shares, tested for anonymous authentication, and retrieved files from a misconfigured SMB share. This exercise demonstrated how weak SMB permissions can expose sensitive data and why organizations must regularly audit file share access controls.

## Screenshot Checklist

| Screenshot | File Name Suggestion |
|---|---|
| Nmap scan showing port 445 open | `nmap-scan.png` |
| SMB share listing | `smb-share-listing.png` |
| Access denied on admin shares | `access-denied-admin-shares.png` |
| Successful WorkShares login | `workshares-login-success.png` |
| Directory listing | `workshares-directory-listing.png` |
| File download with get command | `file-retrieval.png` |
| Local file output | `flag-output.png` |

## Suggested GitHub Folder Structure

```text
Cybersecurity-Portfolio/
├── labs/
│   └── smb-enumeration-anonymous-access.md
├── images/
│   └── labs/
│       └── smb-enumeration/
│           ├── nmap-scan.png
│           ├── smb-share-listing.png
│           ├── access-denied-admin-shares.png
│           ├── workshares-login-success.png
│           ├── workshares-directory-listing.png
│           ├── file-retrieval.png
│           └── flag-output.png
```

