
# Step 1 — VLAN Lab Setup in Hyper-V

In this step, I created a segmented lab network inside Hyper-V using VLANs.  
This allows me to simulate attacker and target environments without exposing them to my real network.

---

## 1. VLAN & IP Plan

| Segment | VLAN ID | Example Subnet | Purpose |
|---------|---------|----------------|---------|
| Users   | 10      | 192.168.10.0/24 | Windows 10 client(s) |
| Servers | 20      | 192.168.20.0/24 | Windows Server (phishing target) |
| Kali    | 10      | 192.168.10.0/24 | Attacker machine |

---

## 2. Create Virtual Switch

- Open **Hyper-V Manager → Virtual Switch Manager**.  
- Add a **Private** switch named `LabSwitch`.  
  *(Private keeps traffic isolated; Internal allows host ↔ VM if needed).*
  
<img width="720" height="682" alt="Screenshot 2025-09-04 104714" src="https://github.com/user-attachments/assets/c8bf1db5-c4b7-43fc-8fff-ac3081d7ef19" />


---

## 3. Attach VMs & Assign VLANs

For each VM:
- Open **Settings → Network Adapter → VLAN**.  
- Enable VLAN ID and assign:

- **Win10 Client** → VLAN 10  
- **WinServer** → VLAN 20  
- **Kali** → VLAN 10
- 
<img width="724" height="692" alt="Screenshot 2025-09-04 104936" src="https://github.com/user-attachments/assets/05bff194-c0bf-4280-9b03-e3ddc6153dc0" />


---

## 4. Configure Static IPs

Without a router, only same-VLAN traffic works.

**Windows (PowerShell):**
```powershell
# Win10 in VLAN 10
New-NetIPAddress -InterfaceAlias "Ethernet" -IPAddress 192.168.10.10 -PrefixLength 24

# WinServer in VLAN 20
New-NetIPAddress -InterfaceAlias "Ethernet" -IPAddress 192.168.20.10 -PrefixLength 24

Kali (Linux):

sudo ip addr add 192.168.10.20/24 dev eth0
sudo ip link set eth0 up

📸 [Placeholder: Screenshot of ipconfig / ip a outputs]


---

5. Test Isolation

From Win10 (192.168.10.10) → ping Kali (192.168.10.20) → ✅ works

From Win10 (192.168.10.10) → ping WinServer (192.168.20.10) → ❌ fails


📸 [Placeholder: Screenshot of ping success and ping fail]


---

(Optional) 6. Inter-VLAN Routing

Later, I can add a Router VM (pfSense or Windows RRAS) with NICs in each VLAN:

VLAN 10 → 192.168.10.1/24

VLAN 20 → 192.168.20.1/24


Then set each VM’s Default Gateway to the router IP.
This enables cross-VLAN traffic when I want to test phishing simulations.

📸 [Placeholder: Screenshot of router VM with dual NICs configured]


---

✅ With this setup, I now have isolated VLANs in Hyper-V. This simulates real-world network segmentation, giving me a safe environment to practice phishing and detection scenarios.
