<p align="center">
<img src="https://i.imgur.com/115agLY.png" alt="network"/>
</p>

Project: Simulate a Network Attack and Secure a Small Network

In this project, you will simulate a network attack, analyze the security risks, and then secure the network using a firewall and VLANs (Virtual Local Area Networks). The goal is to give you hands-on experience with both offensive and defensive security techniques. You'll learn how to launch basic attacks on a network and then implement security measures to protect against such attacks.

- Tools You Will Need:
1. VirtualBox or VMware: To set up a virtual network environment with multiple VMs.
2. Kali Linux: As the attacker machine, which has tools for penetration testing.
3. Ubuntu Server or Windows: As the target machine(s) for the attack.
4. pfSense or UFW (Uncomplicated Firewall): As the firewall solution for securing the network.
5. Wireshark: To analyze network traffic.
6. Router (Virtual or physical, e.g., within pfSense) to simulate a real network.
7. GNS3 or Cisco Packet Tracer: To simulate VLAN configurations if not using actual hardware.

Network Diagram Setup:
You’ll create a virtual network with:

- Attacker (Kali Linux) on a different network segment.
- Victim (Ubuntu/Windows Server) on a private network.
- Firewall (pfSense or UFW) between the attacker and victim to enforce security rules.
- VLANs to isolate network traffic.
Step 1: Set Up the Virtual Network

1.1: Install VirtualBox and Create Virtual Machines
1. Download and install VirtualBox or VMware.
2. Create a new VM for Kali Linux:
- Download the Kali Linux ISO from the official website.
- Create a VM with at least 2 GB of RAM and 20 GB of storage.
- Attach the Kali Linux ISO as a bootable medium and install it.
3. Create a second VM for the target (Ubuntu or Windows):
- Install either Ubuntu Server or Windows as your target VM.
- Provide it with similar resources as the attacker VM (2 GB RAM and 20 GB storage).
4. Create a third VM for pfSense (if using a virtual firewall):
- Download the pfSense ISO.
- Create a third VM with at least 1 GB of RAM and 8 GB of storage.
- Install pfSense to act as the firewall.

1.2: Network Configuration
1. In VirtualBox, configure the network adapters:
- Set Kali Linux to be on a NAT Network (Attacker Network).
- Set the Victim VM on an internal network (Private Network).
- Assign pfSense two interfaces:
  - One connected to the NAT network.
  - The other connected to the internal network where the victim is located.

This simulates a scenario where the attacker is on a different network than the victim, and a firewall protects the victim's network.

2. Ensure that the Kali Linux VM can reach the pfSense firewall but not directly reach the victim.

Step 2: Simulate a Network Attack

In this step, you’ll use Kali Linux to launch a basic network attack on the victim machine. For this project, we’ll simulate a Man-in-the-Middle (MITM) attack using a tool like Ettercap or ARP spoofing.

2.1: Launch ARP Spoofing Attack
1. Open the terminal on Kali Linux.

2. Use Ettercap to perform an ARP spoofing attack, allowing you to intercept traffic between the victim and the router (pfSense).

  - First, scan for the victim’s IP address by using arp-scan:
bash
sudo apt-get install arp-scan
sudo arp-scan --interface=eth0 192.168.x.x/24

  - Once you have the victim’s IP, use Ettercap to initiate ARP spoofing:
bash
sudo ettercap -Tq -i eth0 -M arp:remote /192.168.x.x/ /192.168.x.x/

Replace the IP addresses with the victim and router IP addresses. This command will allow you to act as a man-in-the-middle, intercepting traffic.

3. Use Wireshark on the Kali machine to observe the victim’s traffic. You’ll see that you can capture data such as unencrypted credentials being sent across the network.

Step 3: Secure the Network Using a Firewall and VLAN

3.1: Install and Configure pfSense Firewall

1. If you haven’t already, set up pfSense on your VM. Access the pfSense web interface by navigating to its IP address in a browser.

- Username: admin
- Password: pfsense

2. Create firewall rules to block unauthorized access:

- Go to Firewall > Rules and create a rule to block all inbound traffic from the attacker’s network (the NAT network).
- Add a rule to allow internal traffic between the victim and trusted sources (e.g., DNS and legitimate servers).
  
3. Test the firewall by attempting to ping the victim machine from the attacker machine. If configured correctly, the firewall should block these requests.

3.2: Configure VLANs for Network Segmentation
Virtual Local Area Networks (VLANs) help isolate different types of traffic to reduce the attack surface.

1. Access the pfSense VLAN settings:

- Go to Interfaces > Assignments, and click on the VLANs tab.
- Create a VLAN for the Victim’s network and another for Admin traffic.

2. Assign VLAN IDs to traffic:

- Assign VLAN IDs to segregate traffic, for example:
  - VLAN 10 for general user traffic (e.g., the victim).
  - VLAN 20 for administrator traffic.
  
3. Configure Inter-VLAN Routing:

- Allow restricted communication between VLANs where needed, such as allowing admin traffic (VLAN 20) to access VLAN 10, but blocking general user traffic from VLAN 20.
  
4. Test the VLAN segmentation by trying to access resources across VLANs from the attacker machine. If VLANs are correctly configured, you should not be able to access the victim’s VLAN.

Step 4: Monitor and Analyze Network Traffic

4.1: Use Wireshark to Monitor Traffic
1. Open Wireshark on the Kali machine.
2. Capture the network traffic to verify that the firewall is blocking attacks and that VLAN segmentation is working.
3. Analyze the traffic to see if any unauthorized traffic or attacks are getting through.

4.2: Enable Intrusion Detection/Prevention (Optional)
If you’re using pfSense, enable Snort or Suricata for Intrusion Detection and Prevention:

1. Go to Services > Snort and install the package.
2. Set up Snort rules to detect common attacks like ARP spoofing, DDoS attempts, or suspicious network traffic.
3. Test these rules by running additional attack simulations from Kali Linux.

Step 5: Document the Attack and Mitigation Steps

5.1: Document the Attack
Write a detailed report of the attack simulation:

- The tools used (Ettercap, Wireshark).
- The methods of attack (ARP spoofing).
- The impact of the attack (intercepting traffic, capturing credentials).
  
5.2: Document the Network Security Measures
1. Firewall Rules: Explain the rules you created in pfSense to block unwanted traffic.
2. VLAN Configuration: Explain how VLANs were configured to isolate traffic and reduce the attack surface.
3. Intrusion Detection (if used): Document any alerts or detections that Snort or Suricata picked up.

Step 6: Clean Up Resources
Once you’ve completed the project, it’s important to clean up your environment:

1. Shutdown and remove the virtual machines in VirtualBox or VMware.
2. Delete any unnecessary configurations in VirtualBox, including the network interfaces.
3. Remove the Wireshark logs if not needed for further analysis.

Project Summary
By the end of this project, you will have accomplished the following:

- Set up a virtual network with multiple machines.
- Launched a Man-in-the-Middle (MITM) attack using ARP spoofing.
- Used Wireshark to capture and analyze network traffic.
- Secured the network with a firewall and VLAN segmentation.
- Optionally, implemented intrusion detection with tools like Snort or Suricata.

This project gives you practical experience in both offensive (penetration testing) and defensive (network security) techniques, preparing you for real-world scenarios in network security.
