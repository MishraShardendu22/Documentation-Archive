# Network Layers Explained (Based on Kurose & Ross)

Comprehensive documentation explaining the OSI and Internet protocol stacks, including real-world examples, packet structures, and header visualizations.

---

## 1. Overview

The Internet protocol stack (also mapped to OSI) defines **how data travels** from an application on one device to an application on another. Each layer performs a specific role and encapsulates data from the layer above.

| OSI Layer       | Internet Layer            | Data Unit | Key Function                                           |
| --------------- | ------------------------- | --------- | ------------------------------------------------------ |
| 7. Application  | Application               | Message   | Defines how applications communicate (HTTP, SMTP, DNS) |
| 6. Presentation | (Merged into Application) | —         | Data formatting and encryption                         |
| 5. Session      | (Merged into Application) | —         | Connection management                                  |
| 4. Transport    | Transport                 | Segment   | End-to-end process communication (TCP, UDP)            |
| 3. Network      | Internet                  | Packet    | Host-to-host routing (IP)                              |
| 2. Data Link    | Link                      | Frame     | Node-to-node data transfer (Ethernet, Wi-Fi)           |
| 1. Physical     | Physical                  | Bits      | Transmission through medium                            |

---

## 2. Real-World Example: HTTP Request Flow

**Scenario:** You open `www.example.com` in your browser.

### Step 1 – Application Layer (HTTP)

**Request Example:**

```http
GET /index.html HTTP/1.1
Host: www.example.com
User-Agent: Mozilla/5.0
Accept: text/html
```

**Role:** Defines how web clients (browsers) and servers exchange information.

**Protocols:** HTTP, HTTPS, DNS, SMTP, FTP, WebSocket

---

### Step 2 – Transport Layer (TCP)

**Purpose:** End-to-end, reliable communication between browser and server.

**TCP Header Example:**

```
Source Port: 58321
Destination Port: 80
Sequence Number: 1053
Acknowledgment Number: 2001
Flags: SYN, ACK
Window Size: 65535
Checksum: 0x7B3A
```

**Key Points:**

* Manages connections and retransmissions.
* Uses port numbers to identify processes.
* Example: Source port (random) 58321 → Destination port (HTTP) 80.

**Encapsulation:**

```
TCP Header + HTTP Request Data
```

---

### Step 3 – Network Layer (IP)

**Purpose:** Deliver packets across networks from source to destination host.

**IP Header Example:**

```
Version: IPv4
Source IP: 192.168.1.2
Destination IP: 93.184.216.34
TTL: 64
Protocol: TCP (6)
Header Checksum: 0xD3F2
```

**Encapsulation:**

```
IP Header + TCP Header + HTTP Request Data
```

**Function:** Routers only look at IP headers to forward packets to the next hop.

---

### Step 4 – Data Link Layer (Ethernet/Wi-Fi)

**Purpose:** Node-to-node delivery on a single local network.

**Ethernet Frame Example:**

```
[ Ethernet Header ]
Destination MAC: 00:1C:42:8E:12:AA
Source MAC: 78:4F:43:21:7B:01
Type: IPv4 (0x0800)
----------------------------------
Payload: IP Header + TCP Header + HTTP Data
----------------------------------
Frame Check Sequence (FCS): CRC value
```

**Functions:**

* Framing and local addressing (MAC).
* Error detection via CRC.
* Medium access control (Ethernet CSMA/CD or Wi-Fi CSMA/CA).

**Encapsulation:**

```
Ethernet Header + IP Header + TCP Header + HTTP Request
```

---

### Step 5 – Physical Layer

**Purpose:** Transmission of raw bits as electrical signals, radio waves, or light pulses.

**Example:**

```
01001000 01010100 01010100 01010000 00101101 ...
```

**Medium Examples:**

* Ethernet cable (electrical)
* Fiber optic (light)
* Wi-Fi (radio)

---

## 3. Return Path (Decapsulation)

When the server receives the packet:

1. **Physical Layer:** Converts signals into bits.
2. **Data Link Layer:** Verifies MAC and removes frame header.
3. **Network Layer:** Checks IP and passes to transport layer.
4. **Transport Layer:** Checks port, sequence, and reliability.
5. **Application Layer:** Web server reads HTTP request.

Response then travels back through the same stack in reverse.

---

## 4. OSI vs Internet Stack Comparison

| OSI Model    | Internet Stack | Example Protocol     |
| ------------ | -------------- | -------------------- |
| Application  | Application    | HTTP, DNS, SMTP      |
| Presentation | Application    | TLS/SSL              |
| Session      | Application    | RPC, SOCKS           |
| Transport    | Transport      | TCP, UDP, QUIC       |
| Network      | Internet       | IP, ICMP             |
| Data Link    | Link           | Ethernet, Wi-Fi, ARP |
| Physical     | Physical       | Fiber, Copper, Radio |

---

## 5. Layer Responsibilities Summary

| Layer       | Address Type | Unit    | Example         | Scope                         |
| ----------- | ------------ | ------- | --------------- | ----------------------------- |
| Application | URL, Domain  | Message | HTTP            | End-to-end user communication |
| Transport   | Port         | Segment | TCP             | Process-to-process            |
| Network     | IP           | Packet  | IPv4, IPv6      | Host-to-host                  |
| Data Link   | MAC          | Frame   | Ethernet, Wi-Fi | Node-to-node                  |
| Physical    | —            | Bit     | Fiber, Radio    | Physical medium               |

---

## 6. Full Encapsulation Example

**At Sender:**

```
Application:  GET /index.html HTTP/1.1
Transport:    TCP [src port 58321 → dst port 80]
Network:      IP [192.168.1.2 → 93.184.216.34]
Link:         Ethernet [MAC1 → MAC2]
Physical:     bits over wire/wifi
```

**At Receiver:** Reverse process removes each layer header until only HTTP data remains.

---

## 7. Data Link Layer Deep Dive

### Responsibilities

1. **Framing:** Encapsulate packets into frames with headers and trailers.
2. **Error Detection:** Detect corrupted frames using CRC.
3. **Flow Control:** Prevent sender from overwhelming receiver.
4. **Medium Access:** Manage transmission over shared media.

### Frame Example (Ethernet)

```
+---------------------------------------------------------------+
| Preamble | Dest MAC | Src MAC | Type | Payload | CRC |
+---------------------------------------------------------------+
```

### Example Frame Data

```
Preamble: 101010... (for synchronization)
Dest MAC: 00:1C:42:8E:12:AA
Src MAC: 78:4F:43:21:7B:01
Type: 0x0800 (IPv4)
Payload: IP Packet
CRC: 0xAF12E3D9
```

**Key idea:** Every time the frame crosses a hop (like router to router), the MAC addresses change, but the IP address stays constant.

---

## 8. Visualization Summary

```
HTTP Request (Application)
   ↓
TCP Segment (Transport)
   ↓
IP Packet (Network)
   ↓
Ethernet Frame (Data Link)
   ↓
Bits (Physical)
```

Each layer adds its own header to the data from the layer above. The final product is transmitted as bits, then reconstructed in reverse order by the receiver.
