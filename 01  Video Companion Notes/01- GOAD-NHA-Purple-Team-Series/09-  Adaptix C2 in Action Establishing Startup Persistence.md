## **09 Adaptix C2 in Action: Scheduled Task Persistence with PersisTask-BOF**

This guide demonstrates how to use Adaptix C2 and the `persistask` BOF to establish scheduled task persistence on a Windows host, ensuring `C:\ludus\adapt.exe` runs at every user logon.

> [!WARNING]  
> **OPSEC ALERT: Scheduled Task Detection Risk**  
> 
> Even when using COM object methods (as with PersisTask-BOF) instead of `schtasks.exe`, scheduled task creation is still a high-visibility persistence technique.
> **See in the video, we got busted by elastic SIEM**
>  ![](../../05%20Images/Pasted%20image%2020250520122024.png)
> 
> - **Defenders routinely monitor for new scheduled tasks** via Windows Event Logs, Sysmon, and EDR solutions.
> - Blue teams may baseline legitimate scheduled tasks and alert on new or modified entries, especially those running from uncommon directories or with suspicious names.
> - While COM-based creation avoids direct `schtasks.exe` command-line detection, it can still be flagged by monitoring for changes in `C:\Windows\System32\Tasks`, registry keys (`HKLM\SOFTWARE\Microsoft\Windows NT\CurrentVersion\Schedule\Tasks`), or by tracking processes loading `taskschd.dll`.
> - Tasks running payloads from non-standard locations (like `C:\ludus\adapt.exe`) are especially likely to trigger alerts.
> 
> **Recommendation:**  
> Use scheduled task persistence only when necessary and in controlled environments. Always assume defenders may detect and investigate new scheduled tasks, regardless of creation method.

---

# **Key Takeaways**

##  **BOFs and Persistence**

- **BOF (Beacon Object File):** Small, in-memory C modules for post-exploitation tasks, run by Adaptix agents.
    
- **Scheduled Task Persistence:** Creating a scheduled task that launches your payload at logon.
    

---

# **Step-by-Step Guide: Scheduled Task Persistence with Adaptix C2**

## **1. Prepare the Persistask Extension JSON**

1. **Clone the BOF**
```bash
git clone https://github.com/nickzer0/PersisTask-BOF.git
cd PersisTask-BOF
```
---
2. **Compile the BOF**
```shell
make
```

