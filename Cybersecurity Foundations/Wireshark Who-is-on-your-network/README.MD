This project documents the Wireshark lab completed as part of Cybersecurity Foundations. The exercise uses two supplied packet captures to identify communicating devices, examine network conversations, identify applications and investigate wireless traffic protected with WEP.

The lab has two main analysis areas:

1. `kansascityWEP.pcap` — examine wireless traffic, recover the WEP key provided by the lab capture, and configure Wireshark to decode the protected traffic.
2. `http-espn101.pcapng` — inspect conversations and application traffic, including HTTP requests, the requested ESPN resource, colorized conversations and TCP segments associated with a JPEG transfer.

This repository is a technical record of the lab process. It should be completed with the student's own worksheet answers, observations and screenshots. Do not treat example frame numbers or observations as universal; packet numbering depends on the exact capture file and version.

## Learning objectives

- Identify communicating hosts and conversations in a packet capture.
- Use Wireshark statistics and display filters to investigate traffic.
- Recognise common application protocols in captured traffic.
- Inspect HTTP requests and requested resources.
- Use conversation colouring to make packet relationships easier to follow.
- Examine TCP segments associated with a transferred file.
- Understand the security weaknesses of WEP-protected wireless traffic.
- Configure Wireshark's IEEE 802.11 decryption preferences for an authorised lab capture.
- Document observations with reproducible filters and screenshots.

## Tools and environment

| Item | Description |
|---|---|
| Packet analysis tool | Wireshark |
| WEP analysis utility | Aircrack-ng |
| Linux environment used | Kali Linux |
| Additional environment | Windows, where applicable |
| Capture formats | `.pcap` and `.pcapng` |
| Evidence | Wireshark and terminal screenshots |

Record the exact Wireshark and Aircrack-ng versions used if required by the course.

## Repository structure

```text
Project-4-Wireshark-Who-Is-Talking-on-Your-Network/
├── README.md
├── captures/
│   └── .gitkeep
└── evidence/
    └── README.md
```

The coursework PCAP files are not included in this repository. Add them only if redistribution is permitted by the course owner. Store your genuine screenshots in `evidence/`.

---

## Part 1 – Kansas City WEP capture

### Capture file

`kansascityWEP.pcap`

### Initial inspection

Open the capture in Wireshark and inspect the packet list. The supplied capture contains wireless 802.11 traffic, including ARP and acknowledgement frames. Some wireless data is protected by WEP and is not fully interpretable until Wireshark is configured with the lab's recovered key.

Useful display filters:

```text
wlan
```

```text
wlan.fc.type == 2
```

```text
wlan.fc.type == 2 && wlan.fc.protected == 1
```

The final filter isolates protected IEEE 802.11 data frames. Acknowledgement/control frames are not the same as protected data frames and do not contain application payloads to decrypt.

### WEP key recovery – lab procedure

The assignment directs students to analyse the supplied capture with Aircrack-ng. In the authorised coursework environment, the command used was:

```bash
aircrack-ng kansascityWEP.pcap
```

The lab run reported that a key was found and that the capture's encrypted traffic was decrypted successfully by the utility. Record the exact output from your own terminal screenshot in the worksheet/evidence rather than relying on this README as the only proof.

**Recovered lab key shown in the coursework run:**

```text
1F:1F:1F:1F:1F
```

This value is documented solely as the result observed in this supplied educational capture. Do not use it to access any other wireless network.

### Configure Wireshark decryption

To configure Wireshark for the supplied lab capture:

1. Open `kansascityWEP.pcap`.
2. Select **Edit → Preferences**.
3. Expand **Protocols** and select **IEEE 802.11**.
4. Open **Decryption Keys** by selecting **Edit**.
5. Add a new key using the **+** control.
6. Set the key type to **WEP**.
7. Enter the recovered lab key.
8. Confirm the dialogs and return to the packet list.
9. Apply the protected-data display filter:

   ```text
   wlan.fc.type == 2 && wlan.fc.protected == 1
   ```

10. Select a protected data frame and inspect its decoded packet details.

### Decryption observation

In the lab session, Wireshark displayed protected wireless data frames with decoded protocol information. The selected frame showed a correct WEP integrity check (`WEP ICV: correct`). The packet list also showed ARP traffic after decryption.

The observed capture view showed 30,630 displayed packets (46.9% of the 65,282-packet capture) with the protected-data filter applied. These are observations from that lab session; counts can differ if the capture or filter differs.

### Security observations

WEP is an obsolete wireless security protocol. Its design has weaknesses that make captured traffic vulnerable to key recovery under suitable conditions. Recovering the key from a supplied capture demonstrates why WEP should not be used to protect modern wireless networks.

Security implications include:

- Captured wireless traffic may expose information after decryption.
- Weak wireless encryption can undermine confidentiality.
- Attackers who obtain the key may be able to inspect traffic protected by that key.
- Organisations should use modern wireless security configurations and retire WEP.

---

## Part 2 – HTTP and network conversation analysis

### Capture file

`http-espn101.pcapng`

Use the supplied capture to answer the worksheet questions. The following filters and procedures support the activities discussed in the lab.

### Identify endpoints and conversations

Useful Wireshark menus:

- **Statistics → Endpoints**
- **Statistics → Conversations**
- **Statistics → Protocol Hierarchy**

These views help identify hosts, communication pairs, packet counts, byte counts and protocols present in the capture.

Useful display filters:

```text
http
```

```text
http.request
```

```text
tcp
```


## Findings and observations

Complete this section from the supplied captures and your own Wireshark inspection.

| Analysis area | Observation to record |
|---|---|
| WEP capture packet count | 65,282 packets observed in the supplied lab session |
| Aircrack-ng result | Record the key-recovery result shown in your terminal |
| Wireshark WEP decryption | Record whether decoded data and a correct WEP ICV were observed |
| Decrypted protocols | ARP was visible in the observed decrypted packet list |
| HTTP resource | Record the Full request URI and what happened when opened |
| HTTP conversation | Record the endpoints and packets shown in your colorized conversation |
| JPEG transfer | Record the 18 TCP segment frame numbers identified from your capture |
| Other endpoints/protocols | Add observations from Endpoints, Conversations and Protocol Hierarchy |

## Evidence checklist

Save your genuine screenshots in the `evidence/` directory.

| Suggested filename | Evidence |
|---|---|
| `01-wep-capture-open.png` | `kansascityWEP.pcap` open in Wireshark |
| `02-aircrack-wep-key.png` | Aircrack-ng terminal result from the lab capture |
| `03-wireshark-wep-decryption.png` | Wireshark showing protected data and successful WEP integrity check / decoded traffic |
| `04-http-request-uri.png` | Frame 17 HTTP GET and Full request URI / requested resource as required |
| `05-colorized-http-conversation.png` | Colorized HTTP conversation in the packet list |
| `06-jpeg-tcp-segments.png` | TCP frames associated with the JPEG transfer, including the identified segments |

Use the worksheet to determine whether additional screenshots are required for other questions. See [`evidence/README.md`](evidence/README.md).

## Reproducing the analysis

1. Obtain the two PCAP files from the official course assignment.
2. Open each file in Wireshark.
3. Use the display filters and menu paths in this README.
4. For the WEP capture, perform the assigned analysis in the authorised lab environment and configure Wireshark's IEEE 802.11 decryption preferences.
5. For the HTTP capture, inspect the specified frames and relevant TCP conversation.
6. Save screenshots in `evidence/`.
7. Complete every worksheet question using packet evidence.
8. Check that frame numbers and findings match the exact supplied capture before committing.