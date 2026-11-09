This project documents a Wireshark incident-analysis lab completed as part of Cybersecurity Foundations.

The scenario places the analyst in the role of investigating a possible malware infection on an internal company network. The supplied packet capture represents traffic from the company's internal subnet. The objective is to identify active hosts, use packet evidence to identify device types, examine suspicious communications, and determine which Windows host is the strongest infection candidate.

The lab uses the capture:

- `2018-12-18-traffic-analysis-exercise.pcap`

The analysis should be based on the supplied PCAP and the accompanying 17-question worksheet. This README is a project record and guide; the completed worksheet remains the authoritative submission for the course.

## Scenario and network context

The assignment provides the following network information:

| Item | Value |
|---|---|
| LAN segment | `172.16.3.0/24` |
| Domain | `eggnogsoup.com` |
| Domain Controller | `172.16.3.2` |
| Domain Controller hostname | `EggNogSoup-DC` |
| Default gateway | `172.16.3.1` |
| Broadcast address | `172.16.3.255` |

The investigation focuses on traffic within this subnet and communications involving the Domain Controller.

## Learning objectives

- Identify active hosts communicating on an internal network.
- Use Wireshark endpoint and conversation statistics.
- Examine DHCP, DNS, HTTP and other packet details to identify devices.
- Use HTTP User-Agent and DHCP hostname information as device-identification clues.
- Identify suspicious downloads and unusual outbound communications.
- Correlate multiple packet observations when assessing a possible malware infection.
- Document findings with packet numbers, display filters and screenshots.
- Distinguish observed evidence from assumptions or conclusions.

## Tools and environment

| Tool / resource | Purpose |
|---|---|
| Wireshark | Packet inspection and traffic analysis |
| Supplied PCAP | Network evidence for the investigation |
| Red Hand Online PCAP Analyzer | Optional analysis resource suggested by the assignment |
| VirusTotal | Optional file/indicator reputation resource suggested by the assignment |
| Worksheet | Records answers to all 17 questions |

Record the exact Wireshark version and operating system used if required by the course.

## Repository structure

```text
Project-5-Wireshark-Who-Got-Infected/
├── README.md
├── captures/
│   └── .gitkeep
└── evidence/
    └── README.md
```

The supplied PCAP and course worksheet are not included in this repository. Add packet captures only if you have permission to redistribute the course materials. Place your genuine screenshots in `evidence/`.

---

## Investigation workflow

### 1. Open and profile the capture

1. Obtain `2018-12-18-traffic-analysis-exercise.pcap` from the official course assignment.
2. Open it in Wireshark.
3. Review the packet list and note the protocols present.
4. Use **Statistics → Endpoints** to review active addresses.
5. Use **Statistics → Conversations** to identify communicating pairs.
6. Use **Statistics → Protocol Hierarchy** to understand which protocols appear in the capture.
7. Compare observed addresses with the assigned `172.16.3.0/24` network.

Useful display filters:

```text
ip
```

```text
ip.addr == 172.16.3.2
```

```text
ip.src == 172.16.3.0/24
```

```text
ip.dst == 172.16.3.0/24
```

Wireshark's Endpoints and Conversations windows are useful for counting hosts and examining communication volume. Ensure that the correct address family and conversation type are selected.

### 2. Identify active hosts

Use **Statistics → Endpoints → IPv4** to list IPv4 addresses present in the capture.

When answering the worksheet:

- Include only addresses belonging to the internal subnet unless the question asks otherwise.
- Exclude the Domain Controller (`172.16.3.2`) when the question asks for hosts besides the Domain Controller.
- Use the packet details and other worksheet evidence to support device identification.
- Do not assume that every address in the subnet was active simply because it is a valid address.

### 3. Identify devices

The worksheet uses network evidence to identify operating systems and device types. Useful evidence sources include:

- DHCP hostname information.
- DHCP vendor-class and client information, when present.
- HTTP User-Agent strings.
- DNS queries and destination hostnames.
- Traffic patterns and communications with internal services.

Useful filters:

```text
dhcp
```

```text
http.request
```

```text
dns
```

```text
ip.addr == 172.16.3.188
```

Replace the example address with the address being investigated.

To inspect a DHCP hostname, select a DHCP packet and expand **Dynamic Host Configuration Protocol**. Look for **Option (12): Host Name**.

To inspect a browser or operating-system clue, select an HTTP request and expand **Hypertext Transfer Protocol → User-Agent**.

A hostname or User-Agent is a clue supplied by a client application or device. It is not, by itself, conclusive proof of the physical device or its owner.

### 4. Investigate suspicious activity

The assignment asks the analyst to identify the Windows host showing indications associated with Emotet and IcedID (Bokbot).