3. ** Add Your Adaptix Extension JSON** to the same folder
- Create your extension JSON file (e.g., `persistask.json`) with the following content
```json
{
    "name": "PersisTask-BOF",
    "description": "Persistence via scheduled task creation using a COM object for OPSEC-safe logon persistence.",
    "extensions": [
        {
            "type": "command",
            "agents": ["beacon"],
            "command": "persistask",
            "description": "Add or remove a scheduled task at logon using a stealthy COM object method. For 'add', provide all three arguments. For 'remove', provide the task name and use \"\" (empty quotes) for the command.",
            "message": "BOF implementation: persistask",
            "example": "persistask add MyTaskName \"C:\\Windows\\System32\\notepad.exe\"",
            "args": [
                "STRING <action> (add|remove)",
                "STRING <task_name> (Name of the scheduled task)",
                "STRING <command> (Command to run; required for 'add', use \"\" for 'remove')"
            ],
            "exec": "execute bof $EXT_DIR()/persistask.$ARCH().o $PACK_BOF(CSTR {action} CSTR {task_name} CSTR {command})"
        }
    ]
}

```
 - This JSON defines the `persistask` command for Adaptix C2, matching the BOF’s argument structure[1](https://adaptix-framework.gitbook.io/adaptix-framework/adaptix-c2/bof-and-extensions).

- Your folder should have these contents:
![](../../05%20Images/Pasted%20image%2020250520122804.png)

## **2. Upload the Extension JSON to Adaptix C2**

1. **Open AdaptixClient** and go to the Extensions management section.
![](../../05%20Images/Pasted%20image%2020250520121015.png) 
2. **Upload your `persistask.json` file** via the context menu in the extensions table[1](https://adaptix-framework.gitbook.io/adaptix-framework/adaptix-c2/bof-and-extensions).
    
    - The JSON extension is all that’s needed for command registration.
        
![](../../05%20Images/Pasted%20image%2020250520122953.png)
---

## **3. Deploy Your Payload**

- **Transfer your payload** (`C:\ludus\adapt.exe`) to the target system using your preferred method (SCP, RDP, etc.).
    
- Ensure the file is present and accessible on the target.
    
> [!INFO]
> **OPSEC NOTE: Payload Location, Filename, and EDR Evasion**
>
> - **Destination and Filename Matter:** Placing your payload in uncommon directories (e.g., `C:\ludus\adapt.exe`) or using unique filenames increases the risk of detection by EDR and blue teams.
> - **Legitimate Masquerading:** Adversaries often evade detection by naming payloads after legitimate system binaries and placing them in standard system directories (e.g., `C:\Windows\System32\svchost.exe`). This can help blend in and avoid filename- or path-based alerts.[2]
> - **Detection Opportunities:** Defenders monitor for new or unusual files in non-standard locations, and for scheduled tasks that launch binaries from suspicious paths.
> - **EDR Evasion Strategies:** Consider both the filename and location. Use names and paths that match legitimate software when possible, and test payloads in a well-defended environment to ensure they are not trivially detected.[2][3][4]
> - **Advanced Defenses:** Modern EDRs may still flag binaries based on behavior, metadata, or code similarity, even if masquerading as a system file.[3]
>**Our file was detected since we did nothing to obfuscate it:**
>![](../../05%20Images/Pasted%20image%2020250520123336.png)
> **Recommendation:**  
> Avoid obvious or unique payload names/paths. Consider using trusted locations and filenames to reduce detection risk, but always validate your approach in a test environment before deploying to a real target.

---

## **4. Add the Scheduled Task Persistence**

1. **Open an agent session** with the target host in AdaptixClient.
    
2. **Run the following command to create the scheduled task:**
    
```shell
persistask add MyTaskName "C:\ludus\adapt.exe"
```
    
- This will create a scheduled task named `MyTaskName` that launches `C:\ludus\adapt.exe` at user logon.
        
> [!INFO]
> **OPSEC NOTE: Scheduled Task Naming**
>
> - **Task Name Selection:** The name you assign to a scheduled task is visible in Task Scheduler, system logs, and the file system (`C:\Windows\System32\Tasks`). Obvious or unique names (e.g., `MyTaskName`, `PersistenceTask`, `BackdoorLoader`) can quickly draw the attention of defenders.
> - **Blend In:** Use task names that mimic legitimate system or application tasks (such as `UpdateCheck`, `WindowsUpdateMonitor`, `OneDriveSync`, etc.) to reduce suspicion and avoid standing out during routine blue team reviews.
> - **Consistency:** Ensure the task name matches the naming conventions of other tasks on the target system for maximum stealth.
>
> **Recommendation:**  
> Always choose task names that blend with the environment and do not reveal the task’s true purpose. Review existing scheduled tasks on the target for inspiration before assigning a name.

---

## **5. Validate Persistence**

- **Reboot or log off/log on** the user on the target.
    
- **Check** that `C:\ludus\adapt.exe` executes (monitor process list or logs).
    
- **Verify** the scheduled task:
    
    - In Task Scheduler: Open Task Scheduler, expand **Task Scheduler Library**, and look for `MyTaskName`.
        
    - In the filesystem: Check for a file named `MyTaskName` in `C:\Windows\System32\Tasks`.
        
    - Via command line:
        
        text
        
        `schtasks /query /TN MyTaskName /V`
        

---
## **6. Detection**
- This is a sloppy way of establishing persistence, however, we demoed how a BOF can be used in Adaptix C2. We were detected by elastic SIEM
![](../../05%20Images/Pasted%20image%2020250520121409.png)

and using scp to drop the payload was bad as well
![](../../05%20Images/Pasted%20image%2020250520121459.png)
# **Tools Used**

- **Adaptix C2:** For extension management and agent control[1](https://adaptix-framework.gitbook.io/adaptix-framework/adaptix-c2/bof-and-extensions).
    
- **PersisTask BOF:** For scheduled task creation.
    
- **Windows Task Scheduler/Command Prompt:** For verification.
    
- **SCP/RDP:** For payload transfer.
    

---

# **References**

1. [Adaptix Framework - BOF and Extensions](https://adaptix-framework.gitbook.io/adaptix-framework/adaptix-c2/bof-and-extensions)[1](https://adaptix-framework.gitbook.io/adaptix-framework/adaptix-c2/bof-and-extensions)
    
2. [Windows Scheduled Task Persistence](https://pentestlab.blog/2019/10/01/persistence-scheduled-tasks/)
    

---

**Summary:**  
This workflow enables you to use Adaptix C2 and PersisTask-BOF to stealthily persist your payload (`C:\ludus\adapt.exe`) via a scheduled task.  Though our example was alerted on several times, we still found value, both for defence and offensive side. 

Subscribe to my channel for more valuable no bs content https://www.youtube.com/@ITSecurityLabs