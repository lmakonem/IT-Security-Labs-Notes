#  03 Purple Team Cyber Range with NHA-GOAD - Busted! Default Sliver VS Default Elastic SIEM

This GitHub page is a companion to the YouTube video tutorial on **testing your lab against default Sliver C2 implants**. In this video, you will learn how to deploy a default Sliver payload in a Ludus GOAD environment and analyze the data using Elastic Security SIEM. You’ll also see an unexpected alert that fired during the test!

## 📺 **Watch the Video**

**Video Title**: _03 Purple Team Cyber Range with NHA-GOAD | Busted! Default Sliver VS Default Elastic SIEM_

[](https://www.youtube.com/@ITSecurityLabs)_ 
[Watch the video on YouTube](https://www.youtube.com/watch?v=L5DtvxOriOU)

## 🛠️ **Key Takeaways**

1. **Deploying Sliver C2 Implants**:
    
    - Learn how to deploy a default **Sliver C2 payload** in your Ludus GOAD environment.
    - Understand how attackers use command and control (C2) frameworks like Sliver to manage compromised machines.
    
2. **Analyzing Data with Elastic Security SIEM**:
    
    - Use **Elastic Security SIEM** to monitor and analyze the behavior of the Sliver implant.
    - Learn how to detect suspicious activities and identify potential compromises in real-time.
    
3. **Unexpected Alert**:
    
    - During the test, an unexpected alert fired in Elastic Security. Watch the video to understand what triggered it and how to interpret such alerts.
    

## 📚 **Resources**

Here are some useful resources mentioned in the video:

- [NHA-GOAD Guide](https://docs.ludus.cloud/docs/environment-guides/goad-nha): Official documentation on setting up NHA-GOAD.
- [Elastic SIEM Guide](https://docs.ludus.cloud/docs/environment-guides/elastic): Guide on integrating Elastic Security SIEM.
- [Sliver C2 Framework](https://github.com/BishopFox/sliver): Source code and more information about Sliver C2 from Bishop Fox.

## Previous Videos:

- [Video 1: How to Deploy a Cyber Range Using Ludus: NHA-GOAD for Purple Teaming & Elastic Security SIEM](https://youtu.be/cjTzWQ_Si3U)
- [Video 2: How to Connect to Ludus GOAD with Elastic Using WireGuard](https://youtu.be/fM65Mm011bg)

## 🚀 **Getting Started**

## 1. **Deploy Sliver C2 Implant**

Follow these steps to deploy a default Sliver implant in your Ludus GOAD environment:

1. Download and install the Sliver C2 framework from [Bishop Fox’s GitHub repository](https://github.com/BishopFox/sliver).
2. Create a new implant by running:bash
    
    `sliver > generate --mtls --os windows`
    
3. Transfer the generated payload to one of the machines in your GOAD environment.
4. Execute the payload on the target machine.

## 2. **Monitor with Elastic Security SIEM**

Once the implant is deployed, use Elastic Security SIEM to monitor and detect any suspicious activity:

1. Open Kibana (Elastic's dashboard interface) by navigating to:text
    
    `https://<Elastic_IP>:5601`
    
2. Go to the **Security** section and view real-time alerts and logs generated by Elastic Security.
3. Investigate any alerts or anomalies detected by Elastic, including the surprise alert mentioned in the video.

## 3. **Analyze Logs and Alerts**

Use Kibana’s dashboards to analyze logs related to network traffic, system processes, and user activities associated with the Sliver implant.

## ⚠️ **Disclaimer**

This project is intended for **educational purposes only**. The tools and techniques demonstrated should only be used in environments where you have explicit permission to perform penetration testing or security assessments. Unauthorized use of these tools may violate laws and regulations.

## 🔧 **Tools Used**

- **Ludus Platform**: A cloud-based platform for deploying cybersecurity labs.
- **NHA-GOAD**: A pre-configured Active Directory environment designed for attack and defense simulations.
- **Elastic Security (SIEM)**: A powerful tool for real-time threat detection and monitoring.
- **Sliver C2 Framework**: An open-source command-and-control framework used by attackers for post-exploitation tasks.

Thank you for visiting! Be sure to subscribe to [I.T Security Labs on YouTube](https://www.youtube.com/@ITSecurityLabs) for more tutorials and updates!