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

Get the vhd into your XCP-NG Server<br />
!NOTE THAT TAKES A WHILE! <br />
Note you should have atleast 100 GB more is recomended<br />
![Screenshot_2025-04-29_13-39-05](https://github.com/user-attachments/assets/119f56bb-255d-4449-b644-58804d3a20b7)


# Step 5

Create the Networks
The external one should the eth0 connected, or if you use a pfsense route that one, so that the gateway has actual internet
![Screenshot_2025-04-29_14-00-30](https://github.com/user-attachments/assets/9492c0e0-daf3-48ae-83b8-cc1a92f33ba9)

The external one should NOT be connected to an Interface this is the one you use to connect whonix gateway and what ever you want to connect with it
![Screenshot_2025-04-29_14-04-36](https://github.com/user-attachments/assets/98303a40-413e-4b3d-a39a-e30d45f10cd0)

# Step 6

Setup the VM
The configs should be the same, the Template doesnt rly matter that much, the ISO also can be what ever you want, we wont use it.
![Screenshot_2025-04-29_14-10-57](https://github.com/user-attachments/assets/a39a1df8-5a72-4483-8bb0-e21e82494c32)

Then attach the vhd Disk to the VM
![Screenshot_2025-04-29_14-14-52](https://github.com/user-attachments/assets/d017ce4d-77e5-4b27-9217-c4375c740eac)

Now start up the Gateway

and put int this command, note if you are using a qwertz Keyboard you will have to use the "-" key for "/"  
![Screenshot_2025-04-29_14-28-49](https://github.com/user-attachments/assets/20be9080-7800-4a55-a7e8-3362fa15588c)

Though it tells you to not change the Hardcoded IP though DHCP is not Working for me so that was the only way I could fix it, if you have better solutions feel free to share.
at address put in something like 192.168.178.XXX or 192.168.1.XXX depends on what kind of network I have a Type C Network.
if you put your write `` ip a `` or ``ipconfig`` to get Subnet mask and Gateway, ON YOUR MACHINE THAT YOU WORK ON RN, if you dont know what that is ask Chat-GPT or google. 
Also make sure that the ip that you set in Address is not already given try to ping it in the network if you get nothing back, good, if you do use a diffrent one, free of your choice as long as the Prefix is the same as your networks.
![Screenshot_2025-04-29_14-41-39](https://github.com/user-attachments/assets/bbe970d3-d8ed-49f5-8576-d73fbcbcb7bf)

then CTRL+X and CTRL+Y (or Z if you use qwertz Keyboard)  to save then type reboot to reboot the system.

# Step 7

Enjoy, or cry you will have to setup the Networking yourself, requires a bit of testing if you are not to familiar with it.
![Screenshot_2025-04-29_15-03-12](https://github.com/user-attachments/assets/6ed43309-39d6-4d45-bc25-7c2d118df078)

# Step 8 

Example usage.
A simple Windows 10 VM wich routes traffic through tor.

Add the INTERNAL-Network
![Screenshot_2025-04-29_15-04-57](https://github.com/user-attachments/assets/3cb5a40c-76c6-4272-89e1-f964260275a6)

You ahve to go into the Networking settings of the ethernet addapter and set the ips manually like here
![Screenshot_2025-04-29_15-09-23](https://github.com/user-attachments/assets/d5a0b6dd-1448-43a0-80af-c80de4a44b8d)




