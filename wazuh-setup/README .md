# Wazuh Manager + Agent Setup

## Objective
Install and configure the Wazuh Manager on Kali Linux and deploy a Wazuh Agent on an Ubuntu endpoint so both machines can communicate and forward security events to the Wazuh dashboard.

## Tools Used
- Kali Linux (Wazuh Manager/Server)
- Ubuntu (Wazuh Agent/Endpoint)
- VirtualBox
- Tailscale (for networking between VMs) or just use your  Kali machine's IP address.

---

## VirtualBox Settings (Before You Start)

Get these right before installation or the Wazuh wizard will fail hardware checks.

**Both VMs:**
- Shared Clipboard: Bidirectional
- Drag and Drop: Bidirectional
- Base Memory: minimum 4000 MB

**Kali Linux (Wazuh Manager):**
- Processors: 3

**Ubuntu (Wazuh Agent):**
- Processors: 2

> Take a snapshot of your current VM state before starting installation. If anything goes wrong, you can refer back to the snapshot instead of starting over.

---

## Part 1 — Install Wazuh Manager (on Kali Linux)

### 1. Update your machine

```bash
sudo apt update
```

### 2. Install curl (Ubuntu only — already on Kali by default)

```bash
sudo apt install curl
```

### 3. Download and run the Wazuh installation assistant

```bash
curl -sO https://packages.wazuh.com/4.14/wazuh-install.sh && sudo bash ./wazuh-install.sh -a
```

> This takes time — be patient and let it run. If you are on Kali, you will get a warning about the system not matching recommended systems. Ignore it and let the script continue.

![Wazuh Installation via curl](images/wazuh-install.png)

### 4. Save your credentials

When installation finishes, your admin password will be displayed in the terminal. Save it — you will need it to log into the dashboard.

```
User: admin
Password: <generated-password>
```

### 5. Disable the Wazuh repository (run as root)

```bash
sudo su
sed -i "s/^deb /#deb /" /etc/apt/sources.list.d/wazuh.list
apt update
```

### 6. Access the Wazuh dashboard

Open your browser and navigate to:

```
https://<your-tailscale-ip>
```

Log in with `admin` and the password from step 4.

![Wazuh Dashboard](images/wazuh-dashboard.png)

### 7. Restart Wazuh services (run this every time you boot the VM)

```bash
sudo systemctl restart wazuh-manager
sudo systemctl restart wazuh-indexer
sudo systemctl restart wazuh-dashboard
```

---

## Part 2 — Install Wazuh Agent (on Ubuntu)

### 1. Click "Deploy new agent" on the Wazuh dashboard

From the Overview page, click **Deploy new agent**.

### 2. Select your endpoint architecture

Choose **Linux → DEB amd64** for Ubuntu.

### 3. Enter your Wazuh server address

Put in your Kali machine's Tailscale IP address or just your  Kali machine's IP address  and toggle on **Remember server address**.

### 4. Assign an agent name

Give your agent a unique name — do not leave it blank. Using the hostname automatically can cause errors.

> Agent name used: `Tsacademy`
> Note: The agent name cannot be changed after enrollment.

### 5. Run the generated install command on the Ubuntu machine (as root)

The dashboard generates a command for you. Copy it and run it on your Ubuntu terminal as root. It looks like this:

```bash
wget https://packages.wazuh.com/4.x/apt/pool/main/w/wazuh-agent/wazuh-agent_4.x.x-1_amd64.deb \
  && sudo WAZUH_MANAGER='<tailscale-ip>' WAZUH_AGENT_NAME='Tsacademy' \
  dpkg -i ./wazuh-agent_4.x.x-1_amd64.deb
```

### 6. Start the Wazuh agent

```bash
sudo systemctl daemon-reload
sudo systemctl enable wazuh-agent
sudo systemctl start wazuh-agent
```

![Wazuh Agent Added](images/wazuh-agent.png)

---

## Result

Wazuh Manager installed and accessible via the dashboard. Wazuh Agent (`Tsacademy`) enrolled, running, and reporting to the manager. The endpoint is now being monitored and security events will appear in the Wazuh dashboard.

---

## Network Note

A strong, stable internet connection is required during installation. The Wazuh install script downloads several packages and will fail or stall on a weak connection.

---

## Analyst
**Nwabueze Benita**  
[GitHub](https://github.com/IAMBENITA) | [LinkedIn](https://linkedin.com/in/benitanwabueze)
