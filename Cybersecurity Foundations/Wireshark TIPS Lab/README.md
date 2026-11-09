## Overview

This project documents the Wireshark TIPS Lab completed as part of Cybersecurity Foundations. The lab practises inspecting DHCP traffic, identifying client hostnames, and configuring Wireshark columns to display HTTP hostnames and HTTPS server names.

## Learning objectives

- Apply Wireshark display filters to isolate protocol traffic.
- Recognise DHCP Discover, Offer, Request, ACK and NAK messages.
- Inspect DHCP Option 12 (Host Name).
- Add protocol fields to the packet list as custom columns.
- Identify HTTP `Host` header values.
- Identify TLS Server Name Indication (SNI) values in Client Hello packets.
- Record only what is supported by the packet capture.

## Tools and environment

| Item | Description |
|---|---|
| Packet analysis | Wireshark |
| Operating systems used | Kali Linux and Windows |
| Protocols | DHCP, HTTP and HTTPS/TLS |
| Evidence | Wireshark screenshots |

Record the exact Wireshark version and operating system used if required by the course.

## Repository structure

```text
Project-3-Wireshark-TIPS-Lab/
├── README.md
├── captures/
│   └── .gitkeep
└── evidence/
    └── README.md
```

Keep supplied PCAP files out of GitHub unless you have permission to redistribute them. Add your genuine screenshots to `evidence/`.

## 1. DHCP message analysis

DHCP commonly uses the following message types:

| Message | Option 53 value | Purpose |
|---|---:|---|
| Discover | 1 | Client searches for DHCP servers. |
| Offer | 2 | Server offers an address and configuration. |
| Request | 3 | Client requests the offered configuration. |
| ACK | 5 | Server acknowledges the request. |
| NAK | 6 | Server rejects the request. |

Useful Wireshark display filters:

```text
dhcp
dhcp.option.dhcp == 1
dhcp.option.dhcp == 2
dhcp.option.dhcp == 3
dhcp.option.dhcp == 5
dhcp.option.dhcp == 6
```

To inspect a message, select its packet and expand **Dynamic Host Configuration Protocol** in the Packet Details pane. Expand the DHCP options so the message type, transaction ID and other relevant values are visible.

Compare transaction IDs (`xid`) to associate messages from the same DHCP transaction. A matching transaction ID does not prove that every message in the exchange was captured.

### DHCP interpretation

A typical successful DHCP allocation follows this sequence:

```text
Client                         DHCP Server
  |                                  |
  |---------- Discover ------------>|
  |<----------- Offer ---------------|
  |----------- Request ------------>|
  |<------------ ACK ----------------|
```

A NAK is a negative acknowledgement. It is not an ACK and does not complete the normal successful four-message sequence.

Captures can be incomplete. The capture point may not observe every broadcast or relayed packet, or the capture may have started after address allocation. Record the traffic actually observed. Do not invent a missing Offer or ACK, or substitute a NAK for either one.

## 2. Display DHCP hostnames as a column

DHCP Option 12 carries a client-supplied hostname when the client includes one.

Display filter:

```text
dhcp.option.hostname
```

Steps:

1. Select a DHCP packet containing **Option (12): Host Name**.
2. Expand **Dynamic Host Configuration Protocol**.
3. Locate **Option (12): Host Name**.
4. Right-click the hostname field.
5. Select **Apply as Column**.
6. Clear the display filter.
7. Click the **Host Name** column heading to sort the packet list.
8. Widen the column and capture a screenshot.

A DHCP hostname is supplied by the client. It is useful evidence, but it is not independently verified proof of device identity.

## 3. Display HTTP hostnames as a column

For unencrypted HTTP, the requested hostname is normally carried in the HTTP `Host` header.

Display filter:

```text
http.request
```

Steps:

1. Select a packet showing an HTTP `GET` or `POST` request.
2. Expand **Hypertext Transfer Protocol**.
3. Locate the **Host** field.
4. Right-click it and select **Apply as Column**.
5. Clear the display filter.
6. Widen the new column so the hostnames are readable.
7. Capture a screenshot of the packet list.

If `http.request` returns no packets, the capture may not contain unencrypted HTTP requests. HTTPS does not normally expose the HTTP Host header in an ordinary packet capture.

## 4. Display HTTPS server names as a column

During a typical TLS handshake, a client may include the destination hostname in the Server Name Indication (SNI) extension of its Client Hello.

Display filter:

```text
tls.handshake.extensions_server_name
```

Alternative filter for Client Hello packets:

```text
tls.handshake.type == 1
```

Steps:

1. Apply the SNI display filter.
2. Select a matching TLS Client Hello packet.
3. Expand **Transport Layer Security**, then **Extensions**.
4. Locate **server_name** / **Server Name Indication**.
5. Right-click the server-name value and select **Apply as Column**.
6. Clear the display filter.
7. Widen the new column and capture a screenshot.

An empty result does not necessarily mean HTTPS was absent. The capture may not include the handshake, or the connection may use Encrypted Client Hello (ECH), which can conceal the server name.

## Findings and observations

Complete this table from your own capture. Do not fill it with assumed values.

| Activity | Observation |
|---|---|
| DHCP Discover | Frame number and transaction ID: *complete from capture* |
| DHCP Offer | Frame number, or state that no Offer was observed |
| DHCP Request | Frame number and transaction ID: *complete from capture* |
| DHCP ACK | Frame number, or state that no ACK was observed |
| DHCP NAK | Record only if observed |
| DHCP hostnames | List hostnames visible in the Host Name column |
| HTTP hostnames | List hostnames visible in the HTTP Host column |
| HTTPS server names | List names visible in the TLS SNI column |

## Evidence checklist

Save genuine screenshots in the `evidence/` directory.

| Suggested filename | Required evidence |
|---|---|
| `01-dhcp-discover.png` | Discover packet with DHCP options expanded |
| `02-dhcp-offer.png` | Offer packet with DHCP options expanded, if captured |
| `03-dhcp-request.png` | Request packet with DHCP options expanded |
| `04-dhcp-ack.png` | ACK packet with DHCP options expanded, if captured |
| `05-dhcp-hostnames.png` | Packet list sorted by Host Name |
| `06-http-hostnames.png` | Packet list showing the HTTP Host column |
| `07-https-server-names.png` | Packet list showing the TLS SNI column |

See [`evidence/README.md`](evidence/README.md) for screenshot guidance.

## Security relevance

This lab demonstrates how packet analysis supports network monitoring and troubleshooting:

- DHCP hostnames help associate traffic with client devices.
- DHCP message sequences can reveal address-allocation problems.
- HTTP Host headers can identify requested virtual hosts when traffic is unencrypted.
- TLS SNI can reveal the hostname a client is attempting to reach when the handshake exposes it.
- Custom columns make repeated values easier to compare across a capture.

These fields are investigative clues, not definitive proof of identity or intent. Hostnames can be changed or spoofed, and encryption limits what can be inferred from packet contents.

## Reproducing the analysis

1. Open the relevant `.pcap` or `.pcapng` file in Wireshark.
2. Apply the display filters in this README.
3. Expand the relevant protocol fields in Packet Details.
4. Use **Apply as Column** to add the required fields.
5. Save screenshots in `evidence/`.
6. Record observed frame numbers, transaction IDs and hostnames.
