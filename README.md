# Setting up a Whonix Gateway in XCP-ng

Since I didn't find a proper guide, here is one.

# Disclaimer

This installation comes with **NO guarantees**.
We will have to wiggle around Whonix's configuration, disable certain checks, and accept that there is no time spoofing or full feature set.
More issues may arise.

**Recommendation:** Deploy something like **pfSense** as a virtual router *in front* of the Whonix Gateway to limit problems.

# What You Need

1. Download the KVM version of Whonix: [Whonix KVM Download](https://www.whonix.org/wiki/KVM#Download)
2. XCP-ng with XOA (Xen Orchestra) - [XCP-ng](https://xcp-ng.org/)
3. `qemu-img` - available on Linux and Windows (tested on Debian Linux)
4. Some free time (and patience)

# Actual Setup

## Step 1 — Decompress the Image

After downloading the `.xz` file, decompress it properly:

```bash
tar -xvf Whonix*.libvirt.xz
```

**Note:** This can take a while. No progress bar. It’s probably not stuck, just wait.

If problems arise, reference the official documentation:  
https://www.whonix.org/wiki/KVM#Decompress

If you're using Windows, use WinRAR or a similar tool — **not tested**.

## Step 2 — Prepare Files

You should now have files like:

```
WHONIX_BINARY_LICENSE_AGREEMENT
WHONIX_DISCLAIMER
Whonix-Gateway.xml
Whonix-Workstation.xml
Whonix_external_network.xml
Whonix_internal_network.xml
Whonix-Gateway-Xfce-17.2.8.5.Intel_AMD64.qcow2
Whonix-Workstation-Xfce-17.2.8.5.Intel_AMD64.qcow2
```

Delete everything **except** the `Whonix-Gateway-Xfce-*.qcow2` file.  
(**Keep** the XML files for troubleshooting if needed.)

Make sure you read the WHONIX_DISCLAIMER.

## Step 3 — Convert to VHD

Convert the `.qcow2` image into `.vhd` format for XCP-ng:

```bash
qemu-img convert -O vpc Whonix-Gateway*.qcow2 Whonix-Gateway.vhd
```

The output `Whonix-Gateway.vhd` will be used from now on.

## Step 4 — Upload the VHD

Upload the `.vhd` file to your XCP-ng server.

**Warning:**
- Uploading takes time.
- Ensure you have **at least 100 GB** free space.

![Screenshot_2025-04-29_13-39-05](https://github.com/user-attachments/assets/119f56bb-255d-4449-b644-58804d3a20b7)

## Step 5 — Create Networks

You need two networks:

- **External Network:** Connected to `eth0` or routed through pfSense, for Internet access.
- **Internal Network:** NOT connected to any physical interface — only used inside XCP-ng.

![Screenshot_2025-04-29_14-00-30](https://github.com/user-attachments/assets/9492c0e0-daf3-48ae-83b8-cc1a92f33ba9)

Internal Network setup:

![Screenshot_2025-04-29_14-04-36](https://github.com/user-attachments/assets/98303a40-413e-4b3d-a39a-e30d45f10cd0)

## Step 6 — Setup the VM

- Create a new VM (any Linux template is fine).
- ISO selection doesn't matter — we won't boot from ISO.

![Screenshot_2025-04-29_14-10-57](https://github.com/user-attachments/assets/a39a1df8-5a72-4483-8bb0-e21e82494c32)

- Attach the `Whonix-Gateway.vhd` as the VM’s disk.

![Screenshot_2025-04-29_14-14-52](https://github.com/user-attachments/assets/d017ce4d-77e5-4b27-9217-c4375c740eac)

Start the VM.

## Step 7 — Setting the IP Address (Properly)


Inside the Gateway, open the network config:

```bash
sudo nano /etc/network/interfaces.d/30_non-qubes-whonix
```

Edit it like this:

```
auto eth0
iface eth0 inet static
    address 192.168.178.150
    netmask 255.255.255.0
    gateway 192.168.178.1 
```

**Notes:**
- Replace `192.168.178.x` with your actual network range.
- To find your network info, use `ip a` (Linux) or `ipconfig` (Windows) on your current machine.
- Make sure the static IP you set is **not already in use** (ping it first).

Save the file with `CTRL+X`, then `Y` (or `Z` on QWERTZ keyboards). Reboot:

```bash
sudo reboot
```

![Screenshot_2025-04-29_14-41-39](https://github.com/user-attachments/assets/bbe970d3-d8ed-49f5-8576-d73fbcbcb7bf)

## Step 8 — Example Usage (Windows 10 VM)

You can now create other VMs to route traffic through the Whonix Gateway.

- Create a Windows 10 VM (or any OS).
- Attach it **only to the Internal Network**.

![Screenshot_2025-04-29_15-04-57](https://github.com/user-attachments/assets/3cb5a40c-76c6-4272-89e1-f964260275a6)

Inside Windows:
- Set static IP settings manually:
  - IP Address: `10.152.152.11`
  - Subnet: `255.255.255.0`
  - Gateway: `10.152.152.10` (Gateway's Internal NIC IP)

![Screenshot_2025-04-29_15-09-23](https://github.com/user-attachments/assets/d5a0b6dd-1448-43a0-80af-c80de4a44b8d)

Now all traffic from your VM routes through Tor via Whonix Gateway.

---

**Enjoy — or cry.**

Setting up networking manually requires patience and testing if you're not familiar with it.
