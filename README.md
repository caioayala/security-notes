# Networking Basics

## Network Order (top to bottom)
Cloud → Router → Switch → Devices

## IP Address
- 4 octets, each ranging from 0 to 255
- Example: 192.168.1.1

## Subnets
An IP address is used in three ways within a subnet:
- Identify the **network address**
- Identify the **host address**
- Identify the **default gateway**

Subnetting = dividing a network into smaller pieces.
Home networks are subnets — hosts can use any address from 0 to 254.

## Key Protocols

### ARP (Address Resolution Protocol)
Maps a known IP address to an unknown MAC address within a local network.
Devices broadcast "who has this IP?", the owner replies with its MAC.
Security note: ARP has no authentication, vulnerable to ARP Spoofing.

### DHCP (Dynamic Host Configuration Protocol)
Automatically assigns IP addresses to devices using a client-server model.
Process (DORA):
1. Discover —> client broadcasts looking for a DHCP server
2. Offer —> server offers an available IP
3. Request —> client accepts the offer
4. Acknowledge —> server confirms the assignment
IPs are temporary (typically 24h leases).

## OSI Model

| Layer | Name         | Description |
|-------|--------------|-------------|
| 7     | Application  | User-facing interface —> determines data access (send/receive) |
| 6     | Presentation | Data translation, compression, encryption (SSL/TLS operates here) |
| 5     | Session      | Creates, maintains, and terminates connections between devices |
| 4     | Transport    | Data transmission —> TCP (reliable, error-checked) vs UDP (fast, no guarantee) |
| 3     | Network      | Routing packets via IP addresses —> path determined by routing protocols (OSPF, BGP) |
| 2     | Data Link    | Adds physical MAC address of receiving endpoint —> MACs can be spoofed |
| 1     | Physical     | Hardware layer —> electrical signals over cables, binary transmission |

## Network Interface Card (NIC)
Every device has a NIC with a unique MAC address used for physical identification on the network.

## Security Notes
- MAC addresses can be spoofed (Layer 2 attack vector)
- ARP has no authentication — ARP Spoofing enables man-in-the-middle attacks
- DHCP Starvation: attacker exhausts IP pool, denying addresses to legitimate devices
