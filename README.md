# wireshark-network-traffic-analysis
This project consists of a beginner analysis of analysing network packets using Wireshark

# Network Traffic Analysis Using Wireshark


## 1. Project Overview

This project involved capturing and analysing live network traffic using Wireshark.

The objective was to develop practical networking and cybersecurity investigation skills by examining DNS queries and responses, identifying IP addresses, analysing TCP connections and observing TLS traffic.

## 2. Tools and Environment

|---|---|
| Operating system | macOS |
| Hardware | Apple Silicon M1 Mac |
| Network analysis tool | Wireshark 4.6.10 |
| Network interface | Wi-Fi (en0) |
| Capture format | PCAPNG |

## 3. Project Objectives

- Capture live network traffic.
- Understand packet structures and network protocols.
- Filter DNS and TCP traffic.
- Identify DNS queries and responses.
- Understand A, AAAA and CNAME records.
- Investigate TCP connection establishment.
- Identify SYN, SYN-ACK and ACK flags.
- Observe TLS traffic and Client Hello messages.
- Document investigation findings.

## 4. Investigation 1: Capturing Network Traffic

I opened Wireshark and selected the Wi-Fi (en0) interface.

I started a live packet capture while generating normal network activity through browsing and using services on my own MacBook.

**Results:**

- Total packets captured: 11,754
- Packets dropped: 0
- Interface: Wi-Fi (en0)

This provided a sample of network traffic for further investigation.

## 5. Investigation 2: DNS Analysis

### Filtering DNS traffic

I applied the following display filter:

`dns`

The capture displayed 36 DNS packets out of 11,754 total packets.

I identified DNS activity associated with Apple and Spotify services.

Examples included:

- `configuration-lb.ls-apple.com.akadns.net`
- `configuration.ls.v.aaplimg.com`
- `mesu.g.aaplimg.com`
- `gew1-spclient.spotify.com`

### Investigating a Spotify DNS response

I selected packet 6278 and examined its DNS response.

Findings:

| Field | Result |
|---|---|
| Original hostname | `gew1-spclient.spotify.com` |
| Query type | HTTPS |
| Response record | CNAME |
| CNAME destination | `edge-web-gew1.dual-gslb.spotify.com` |
| Response status | No error |

This demonstrated how DNS can use CNAME records to associate one hostname with another.

### Identifying the IPv4 address

I then examined packet 6280, which contained an A-record response.

The response showed:

`gew1-spclient.spotify.com`

CNAME destination:

`edge-web-gew1.dual-gslb.spotify.com`

IPv4 address:

`35.186.224.26`

This demonstrated how DNS responses can provide an IPv4 address associated with a hostname.

### DNS filter used

`dns.qry.name == "gew1-spclient.spotify.com"`

This narrowed the investigation to 12 matching packets.

### Network addressing

I also identified the following addresses in the capture:

| Address | Observation |
|---|---|
| `192.168.1.116` | Local device address |
| `192.168.1.254` | DNS resolver/router |

DNS traffic was observed using UDP port 53.

## 6. Investigation 3: TCP Traffic Analysis

### Filtering TCP traffic

I applied:

`tcp`

The filter displayed 402 TCP packets.

I examined TCP traffic and identified several packet types:

- ACK
- FIN, ACK
- RST
- TCP Retransmission
- TLS Application Data

I also observed communications involving destination port 443, commonly used for HTTPS traffic.

### Investigating the TCP three-way handshake

I applied the following filter:

`tcp.flags.syn == 1`

This displayed 26 packets containing the SYN flag.

I selected packets 4056, 4059 and 4060 to investigate a TCP connection.

**Packet 4056 — SYN**

- Source port: 62428
- Destination port: 443
- Flags: SYN, ECE, CWR

The client initiated a TCP connection to a remote service on port 443.

**Packet 4059 — SYN-ACK**

- Source port: 443
- Destination port: 62428
- Flags: SYN, ACK, ECE

The remote endpoint acknowledged the connection request and responded with SYN-ACK.

**Packet 4060 — ACK**

- Source port: 62428
- Destination port: 443
- Flags: ACK

The client acknowledged the server's response.

This demonstrated the complete TCP three-way handshake.

### TCP filter used

`tcp.port == 62428`

This allowed me to isolate traffic associated with the connection and examine the handshake and subsequent communications.

## 7. Investigation 4: TLS Traffic Observation

During the TCP investigation, I identified packet 4062.

The packet contained a TLS Client Hello message with the following Server Name Indication (SNI):

`mesu.apple.com`

This demonstrated how TLS negotiation begins after a TCP connection is established.

I also observed TLS Application Data packets.

This helped me understand the relationship between TCP connections and encrypted application communications.

## 8. Key Concepts Learned

**DNS:** Translates domain names into IP addresses and supports service discovery.

**A Record:** Associates a hostname with an IPv4 address.

**AAAA Record:** Associates a hostname with an IPv6 address.

**CNAME Record:** Associates one hostname with another canonical hostname.

**TCP:** A transport protocol used to establish reliable connections between devices.

**SYN:** Initiates a TCP connection.

**SYN-ACK:** Acknowledges the connection request and responds.

**ACK:** Acknowledges receipt of data or connection messages.

**TLS Client Hello:** Begins the TLS negotiation process.

**SNI:** Indicates the hostname a client is attempting to reach during TLS negotiation.

**Wireshark Display Filters:** Allow analysts to isolate particular protocols, addresses and packet characteristics.

## 9. Main Findings

Throughout the investigation, I successfully:

1. Captured 11,754 network packets.
2. Identified DNS traffic and filtered 36 DNS packets.
3. Investigated DNS queries and responses associated with Apple and Spotify.
4. Identified a CNAME destination and IPv4 address.
5. Identified local network addressing and DNS resolver traffic.
6. Filtered 402 TCP packets.
7. Investigated the complete TCP three-way handshake using packets 4056, 4059 and 4060.
8. Observed a TLS Client Hello containing the SNI hostname `mesu.apple.com`.

The observed traffic was consistent with ordinary network communications. No malicious activity was established during this investigation.

## 10. Conclusion

This project provided practical experience using Wireshark to capture and analyse network traffic.

I investigated DNS requests and responses, identified hostname resolution records, examined TCP connection establishment and observed TLS negotiation.

The project strengthened my understanding of how devices communicate over networks and provided practical experience with tools and techniques relevant to entry-level cybersecurity and SOC Analyst roles.
