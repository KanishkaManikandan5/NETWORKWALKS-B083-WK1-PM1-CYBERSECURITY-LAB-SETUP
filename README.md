# 🔐 Cybersecurity Lab Environment Setup

**Building an isolated virtual lab for penetration testing and ethical hacking practice**

---

## 📌 Project Overview

This project focuses on setting up a **virtual cybersecurity and penetration-testing laboratory** using VirtualBox and Kali Linux.

The purpose of the lab is to create a controlled environment where cybersecurity tools, network scanning, reconnaissance, vulnerability assessment, and other security-testing activities can be performed safely and repeatedly.

The lab is configured on a private virtual network so that additional machines can be added later and used as targets for authorized security testing.

---

## 🎯 Objectives

The main goals of this build were to:

- Install and configure VirtualBox as the hypervisor
- Import Kali Linux as a virtual machine
- Create a dedicated NAT Network for the lab
- Configure Kali's network connectivity
- Assign Kali a consistent, static IP address
- Confirm network connectivity and DNS resolution
- Capture a clean VM snapshot as a recovery point
- Document the full process, screenshots included
- Leave the environment ready for future cybersecurity practicals

---

## 🔗 Tools & Resources

- **7-Zip:** <https://7-zip.org/download.html>
- **VirtualBox:** <https://virtualbox.org/wiki/Downloads>
- **Kali Linux:** <https://kali.org/get-kali>

---

## ⚙️ Lab Configuration

| 🧩 Component        | ⚙️ Configuration   |
| ------------------- | ------------------ |
| 🖥️ Host OS           | Windows 11          |
| 🧰 Hypervisor        | VirtualBox 7.2      |
| 🐉 Security OS       | Kali Linux          |
| 🌐 Virtual Network   | NAT Network         |
| 📡 Network Address   | 10.0.0.0/24         |
| 🐧 Kali IP Address   | 10.0.0.2/24         |
| 🚪 Default Gateway   | 10.0.0.1            |
| 🌍 DNS Server        | 8.8.8.8             |

---

# 🪜 Lab Setup Procedure

## Step 1. Install 7-Zip

7-Zip was installed first, since the Kali Linux VM package is distributed as a compressed archive and needs extracting before it can be imported.

