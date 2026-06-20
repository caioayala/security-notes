# Networking Basics

## Network Architecture (top to bottom)

Cloud → Router → Switch → Devices

Physical cable design matters in LAN environments. A network should be structured
so that a single cable failure does not interrupt communication for all connected
devices. Redundant paths ensure packet delivery even under partial failure.

## IP Address

An IP address is a logical identifier assigned to each device on a network.
It consists of 4 octets, each ranging from 0 to 255, separated by dots.

Example: 192.168.1.1

IP addresses are not permanent by default. They can be dynamically assigned
by a DHCP server or manually configured as static addresses.

## Subnets

A subnet is a logical subdivision of a network. IP addresses within a subnet
serve three distinct purposes:

- Identify the network address
- Identify the host address
- Identify the default gateway

Subnetting is the process of dividing a larger network into smaller, more
manageable segments. This improves performance, organisation and security
by limiting broadcast domains.

Home networks are a common example of a subnet. Hosts can be assigned any
address from 0 to 254 within the local range.

## Key Protocols

### ARP (Address Resolution Protocol)

ARP operates at Layer 2 and resolves a known IP address to an unknown MAC
address within a local network. When a device wants to communicate with another
on the same subnet, it broadcasts an ARP request asking "who has this IP?".
The device with the matching IP replies with its MAC address. The result is
stored in an ARP cache to avoid repeating the process for every packet.

Security note: ARP has no authentication mechanism. Any device can reply to
an ARP request, making it vulnerable to ARP Spoofing, where an attacker
associates their MAC with a legitimate IP to intercept traffic.

### DHCP (Dynamic Host Configuration Protocol)

DHCP automates IP address assignment using a client-server model, eliminating
the need for manual configuration on each device. The process follows four
steps, commonly referred to as DORA:

1. Discover: client broadcasts a request looking for an available DHCP server
2. Offer: server responds with an available IP address
3. Request: client formally requests the offered IP
4. Acknowledge: server confirms the assignment and sets the lease duration

IP addresses assigned via DHCP are temporary, typically lasting 24 hours,
after which the device must renew or request a new address.

Security note: DHCP Starvation is an attack where an attacker floods the server
with requests using spoofed MAC addresses, exhausting the available IP pool and
denying addresses to legitimate devices. This is often a precursor to a rogue
DHCP server attack.

## OSI Model

The OSI (Open Systems Interconnection) model is a conceptual framework that
standardises how different network systems communicate. It divides network
communication into 7 layers, each with a specific responsibility.

| Layer | Name         | Description |
|-------|--------------|-------------|
| 7     | Application  | User-facing interface, determines data access (send/receive) |
| 6     | Presentation | Data translation, compression and encryption (SSL/TLS operates here) |
| 5     | Session      | Creates, maintains and terminates connections between devices |
| 4     | Transport    | Data transmission, TCP (reliable, error-checked) vs UDP (fast, no guarantee) |
| 3     | Network      | Routes packets via IP addresses, path determined by routing protocols (OSPF, BGP) |
| 2     | Data Link    | Adds physical MAC address of the receiving endpoint, MACs can be spoofed |
| 1     | Physical     | Hardware layer, electrical signals transmitted over cables in binary |

A practical way to memorise the layers from 7 to 1: "All People Seem To
Need Data Processing".

## Network Interface Card (NIC)

Every networked device contains a Network Interface Card, a hardware component
responsible for connecting the device to a network. Each NIC is assigned a
unique MAC (Media Access Control) address at the factory, used for physical
identification at Layer 2. Although MAC addresses are intended to be permanent,
they can be spoofed at the software level.

## TCP/IP Model

While the OSI model is the theoretical standard, the TCP/IP model is what
networks actually implement in practice. It condenses the 7 OSI layers into 4:

| TCP/IP Layer  | Equivalent OSI Layers |
|---------------|-----------------------|
| Application   | 5, 6, 7               |
| Transport     | 4                     |
| Internet      | 3                     |
| Network Access| 1, 2                  |

Understanding both models is important because documentation and tools
reference either one depending on context.

## Security Notes

- MAC addresses can be spoofed, making Layer 2 an unreliable trust boundary
- ARP Spoofing enables man-in-the-middle attacks by poisoning ARP caches
- DHCP Starvation exhausts the IP pool, enabling rogue DHCP server insertion
- Subnetting limits broadcast domains, reducing the blast radius of Layer 2 attacks
- Unencrypted protocols at Layer 7 (such as HTTP and FTP) expose data to
  interception at any layer below
