# Setting up a Whonix Gateway in XCP-NG

So since I didnt find a Guide, here is one.

# Disclaimer

This installation is NOT comming with any kind of garuentees, we will have to wiggle around a bit with the whonix config and disable certian checks, also no Time spoofing, possibly more issues.
Recomidation get something like Pfsense as a Virtual Router infornt of the Whonix Gateway to avoid that.

# What you need

1. https://www.whonix.org/wiki/KVM#Download_Whonix yep the KVM one we convert that later
2. XCP-NG with XAO cause clicking is less painfull https://xcp-ng.org/
3. qemu-img, apperntly also avaliable in Windows, only tested on Debian Linux
4. Some time

# Actual Setup

# Step 1

After you downloaded it <br />
Decompress the xz file properly with<br />
``
qemu-img convert -O raw whonix-gateway.qcow2 whonix-gateway.raw 
``<br />
Note that that can take some time, nope no progress bar, probably not stuck, just wait.<br />
If Problemse arrise reffernce the Doku<br />
https://www.whonix.org/wiki/KVM#Decompress<br />
If you are using Windows use Winrar, or something, not tested!

# Step 2

Now we have got something similar to this

``
WHONIX_BINARY_LICENSE_AGREEMENT
WHONIX_DISCLAIMER
Whonix-Gateway.xml
Whonix-Workstation.xml
Whonix_external_network.xml
Whonix_internal_network.xml
Whonix-Gateway-Xfce-17.2.8.5.Intel_AMD64.qcow2
Whonix-Workstation-Xfce-17.2.8.5.Intel_AMD64.qcow2
``

You can delete anything apart from
Whonix-Gateway-Xfce-17.2.8.5.Intel_AMD64.qcow2
though obviously read the WHONIX_DISCLAIMER and make sure you understand and agree, is binding either way.

The XML files are not usefull to use, though they can help if problems arrise so you would be wise to keep them too.
They have the configuration for the Networks we will have to create later and also for the VM.

# Step 3

So no we have to make the .qcow2 Virtual Hardrive into something that our XCP-NG server can actually work with.

``
qemu-img convert -O vpc Whonix-Gateway*.qcow2 Whonix-Gateway.vhd
``

the Whonix-Gateway.vhd is now what we will use from now on.

# Step 4

Get the vhd into your XCP-NG Server
!NOTE THAT TAKES A WHILE!
![Screenshot_2025-04-29_13-39-05](https://github.com/user-attachments/assets/119f56bb-255d-4449-b644-58804d3a20b7)