**Tool:** 7-Zip
![image alt](https://github.com/KanishkaManikandan5/NETWORKWALKS-B083-WK1-PM1-CYBERSECURITY-LAB-SETUP/blob/28334f1c2bd083e850c0b0092cd9f22166701b55/1%207ZIP%20SCREENSHOT.jpg)


## Step 2. Install VirtualBox

VirtualBox itself, along with the matching Extension Pack, was downloaded from the official site and installed as the hypervisor.

![VirtualBox download page](https://github.com/KanishkaManikandan5/NETWORKWALKS-B083-WK1-PM1-CYBERSECURITY-LAB-SETUP/blob/fa65e857a4da855e098a6f5c3b81dca94b1458b2/2%20virtual%20box%20screenshot.jpeg)

---

## Step 3. Create the NAT Network

A dedicated NAT Network was created inside VirtualBox with the following settings:

```
Network Name: NatNetwork
IPv4 Prefix:  10.0.0.0/24
DHCP:         Enabled
IPv6:         Disabled
```

![NAT Network settings](https://github.com/KanishkaManikandan5/NETWORKWALKS-B083-WK1-PM1-CYBERSECURITY-LAB-SETUP/blob/fa65e857a4da855e098a6f5c3b81dca94b1458b2/3%20network%20screenshot.jpeg)

A **NAT Network** (rather than plain NAT) was the right choice here, since machines attached to the same NAT Network can reach each other directly while still getting outbound internet access — exactly what's needed once attacker and target VMs are both added to the lab.

---

## Step 4. Import Kali Linux

The Kali Linux archive (~15 GB) was extracted with 7-Zip and imported into VirtualBox as a new VM.

![Importing Kali Linux into VirtualBox](https://github.com/KanishkaManikandan5/NETWORKWALKS-B083-WK1-PM1-CYBERSECURITY-LAB-SETUP/blob/fa65e857a4da855e098a6f5c3b81dca94b1458b2/4%20oracle%20vmscreenshot.jpeg)

The VM's network adapter was attached to the NAT Network created in Step 3, and shared folders, clipboard, and drag-and-drop were enabled between host and guest.

After booting, a quick `ifconfig` check showed Kali had picked up a DHCP address (`10.0.0.4`) on `eth0` — confirming the NAT Network was working, but not yet the fixed address the lab needs.

![Kali ifconfig showing DHCP address](https://github.com/KanishkaManikandan5/NETWORKWALKS-B083-WK1-PM1-CYBERSECURITY-LAB-SETUP/blob/fa65e857a4da855e098a6f5c3b81dca94b1458b2/5%20wireless%20connection%20screenshot.jpeg)

---

## Step 5. Configure the Kali Linux Network

Kali's network settings were then switched from automatic to a consistent, manual configuration. Inside the Network Manager applet, **Wired connection 1** was opened, starting on the **Ethernet** tab to confirm the correct device.

On the **IPv4 Settings** tab, the method was changed to **Manual** and the following applied:

```
IP Address:  10.0.0.2
Subnet Mask: 255.255.255.0 (/24)
Gateway:     10.0.0.1
DNS:         8.8.8.8
```

A fixed address like this makes the lab far easier to document and reference in later exercises than a DHCP lease that could change.

Verified afterward with `ip a` / `ifconfig`, now showing the fixed `10.0.0.2` address:

![Kali IPverified as static 10.0.0.2](https://github.com/KanishkaManikandan5/NETWORKWALKS-B083-WK1-PM1-CYBERSECURITY-LAB-SETUP/blob/fa65e857a4da855e098a6f5c3b81dca94b1458b2/6%20ip%20screenshot.jpeg)

---

## Step 6. Create a Clean VM Snapshot

Once the network was confirmed working, a VirtualBox snapshot was taken to lock in this state as a known-good baseline:

```
Snapshot name: Clean Kali - Network Setup
```

If a future exercise breaks or misconfigures the VM, it can be rolled back to this point instead of rebuilding from scratch.

---

# 🔎 Lab Verification

| ✅ Test                       | 🧾 Command             | 🎯 Expected Result               |
| ----------------------------- | ---------------------- | -------------------------------- |
| 🌐 Check IP address            | `ip a` / `ifconfig`    | Correct Kali IP shown            |
| 📡 Test gateway                | `ping 10.0.0.1`        | Successful replies               |
| 🌍 Test internet connectivity  | `ping 8.8.8.8`         | Successful replies               |
| 🔎 Test DNS resolution         | `nslookup <any domain>`| Domain resolves                  |
| 🔄 Verify snapshot             | Restore snapshot, then `ip a` | Baseline config restored  |

---

# 🐞 Problems Encountered & Solutions

Keeping a record of what went wrong (and how it was fixed) is part of documenting this properly.

## Problem 1. Static IP not applying right away

The first attempt at setting the manual address didn't take effect immediately. This turned out to be because the change needs to be made specifically on the **IPv4 Settings** tab of the correct connection profile, followed by restarting the connection rather than just saving it.

## Problem 2. Large archive extraction time

The Kali VM package (~15 GB compressed) took a noticeable amount of time to extract with 7-Zip — worth factoring in if working to a deadline.

> **Note:** Interface and connection names (e.g. `eth0`, `Wired connection 1`) can differ between systems — always confirm the actual names on your own machine before running any commands.

---

# 💡 What I Learned

Building this lab was a practical introduction to virtual networking and lab documentation. The main things that stuck:

### 1. NAT vs NAT Network

A plain NAT adapter only lets a VM reach the outside world — it can't see other VMs. A NAT Network solves that by letting every VM attached to it talk to each other and reach the internet, which is essential once more machines join the lab.

### 2. Virtual machine networking

How VirtualBox's virtual adapters (type, MAC address, attachment mode) map through to what the guest OS actually reports and uses.

### 3. Static IP configuration

How to set and verify IPv4 addressing, subnet masks, gateway, and DNS inside Kali Linux using both `ifconfig` and `ip a`.

### 4. VM snapshots

Why it pays to snapshot a clean, working state before installing tools or making risky changes — it turns a mistake into a two-click rollback instead of a rebuild.

### 5. Documentation

How much clearer a setup becomes once it's written up step by step with screenshots, configuration values, and the problems hit along the way.

---

# 🔐 Security & Ethical Use

This laboratory is intended strictly for education purposes only.

---

# 👤 Author

**KANISHKA M**
Cybersecurity Intern B083

LinkedIn: <https://www.linkedin.com/in/kanishka-m525>

