# Wireshark-notes
This is my Wireshark notes

# 🦈 Wireshark Notes

Interview-focused notes on **Wireshark**: what it is, who uses it, how to install it, the 3 panes, filters, common protocols, IDS vs IPS, and how to detect a **port scan**.

---

## 📑 Table of Contents
1. [What is Wireshark?](#1-what-is-wireshark)
2. [Uses of Wireshark](#2-uses-of-wireshark)
3. [Who uses Wireshark?](#3-who-uses-wireshark)
4. [Download and setup](#4-download-and-setup)
5. [How Wireshark works](#5-how-wireshark-works)
6. [Interface tour (3 panes)](#6-interface-tour-3-panes)
7. [Common protocols to know](#7-common-protocols-to-know)
8. [Interview notes](#8-interview-notes)
9. [Wireshark vs IDS vs IPS](#9-wireshark-vs-ids-vs-ips)
10. [Capture filters vs Display filters](#10-capture-filters-vs-display-filters)
11. [Interview question: how many ports did the attacker try?](#11-interview-question-how-many-different-ports-did-the-attacker-try)
12. [Quick revision](#12-quick-revision)

---

## 1. What is Wireshark?
Wireshark is a free **protocol analyser** and **network analyser**. It captures the packets moving through your network card and shows them in a form you can read. It is used by **network engineers** and **cybersecurity engineers**.

Every website visit, login and file transfer is made of packets. Think of Wireshark as a **microscope** that lets you open each one. You can watch **live traffic** or open a saved **capture file (pcap)** and study it later.

![What is Wireshark]wireshark-notes/images/01-what-is-wireshark.png

It helps answer questions like:
- Which devices are communicating?
- Which IP addresses are communicating?
- Which protocols are being used?
- Is there suspicious network traffic?
- What data is being transmitted?

---
<img width="562" height="309" alt="Screenshot 2026-10-30 155515" src="https://github.com/user-attachments/assets/959070d7-b03a-4615-941b-e3d4b97547f8" />


## 2. Uses of Wireshark
- **Network troubleshooting:** find slow responses, dropped packets and retransmissions.
- **Security analysis:** spot scans, suspicious connections and clear-text passwords.
- **Learning protocols:** see how TCP, DNS and HTTP work, one packet at a time.
- **Incident investigation:** rebuild what happened from a saved capture file.
- **Performance checks:** find which devices and sites use the most bandwidth.
- **Free and open source.**

![Uses of Wireshark](images/02-uses-of-wireshark.png)

---

## 3. Who uses Wireshark?
- **Network engineers:** troubleshoot slow links, drops and misconfigured devices.
- **SOC analysts:** investigate alerts and check what a suspicious device was doing.
- **Ethical hackers:** study traffic during authorised tests and lab exercises.

![Who uses Wireshark](images/03-who-uses-wireshark.png)

---

## 4. Download and setup
1. Go to **wireshark.org** and open the Download page.
2. Pick your system: Windows, macOS or Linux.
3. Run the installer (default options are fine).
4. Allow **Npcap**, the driver that captures packets on Windows.

![Download and setup](images/04-download-and-setup.png)

---

## 5. How Wireshark works
**Capture → Decode → Display**

1. **Capture:** records packets from your network card.
2. **Decode:** splits each packet into its protocol layers.
3. **Display:** shows it in a form you can read and filter.

To view and analyse packet contents:
- Click a packet in the packet list.
- Expand each layer in the packet details.
- Check the raw data in the bytes pane.

![How Wireshark works](images/05-how-wireshark-works.png)

---

## 6. Interface tour (3 panes)
Wireshark has **three panes**. Know them before you start:

| # | Pane | What it shows |
|---|------|---------------|
| 1 | **Packet List** | One line per packet: time, source, destination, protocol |
| 2 | **Packet Details** | The layers of the packet you selected |
| 3 | **Packet Bytes** | The raw data in hex and text |

![Interface tour](images/06-interface-tour-three-panes.png)

---

## 7. Common protocols to know

| # | Protocol | Purpose |
|---|----------|---------|
| 1 | **TCP** | Reliable delivery using the **3-way handshake** (SYN → SYN-ACK → ACK) |
| 2 | **DNS** | Turns names into IP addresses |
| 3 | **HTTP** | Carries web traffic in **clear text** |
| 4 | **ARP** | Finds the **MAC address** for an IP |
| 5 | **ICMP** | Carries **ping** and error messages |

> 💡 Type the protocol name in the **display filter** (`tcp`, `dns`, `http`, `arp`, `icmp`) to see only those packets.

---

## 8. Interview notes

> ❓ **At which layer does Wireshark capture packets?**
> Wireshark captures packets at **Layer 2 (Data Link)**, and supports analysis from **Layer 2 up to Layer 7** of the OSI model.

Even when capturing for a router, the router has a switched (Layer 2) interface, and the capture happens through that Layer 2 interface.

**Important limitations**
- ❌ Wireshark has **no IPS** (Intrusion Prevention System).
- ❌ It **cannot stop or block** traffic.
- ✅ It works **passively**, in **live mode** (capture and watch in real time) or **offline mode** (open a saved capture later).
- ✅ Captured packets can be **saved**.

---

## 9. Wireshark vs IDS vs IPS

**IDS (Intrusion Detection System):** monitors traffic and detects suspicious or malicious activity, then generates an **alert**. It generally does **not** block automatically.

**IPS (Intrusion Prevention System):** detects **and prevents** malicious traffic. It can automatically **block** an attack.

| Feature | Wireshark | IDS | IPS |
|---------|-----------|-----|-----|
| Monitor traffic | Yes | Yes | Yes |
| Analyze packets | Yes | Depends on system | Depends on system |
| Detect attacks | Manual analysis | Yes | Yes |
| Generate alerts | Not its primary role | Yes | Yes |
| Block attacks | **No** | Usually no | **Yes** |

---

## 10. Capture filters vs Display filters

| | Capture filters | Display filters |
|---|-----------------|-----------------|
| **Applied** | **Before** capture starts | **After** capture |
| **Effect** | Only matching packets are saved | Hides packets, **nothing is deleted** |
| **Change during capture?** | No | Yes, any time |
| **Example** | `host 192.168.1.5` | `ip.addr == 192.168.1.5` |

![Capture vs display filters](images/07-capture-vs-display-filters.png)

---

## 11. Interview question: How many different ports did the attacker try?

This asks how many **different destination ports** the attacker scanned on the target IP. This activity is called a **Port Scan**.

**Step 1: Apply a filter** (for TCP SYN scans)
```
tcp.flags.syn == 1 && tcp.flags.ack == 0
```

**Step 2: Identify the attacker.** Check the **Source** column for the attacker's IP.

**Step 3: Filter attacker-to-target traffic** (replace with your real IPs)
```
ip.src == 192.168.1.50 && ip.dst == 192.168.1.100 && tcp.flags.syn == 1 && tcp.flags.ack == 0
```

**Step 4: Find the different ports**
1. Click a packet.
2. Expand **Transmission Control Protocol** in the middle pane.
3. Find **Destination Port**.
4. Note the destination ports across the packets.
5. Count **only unique** port numbers (no duplicates).

**Example:** destination ports `21, 22, 23, 25, 80, 443, 8080` → **7 different ports**.

---

## 12. Quick revision
- Wireshark = **passive** packet analyser, cannot block traffic.
- Captures at **Layer 2**, analyses **Layer 2 to 7**.
- Works in **live** and **offline (pcap)** mode.
- **3 panes:** Packet List, Packet Details, Packet Bytes.
- **IDS** detects and alerts; **IPS** detects and blocks.
- **Capture filter** = before capture; **Display filter** = after capture.
- Port scan: filter SYN-only packets, then count **unique destination ports**.
- Key protocols: **TCP, DNS, HTTP, ARP, ICMP**.
