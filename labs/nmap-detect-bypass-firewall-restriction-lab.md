# Nmap to Detect and Bypass Firewall Restrictions


## Objective
-The objective of this lab is to demonstrate how Nmap can be used to identify active firewall rules and restrictions, and to test techniques for bypassing those controls. By scanning and analyzing network responses, this exercise provides hands-on experience with understanding how firewalls filter traffic, how attackers attempt to evade detection, and how defenders can strengthen security configurations.

## Tools & Methodology
- **Linux Terminal** – Used as the command-line interface to create directories, files, and execute scans.  
- **mkdir** – Created a project directory (`/home/labex/project/http_service`) to organize files for the HTTP service.  
- **cd** – Navigated into the HTTP service directory.  
- **echo** – Created a basic HTML file (`index.html`) that would be served by the HTTP server.  
- **cat** – Verified the contents of the HTML file to confirm successful creation.  
- **Python3 http.server** – Started a lightweight HTTP server on port 8000 to act as the target for scanning.  
- **Nmap (Regular Scan)** – Executed a standard scan with the `-Pn` option to bypass ICMP echo blocking and identify open ports.  
- **Nmap (Fragmentation Scan)** – Executed a scan using `-f -Pn` to test firewall evasion by fragmenting packets.  
- **Comparison of Results** – Combined output from regular and fragmented scans into a single file (`scan_comparison.txt`) to analyze differences.

## Findings
- A local HTTP server was successfully created to provide a safe environment for scanning practice.  
- A basic Nmap scan with the `-Pn` option bypassed host discovery (ICMP blocking) and revealed open ports that were hidden by firewall rules.  
- The packet fragmentation technique (`-f`) allowed Nmap to bypass stateless packet filters, exposing additional ports that were not visible with standard scans.  
- Results from normal scans and evasion scans differed, confirming that firewall restrictions can be detected and bypassed with the right techniques.  

## Analysis
The lab demonstrated how Nmap can be used to detect and bypass firewall restrictions by applying different scanning techniques.  
- The use of the `-Pn` option highlighted how firewalls that block ICMP echo requests can still allow port scanning, revealing services that would otherwise appear hidden.  
- The `-f` (fragmentation) option showed how attackers can bypass stateless packet filters by splitting packets into smaller fragments, making detection more difficult.  
By comparing standard scans with evasion scans, it became clear that firewalls alone cannot provide complete protection, and layered defenses are required to detect and respond to such techniques. 

## Recommendations
- **Enable Stateful Firewalls** – Use stateful inspection instead of stateless packet filtering to detect fragmented or abnormal traffic patterns.  
- **Deploy Intrusion Detection/Prevention Systems (IDS/IPS)** – Configure alerts for unusual scanning behavior, such as fragmented packets or excessive connection attempts.  
- **Harden Host Configurations** – Restrict services to necessary ports only, and enforce proper access control lists (ACLs).  
- **Log and Monitor Aggressively** – Ensure firewall and server logs are reviewed for repeated scanning attempts or abnormal traffic flows.  
- **Conduct Regular Security Assessments** – Perform authorized vulnerability scans and penetration tests to validate firewall effectiveness against evasion techniques.  
- **User Awareness of Legal/Ethical Boundaries** – Reinforce that such testing must only occur with explicit permission to avoid violating policies and laws.
  
## Screenshots
<img width="1212" height="768" alt="Screenshot 2025-09-19 192123" src="https://github.com/user-attachments/assets/67f9e5dc-33d4-49a0-9074-760b72a0e46a" />
<img width="1549" height="1134" alt="Screenshot 2025-09-19 194024" src="https://github.com/user-attachments/assets/f194503b-6b83-4cfd-b262-22fae6ac24ae" />
<img width="1212" height="768" alt="Screenshot 2025-09-19 192123" src="https://github.com/user-attachments/assets/78722567-66bc-48d0-b3c4-ca86586b0d65" />