A sound analysis should correlate several observations rather than rely on a single unusual packet. Examine:

- Requests for documents or executable files.
- HTTP response details and downloaded filenames.
- Destination domains and IP addresses.
- The source host responsible for the requests.
- Communications that occur after a suspicious download.
- Whether the observed activity is consistent with the worksheet's stated scenario.

Useful display filters:

```text
http.request
```

```text
http.response
```

```text
http.request.method == "GET"
```

```text
ip.addr == 172.16.3.133
```

The last filter is an example for inspecting the suspected host identified during the lab walkthrough. Confirm all relevant frames and conclusions against your own copy of the supplied capture before recording them as findings.

For a specific packet, use:

```text
frame.number == 47170
```

You can combine frame-number conditions with `||`, for example:

```text
frame.number == 47170 || frame.number == 48352 || frame.number == 75690
```

Frame numbers are specific to a particular capture file. Confirm that the selected frames contain the expected traffic before relying on them.

### 5. Record the infection assessment

The lab walkthrough identified `172.16.3.133` (hostname `Varner-Win-PC`) as the strongest suspected infection candidate. The observations discussed included a document download, an executable download and subsequent unusual outbound communication.

Treat this as a packet-based assessment, not a forensic confirmation that malware executed successfully or that the endpoint was definitively compromised. A PCAP can show network activity, but it generally cannot prove what happened on the endpoint itself.

When completing the worksheet, preserve the distinction between:

- **Observed evidence:** what the packet capture directly shows.
- **Interpretation:** why the activity appears suspicious.
- **Assessment:** which host is the strongest infection candidate based on the combined evidence.

---

## Findings summary

Use this table as a concise record of the investigation. Confirm details against the actual capture and worksheet before committing final findings.

| Investigation item | Finding / note |
|---|---|
| Internal subnet | `172.16.3.0/24` |
| Domain | `eggnogsoup.com` |
| Domain Controller | `172.16.3.2` (`EggNogSoup-DC`) |
| Gateway | `172.16.3.1` |
| Broadcast address | `172.16.3.255` |
| Suspected Windows host | `172.16.3.133` |
| Suspected host name | `Varner-Win-PC` |
| Assessment | Strongest infection candidate based on the suspicious network activity discussed in the lab |
| Malware families named in scenario | Emotet and IcedID (Bokbot) |

## Worksheet coverage

The course worksheet contains 17 questions. Use it to ensure that each response is complete and supported by packet evidence.

| Topic | Questions / evidence focus |
|---|---|
| Active hosts and IP addresses | Questions 1–3 |
| Ubuntu, iPhone and Amazon Fire identification | Questions 4–8 |
| Windows hosts and suspected infection | Questions 9–11 |
| Android version and device identification | Questions 12–17 |

The worksheet may request screenshots for specific answers. Capture the relevant Wireshark packet list and expanded packet details, ensuring that the frame number and identifying field are readable.

## Evidence checklist

Save your genuine screenshots in `evidence/`. Suggested filenames:

| Suggested filename | Evidence |
|---|---|
| `01-ipv4-endpoints.png` | Statistics → Endpoints → IPv4, showing the internal hosts |
| `02-iphone-dhcp-hostname.png` | DHCP packet details showing the iPhone hostname |
| `03-amazon-fire-dhcp-hostname.png` | DHCP packet details showing the Amazon device hostname |
| `04-suspicious-traffic.png` | Packet details supporting the suspected infection assessment |
| `05-motorola-user-agent.png` | HTTP User-Agent showing Android version and Motorola model |
| `06-samsung-user-agent.png` | HTTP User-Agent showing the Samsung model identifier |

Use the worksheet to confirm the exact screenshot placement and any additional evidence requested. See [`evidence/README.md`](evidence/README.md).

## Responsible handling of PCAPs and indicators

Packet captures can contain internal IP addresses, hostnames, browsing activity and other potentially sensitive information. Keep course-provided captures and screenshots within the intended educational context. Do not publish sensitive traffic or redistribute instructor-provided files without permission.

External analysis services such as Red Hand Online PCAP Analyzer and VirusTotal were suggested as optional resources in the assignment. Before uploading a capture or extracted file to a third-party service, consider whether it contains private or sensitive information and follow your institution's rules.

## Reproducing the analysis

1. Download the official course PCAP and worksheet.
2. Open the PCAP in Wireshark.
3. Review Endpoints, Conversations and Protocol Hierarchy.
4. Use DHCP, DNS and HTTP packet details to identify devices.
5. Investigate the suspicious host's requests, responses and subsequent communications.
6. Record packet numbers and supporting observations in the worksheet.
7. Save the required screenshots in `evidence/`.
8. Verify that every answer is supported by the capture.
9. Complete the worksheet and submit it through the course platform.