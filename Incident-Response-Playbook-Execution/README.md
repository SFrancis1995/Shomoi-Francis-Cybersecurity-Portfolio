# Incident Response Playbook: Phishing Email Investigation

## Objective
Follow the incident response playbook to analyze, contain, and remediate a suspected phishing email reported by a user.  
The goal of this exercise is to demonstrate structured IR processes using NIST’s phases (Preparation, Identification, Containment, Eradication, Recovery, and Lessons Learned).

---

## Tools & Methodology
- **Email Header Analysis** → Used built-in email header analyzer to review sender, SPF/DKIM/DMARC status.  
- **VirusTotal & Hybrid Analysis** → Checked links and attachments for known malicious signatures.  
- **SIEM (Splunk)** → Queried logs for other instances of the same sender domain/IP.  
- **Incident Response Playbook** → Followed organization’s phishing response workflow.

---

## Findings
- The email contained a suspicious link: `hxxp://login-example[.]com`  
- Domain registered less than 24 hours ago, flagged by VirusTotal as malicious.  
- Splunk search showed **5 additional employees** received the same email.  
- No evidence of click-throughs or credential submissions in proxy/firewall logs.

---

## Analysis
The incident matched known phishing tactics (use of look-alike domains, newly registered infrastructure, and credential harvesting attempts).  
Because no user clicked the link, the impact was **contained at the delivery stage**.  
If clicked, the attacker likely would have harvested credentials to attempt lateral movement.

---

## Recommendations
- Block sender domain and malicious URL at the email gateway and firewall.  
- Add domain to organization’s threat intelligence blacklist.  
- Conduct user awareness refresher training (reinforce phishing recognition).  
- Review SIEM alerts to ensure automated detection of similar campaigns.

---

## Conclusion
The phishing attempt was detected and mitigated before execution.  
No accounts were compromised. The incident response playbook was followed successfully, demonstrating effective detection and containment.  
This exercise reinforces the importance of proactive log monitoring and user reporting.

---

## Screenshots
*(Add screenshots of your SIEM queries, VirusTotal results, or email header analysis here)*  
