# TCP, UDP and the Three-Way Handshake

## TCP vs UDP

TCP (Transmission Control Protocol) and UDP (User Datagram Protocol) are the two
main transport layer protocols (Layer 4 of the OSI model). They serve different
purposes depending on the use case.

| Feature         | TCP                              | UDP                          |
|-----------------|----------------------------------|------------------------------|
| Reliability     | Guaranteed delivery              | No guarantee                 |
| Speed           | Slower due to error checking     | Faster, no overhead          |
| Connection      | Connection-oriented              | Connectionless               |
| Error Checking  | Yes                              | No                           |
| Use Cases       | Web, SSH, FTP, email             | DNS, VoIP, video streaming   |

TCP is used when data integrity matters. UDP is used when speed matters more
than accuracy, such as live video or online gaming where a lost packet is
preferable to a delayed one.

## Three-Way Handshake

The three-way handshake is the process TCP uses to establish a connection
between two devices before any data is transmitted.

| Step | Message | Description |
|------|---------|-------------|
| 1    | SYN     | Client sends a synchronisation packet to initiate the connection |
| 2    | SYN/ACK | Server acknowledges the SYN and sends its own synchronisation packet |
| 3    | ACK     | Client acknowledges the server response, connection is established |
| 4    | DATA    | Actual data begins to be transmitted between devices |
| 5    | FIN     | Cleanly closes the connection after transmission is complete |
| *    | RST     | Abruptly terminates the connection, indicates an error or fault |

Security note: SYN Flood is a DoS attack that exploits the handshake by sending
large volumes of SYN packets without completing the process, exhausting server
resources.

## Ports

Ports define where a device is able to send and receive specific types of traffic.
Port numbers range from 0 to 65535. To avoid chaos in communication, developers
and organisations follow standardised port assignments.

### Common Ports

| Protocol                          | Port | Description |
|-----------------------------------|------|-------------|
| FTP (File Transfer Protocol)      | 21   | File sharing using a client-server model |
| SSH (Secure Shell)                | 22   | Secure remote login via text-based interface |
| HTTP (HyperText Transfer Protocol)| 80   | Powers the web, transmits text, images and video |
| HTTPS (HTTP Secure)               | 443  | Same as HTTP with encryption |
| SMB (Server Message Block)        | 445  | File and device sharing, including printers |
| RDP (Remote Desktop Protocol)     | 3389 | Secure remote login via graphical desktop interface |

### Port Ranges

| Range         | Category          | Description |
|---------------|-------------------|-------------|
| 0 to 1023     | Well-known ports  | Reserved for standard protocols |
| 1024 to 49151 | Registered ports  | Used by specific applications |
| 49152 to 65535| Dynamic ports     | Assigned temporarily by the OS |

Security note: During a port scan with Nmap, identifying open ports and the
services running on them is the first step in mapping the attack surface of
a target.
