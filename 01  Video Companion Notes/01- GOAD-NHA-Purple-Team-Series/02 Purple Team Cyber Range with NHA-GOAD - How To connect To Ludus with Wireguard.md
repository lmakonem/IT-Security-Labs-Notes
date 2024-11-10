# 02 Purple Team Cyber Range with NHA-GOAD - How To connect To Ludus with Wireguard

This GitHub page is a companion to the YouTube video tutorial on **how to connect to Ludus GOAD with Elastic using WireGuard**. In this video, you will learn how to securely connect your Kali Linux machine to a Ludus cyber range using WireGuard, allowing you to practice ethical hacking and threat detection in a realistic environment.

## 📺 **Watch the Video**

**Video Title**: _ 02 Purple Team Cyber Range with NHA-GOAD - How To connect To Ludus with Wireguard_ 
[Watch the video on YouTube](https://youtu.be/fM65Mm011bg)

## 🛠️ **Key Takeaways**

1. **Deploy and Connect to NHA-GOAD**:
    
    - Learn how to deploy the **NHA-GOAD** Active Directory lab environment on Ludus. Follow [[01 - How To Setup a Purple Team Cyber Range with NHA-GOAD & Elastic SIEM on Ludis - AUTOMATED]] for setup instructions.
    - Use WireGuard to securely connect your Kali Linux machine to the GOAD environment for purple teaming exercises.
    
2. **Integrate Elastic Security SIEM**:
    
    - Access the **Elastic Security SIEM** dashboard within the GOAD environment.
    - Monitor and analyze security events in real-time, making it an essential tool for blue team operations.
    
3. **Hands-On Learning**:
    
    - This lab setup allows you to scan the network, simulate attacks, and detect malicious activities using Elastic Security.
    

## 📚 **Resources**

Here are some useful resources mentioned in the video:

- [Part 1: How to Deploy the Lab](https://youtu.be/cjTzWQ_Si3U?si=ZZF0r9EKEw0gq8u0): A step-by-step guide on deploying the cyber range.
- [NHA-GOAD Guide](https://docs.ludus.cloud/docs/environment-guides/goad-nha): Official documentation on setting up NHA-GOAD.
- [Elastic SIEM Guide](https://docs.ludus.cloud/docs/environment-guides/elastic): Guide on integrating Elastic Security SIEM.
- [How to Install WireGuard in Kali](https://www.kali.org/blog/wireguard-on-kali/): Instructions for installing WireGuard on Kali Linux.

## 🚀 **Getting Started**

## 1. **Set Up WireGuard on Kali Linux**

Follow these steps to install and configure WireGuard on your Kali machine:

1. Install WireGuard:bash
    
    `sudo apt-get install wireguard`
    
2. Generate or receive a WireGuard configuration file (`wg0.conf`) from your lab admin (if you're sharing the environment). This is only needed if you are not using the ludus provided kali
3. Place the configuration file in `/etc/wireguard/`:
    
    `sudo nano /etc/wireguard/wg0.conf`
    
4. Start WireGuard:bash
    
    `sudo wg-quick up wg0`
    
5. Verify that your connection is active by checking your IP address:bash
    
    `ip a`
    

## 2. **Access Elastic SIEM**

Once connected via WireGuard, access your **Elastic SIEM** dashboard:

1. Open a browser and navigate to the lab Kibana instance:
    
    `https://<Elastic_IP>:5601`
    
2. Sign in and start monitoring security events.

## 3. **Scan the Network**

Use tools like `nmap` from your Kali machine to scan the GOAD network:

`nmap -sV -v 10.3.1.0/24`

This will help you identify open ports and services running within the cyber range.

## ⚠️ **Disclaimer**

This project is intended for **educational purposes only**. The tools and techniques demonstrated should only be used in environments where you have explicit permission to perform penetration testing or security assessments. Unauthorized use of these tools may violate laws and regulations.

## 🔧 **Tools Used**

- **Ludus Platform**: A cloud-based platform for deploying cybersecurity labs.
- **NHA-GOAD**: A pre-configured Active Directory environment designed for attack and defense simulations.
- **Elastic Security (SIEM)**: A powerful tool for real-time threat detection and monitoring.
- **WireGuard**: A modern VPN solution used for secure connections between machines.

Thank you for visiting! Be sure to subscribe to [I.T Security Labs on YouTube](https://www.youtube.com/@ITSecurityLabs) for more tutorials and updates!