## **07 - Deploying Adaptix C2 in Ludus - Purple Team Cyber Range with NHA-GOAD**

This guide accompanies the **seventh video** in your series, focusing on deploying the **Adaptix C2 framework** within the Ludus platform for offensive security operations in the **NHA-GOAD** lab.

#  **Key Takeaways**

## 1. **What is Adaptix C2?**

Adaptix is a command-and-control (C2) framework designed for red team operations, featuring SSL encryption, BOF,plugin support, and cross-platform compatibility[1](https://github.com/badsectorlabs/ludus_adaptix_c2)[4](https://adaptix-framework.gitbook.io/adaptix-framework/adaptix-c2/getting-starting/starting). It integrates seamlessly with Ludus via Ansible roles for automated deployment[3](https://docs.ludus.cloud/docs/roles).

## 2. **Deployment Workflow**

- Add the Adaptix Ansible role to Ludus.
    
- Configure VMs for server/client installation.
    
- Deploy the range and validate services.
    
- Launch the Adaptix client on Kali.
    

# **Step-by-Step Deployment Guide**

## **1. Add the Adaptix Role to Ludus**
Assuming you already have ludus installed and running

```bash
ludus ansible role add badsectorlabs.ludus_adaptix_c2
```

This installs the Adaptix role from Ansible Galaxy[3](https://docs.ludus.cloud/docs/roles).

## **2. Configure Adaptix Server/Client VMs**

Create `adaptix_c2_config.yml` with:
```yml
ludus:
  - vm_name: "{{ range_id }}-adaptix-server"
    hostname: "{{ range_id }}-adaptix"
    template: debian-12-x64-server-template
    vlan: 99
    ip_last_octet: 1
    ram_gb: 4
    cpus: 2
    linux: true
    roles:
      - badsectorlabs.ludus_adaptix_c2
    role_vars:
      ludus_adaptix_c2_install_server: true

  - vm_name: "{{ range_id }}-kali-2"
    hostname: "{{ range_id }}-kali-2"
    template: kali-x64-desktop-template
    vlan: 99
    ip_last_octet: 2
    ram_gb: 4
    cpus: 2
    linux: true
    roles:
      - badsectorlabs.ludus_adaptix_c2
    role_vars:
      ludus_adaptix_c2_install_client: true

```

## **3. Deploy the Range**
```bash
ludus range config set -f adaptix_c2_config.yml
ludus range deploy
ludus range logs -f  # Monitor progress
```

## **4. Post-Deployment Setup**

## **On Adaptix Server:**

```bash
# Generate SSL certs (required for server)
sudo /opt/adaptix/ssl_gen.sh

# Start Adaptix server
sudo systemctl start adaptix-server
```

## **On Kali Client:**

```bash
# Launch Adaptix GUI client
adaptixclient
```
- **Auth Fields**:
    
    - Endpoint: `https://<SERVER_IP>:443`
        
    - Password: [From role defaults/server config]
        
    - Nickname: [Your operator name]
        
## **5. Validate Deployment**

```bash
# Check Adaptix server status
ssh ludus@adaptix-server "systemctl status adaptix-server"

# Verify client connectivity
tail -f ~/.adaptix/adaptix-client.log

```

## ⚠️ **Critical Notes**

- **SSL Certificates**: Adaptix requires valid SSL certs for secure communication[4](https://adaptix-framework.gitbook.io/adaptix-framework/adaptix-c2/getting-starting/starting). Use the included `ssl_gen.sh` script or provide your own.
    
- **Firewall Rules**: Ensure VLAN 99 allows traffic between server/client VMs.
    
- **Role Variables**: Customize `role_vars` for advanced configurations like custom ports or plugins[3](https://docs.ludus.cloud/docs/roles).
    

## 🛠️ Bypassing Gopher Agent Build Error in Adaptix C2 Ansible Playbook

## ❗ Error Context

During Adaptix C2 deployment, the following Ansible task may fail with a `MODULE FAILURE` due to Go build timeouts or environment issues:

```text
- name: Build the gopher agent for Linux and macOS to "warm" the Go build cache...
  ansible.builtin.shell: |
    cd /opt/adaptix/dist/extenders/agent_gopher/src_gopher
    go mod tidy
    CGO_ENABLED=0 GOOS=linux GOARCH=amd64 go build -trimpath -ldflags="-s -w" -o agent -buildvcs=false && rm agent
    CGO_ENABLED=0 GOOS=linux GOARCH=arm64 go build -trimpath -ldflags="-s -w" -o agent -buildvcs=false && rm agent
    CGO_ENABLED=0 GOOS=darwin GOARCH=arm64 go build -trimpath -ldflags="-s -w" -o agent -buildvcs=false && rm agent
  become: true
  become_user: adaptix

```

## 🧑‍💻 Manual Cache Warming (Prevents JWT Token Errors)

1. **SSH into the Adaptix server.**
    
2. **Run these commands to pre-build the agent and warm the Go cache:**
```bash
sudo su - adaptix
cd /opt/adaptix/dist/extenders/agent_gopher/src_gopher
CGO_ENABLED=0 GOOS=linux GOARCH=amd64 go build -trimpath -ldflags="-s -w" -o agent -buildvcs=false && rm agent
CGO_ENABLED=0 GOOS=linux GOARCH=arm64 go build -trimpath -ldflags="-s -w" -o agent -buildvcs=false && rm agent
CGO_ENABLED=0 GOOS=darwin GOARCH=arm64 go build -trimpath -ldflags="-s -w" -o agent -buildvcs=false && rm agent

```

## ✏️ Modify the Ansible Playbook

Edit the Ludus role’s task file  
`roles/badsectorlabs.ludus_adaptix_c2/tasks/build_server.yml`  
to **ignore errors for this task**:

This file will be in `/opt/ludus/users/<yourUser>/.ansible/roles/badsectorlabs.ludus_adaptix_c2/tasks` where `yourUser` is the ludus user you are using to deploy the lab under

```text
- name: Build the gopher agent for Linux and macOS to "warm" the Go build cache...
  ansible.builtin.shell: |
    # ... (existing commands)
  become: true
  become_user: adaptix
  ignore_errors: yes  # <-- Add this line

```

## ✅ Result

- The playbook will **proceed even if this task fails**.
![](../../05%20Images/Pasted%20image%2020250517220025.png)
- Subsequent tasks (server setup, client installation) will execute normally.
    
- Manual cache warming ensures the JWT token error is resolved.
    

> [!note]  
> This is a temporary workaround. Remove `ignore_errors` once the underlying build issue is fixed.

##  **Tools Used**

- **Ludus Platform**: Automated purple team environment deployment[2](https://minder-security.ghost.io/ludus-build-a-purple-teaming-test-environment/)[3](https://docs.ludus.cloud/docs/roles)
    
- **Adaptix C2**: Command-and-control framework[1](https://github.com/badsectorlabs/ludus_adaptix_c2)[4](https://adaptix-framework.gitbook.io/adaptix-framework/adaptix-c2/getting-starting/starting)
    
- **Ansible**: Infrastructure-as-code role management[3](https://docs.ludus.cloud/docs/roles)[5](https://docs.ludus.cloud/docs/developers/ansible-roles)
    
[Subscribe for updates](https://www.youtube.com/@ITSecurityLabs)