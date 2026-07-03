# Chapter 5: Standards, Organizations & Fundamentals — Part 2 (Applied + Practice)
**CWNA-109 | Book Chapter 1**

---

## 5.12 Regulatory Deep Dive — FCC Power Limits and Channel Rules

The CWNA exam expects you to know not just *that* power limits exist, but *what* they are and *why* they differ between bands and sub-bands.

### 5.12.1 The 2.4 GHz ISM Band — FCC Rules

- Frequency range: 2.400 – 2.4835 GHz
- Total usable spectrum: 83.5 MHz
- Maximum EIRP (point-to-multipoint): **30 dBm (1 Watt)**
- Maximum EIRP (point-to-point outdoor): higher limits allowed under FCC Part 15.247 with reduced transmitter power as antenna gain increases
- 11 channels defined in USA (only 3 non-overlapping: 1, 6, 11 — detail in Chapter 6)
- No DFS requirement — no radar systems use 2.4 GHz

**FCC Point-to-Point outdoor rule detail:**
- For every 3 dB that antenna gain exceeds 6 dBi, transmitter power must be reduced 1 dB
- This keeps EIRP from growing unboundedly on high-gain outdoor point-to-point links
- Example: If antenna gain = 24 dBi (18 dB above 6 dBi baseline → 6 reductions of 1 dB each → transmitter power must be reduced 6 dB from the standard limit)

---

### 5.12.2 The 5 GHz UNII Bands — FCC Rules

The 5 GHz band is divided into multiple UNII (Unlicensed National Information Infrastructure) sub-bands, each with different power limits and DFS requirements:

| Sub-band | Frequency Range | Max EIRP | DFS Required? | Indoor Only? |
|---|---|---|---|---|
| UNII-1 | 5.150 – 5.250 GHz | 50 mW (17 dBm) | No | Originally yes; now indoor/outdoor allowed |
| UNII-2A | 5.250 – 5.350 GHz | 250 mW (24 dBm) | **Yes** | No |
| UNII-2C (Extended) | 5.470 – 5.725 GHz | 250 mW (24 dBm) | **Yes** | No |
| UNII-3 | 5.725 – 5.850 GHz | 1 W (30 dBm) | No | No |

**Why DFS is required in UNII-2A and UNII-2C:**
- These sub-bands overlap with frequency allocations used by:
  - Weather radar systems (NEXRAD)
  - Military radar systems
  - Terminal Doppler Weather Radar
  - Airport surveillance radar
- DFS ensures unlicensed Wi-Fi devices don't interfere with these safety-critical licensed systems
- AP must perform a **Channel Availability Check (CAC)** — listen for 60 seconds before transmitting on a DFS channel to confirm no radar present
- After radar is detected: AP must **vacate the channel within 10 seconds** and avoid it for a **non-occupancy period of 30 minutes**

**Practical impact of DFS:**
- APs on DFS channels may take 60 seconds to become available after a reboot (CAC period)
- DFS events cause temporary client disconnection — a real operational concern in radar-dense areas
- Some enterprise deployments avoid DFS channels entirely for predictability despite reduced available channels

---

### 5.12.3 The 6 GHz UNII-5 Through UNII-8 Bands

- Added by FCC in April 2020 — a major spectrum expansion for Wi-Fi
- Frequency range: 5.925 – 7.125 GHz (1200 MHz of new spectrum)
- Divided into UNII-5, UNII-6, UNII-7, UNII-8 sub-bands
- Three power tiers for different device types:
  - **Standard Power (SP):** highest power — requires AFC (Automated Frequency Coordination) to protect incumbent licensed users
  - **Low Power Indoor (LPI):** indoor only, no AFC required
  - **Very Low Power (VLP):** portable devices, very low power
- No legacy 2.4/5 GHz devices exist here — 6 GHz is exclusively Wi-Fi 6E and newer
- Result: much less interference and congestion vs. the crowded 2.4 GHz and increasingly congested 5 GHz bands

---

## 5.13 OSI Model Application — Diagnostic Framework

The OSI model isn't just a memorization topic — it's a **systematic troubleshooting framework**. Here's how to use it diagnostically in Wi-Fi:

### The Layer-by-Layer Wi-Fi Diagnostic Process

**Layer 1 — Physical:**
- Check: RSSI (signal strength), SNR, noise floor, channel utilization
- Tools: spectrum analyzer, Wi-Fi scanner, AP management console
- Problems at this layer: low RSSI, high noise floor, interference, incorrect antenna, cable/connector issue (VSWR), physical obstruction
- **If Layer 1 is healthy (good RSSI, good SNR) → problem is NOT RF → move to Layer 2**

**Layer 2 — Data Link (MAC sublayer):**
- Check: association status, authentication state, frame retransmission rates, BSSID connectivity
- Tools: protocol analyzer (Wireshark with Wi-Fi adapter in monitor mode)
- Problems at this layer: 802.1X auth failure, PMKID mismatch, AP association table full, 4-way handshake failure, incorrect VLAN assignment at AP
- **If Layer 2 is healthy (client associates and authenticates successfully) → move to Layer 3**

**Layer 3 — Network:**
- Check: IP address assignment (DHCP), default gateway reachability, routing table
- Tools: ipconfig/ifconfig, ping, traceroute
- Problems at this layer: DHCP scope exhausted, DHCP server unreachable, incorrect VLAN routing, default gateway misconfiguration
- **If Layer 3 is healthy (client has correct IP, can reach gateway) → move to Layer 4+**

**Layer 4+ — Transport and Above:**
- Check: DNS resolution, specific application connectivity, firewall rules, NAT
- Tools: nslookup, application-specific testing
- Problems here: DNS failure, firewall blocking specific ports, application server issues

**Why this matters:**
- Without this framework, technicians waste time checking RF signal when the problem is DHCP, or reconfiguring VLAN assignments when the problem is simply low RSSI
- The OSI model gives you a structured path: start at Layer 1, confirm it's healthy, move up

**Scenario — Using OSI Model to Diagnose:**
- User reports: "Wi-Fi shows connected but I can't access anything"
- Step 1: Check Layer 1 — RSSI is −58 dBm, SNR is 28 dB → Layer 1 healthy
- Step 2: Check Layer 2 — client shows "connected" to the SSID → association succeeded → Layer 2 healthy
- Step 3: Check Layer 3 — client has IP 169.254.x.x (APIPA address) → DHCP failed → **Layer 3 problem**
- Diagnosis: DHCP server unreachable or DHCP scope exhausted — not a Wi-Fi problem at all
- Fix: investigate DHCP server, not the AP or RF environment

---

## 5.14 Standards Deep Dive — What Each Amendment Actually Changed

Understanding *why* each amendment was created (what problem it solved) is more useful than memorizing a table.

### 802.11b (1999) — Why it drove mass adoption
- First 802.11 amendment to achieve real commercial success
- 11 Mbps at 2.4 GHz using HR-DSSS (High Rate Direct Sequence Spread Spectrum)
- Lower cost than 802.11a (5 GHz hardware was more expensive in 1999)
- 2.4 GHz penetrated buildings better — more practical for real deployments
- **Problem it created:** Crowded the 2.4 GHz band; only 3 non-overlapping channels in USA

### 802.11a (1999) — Why it was technically superior but commercially slower
- 54 Mbps at 5 GHz using OFDM
- More channels available at 5 GHz, less congestion
- **Problem:** 5 GHz hardware more expensive in 1999; shorter range than 2.4 GHz; didn't interoperate with 802.11b — slow commercial adoption

### 802.11g (2003) — Why it was a critical bridge
- 54 Mbps at 2.4 GHz — brought 802.11a's speed to 802.11b's frequency
- Backward compatible with 802.11b — existing b devices still worked on g networks
- **Problem it created:** When b clients join a g network, they slow down the entire network because the AP must use protection mechanisms (CTS-to-Self or RTS/CTS) to prevent b/g collisions — reducing overall throughput

### 802.11n (2009, Wi-Fi 4) — Why it was a major architectural shift
- First amendment to introduce MIMO (multiple spatial streams)
- First dual-band standard (2.4 AND 5 GHz)
- Channel bonding introduced: 20 MHz → 40 MHz channels (doubling channel width = roughly doubling throughput)
- Up to 4 spatial streams; theoretical max 600 Mbps
- **Problem it created:** 40 MHz channels in 2.4 GHz consume almost the entire band — leaves almost no room for neighboring APs on non-overlapping channels

### 802.11ac (2013, Wi-Fi 5) — Why 5 GHz only was a deliberate decision
- 5 GHz only — deliberately dropped 2.4 GHz to focus on the cleaner, wider band
- 80 MHz mandatory channel width; 160 MHz optional
- MU-MIMO downlink (AP to multiple clients simultaneously)
- 256-QAM modulation (denser data encoding than 802.11n's 64-QAM)
- Up to 8 spatial streams; theoretical max 6.9 Gbps
- **Problem it addressed:** The 2.4 GHz band was too congested and too narrow for the wider channels needed for multi-Gbps speeds

### 802.11ax (2019, Wi-Fi 6) — Why efficiency, not just speed, was the goal
- Previous amendments focused on peak throughput — 802.11ax focused on **average throughput in dense environments**
- Key innovations:
  - **OFDMA** — subdivides channel into Resource Units (RUs) to serve multiple clients simultaneously within one transmission
  - **Uplink MU-MIMO** — clients can transmit to AP simultaneously (not just AP-to-client)
  - **BSS Coloring** — reduces co-channel interference in dense deployments by tagging frames with a color to identify which BSS they belong to
  - **TWT (Target Wake Time)** — IoT/battery devices schedule specific wake windows, dramatically reducing power consumption and channel contention
  - **1024-QAM** — even denser modulation for higher peak speeds
- **Problem it addressed:** High-density environments (stadiums, airports, conference centers) where many clients compete for airtime, causing degraded average performance even with high-speed APs

---

## 5.15 Teach-Back Drills

### Drill A — Non-technical manager
**Q:** "We're opening offices in three countries — USA, Germany, and Japan. Can we just buy all our APs in the USA and ship them everywhere?"

**Model answer:**
> "Technically you could buy the same hardware model if it supports multiple regulatory domains — many enterprise APs do. But you absolutely cannot ship them pre-configured with USA settings. Each country has different laws about which Wi-Fi frequencies are legal and at what power. Before deploying in Germany or Japan, each AP must be configured with the correct country setting for where it's installed. Deploy with the wrong setting and you're potentially violating telecommunications law in those countries."

---

### Drill B — Junior technician
**Q:** "A user says Wi-Fi is broken — where do you start diagnosing?"

**Model answer:**
> "I start at Layer 1 — check their signal strength and SNR first. If the RF is healthy, I move to Layer 2 and check whether the device is actually associating and authenticating to the AP successfully. If association is fine, I go to Layer 3 and check whether they're getting an IP address and can reach the default gateway. Each layer either clears or points at a specific area — which saves a lot of time versus randomly changing settings hoping something fixes it."

---

### Drill C — Fellow CWNA candidate
**Q:** "Why did 802.11ax focus on efficiency rather than raw peak speed, and what specific mechanisms does it use to achieve that?"

**Model answer:**
> "By the time 802.11ax was being designed, peak throughput wasn't the real-world problem anymore — 802.11ac already delivered multi-Gbps. The actual problem was that in dense deployments, 30 clients fighting over one AP's airtime meant each client got terrible average performance even if the theoretical speed was high. 802.11ax addresses this with three main mechanisms: OFDMA divides the channel into resource units that let the AP serve multiple clients within a single transmission instead of one at a time; uplink MU-MIMO lets multiple clients transmit simultaneously to the AP; and TWT lets IoT devices schedule their awake windows so they don't compete for airtime when they don't need to. BSS Coloring reduces wasted airtime from overhearing neighboring APs' transmissions. Combined, these mechanisms improve average throughput per client in dense environments — which is where the real-world problem was."

---

## 5.16 Self-Check Before Moving to Chapter 6

- [ ] Can name ITU-R's three regions and explain its role vs. national regulators like FCC
- [ ] Know FCC power limits for 2.4 GHz (30 dBm) and the four 5 GHz UNII sub-bands
- [ ] Can explain DFS — what triggers it, the 10-second vacate requirement, 30-minute non-occupancy, and CAC
- [ ] Can explain 6 GHz's three power tiers (Standard Power/AFC, LPI, VLP)
- [ ] Can place 802.11 at the correct OSI layers and explain what each layer handles in a Wi-Fi context
- [ ] Can use the OSI model as a diagnostic framework (Layer 1 → 2 → 3 → 4+)
- [ ] Can explain what problem each major amendment (b, a, g, n, ac, ax) was designed to solve — not just what speeds they achieve
- [ ] Can distinguish IEEE (writes spec) vs. Wi-Fi Alliance (certifies products) clearly

---

## 5.17 Final Practice Set

1. **(Recall)** Which 5 GHz UNII sub-bands require DFS, and what is the channel vacate time requirement when radar is detected?
2. **(Applied)** A user's laptop shows "Connected" to the SSID with −60 dBm RSSI and 25 dB SNR, but has IP address 169.254.1.15. What layer is the problem at, what is the specific issue, and what should you check first?
3. **(Applied)** An enterprise customer asks why you're recommending Wi-Fi 6 APs for their new high-density conference center rather than the cheaper Wi-Fi 5 APs that also offer multi-Gbps speeds. Give a technically accurate answer.
4. **(Teach)** In 3 sentences, explain to a non-technical person what DFS is and why it occasionally causes their Wi-Fi to drop for a few seconds.

### Answers
1. UNII-2A (5.250–5.350 GHz) and UNII-2C Extended (5.470–5.725 GHz). When radar is detected, the AP must vacate the channel within **10 seconds** and may not return for a **30-minute non-occupancy period**
2. Layer 3 — 169.254.x.x is an APIPA (Automatic Private IP Addressing) address, assigned by the OS when DHCP fails. Layers 1 and 2 are healthy (good RSSI/SNR, connected to SSID). Check: Is the DHCP server reachable? Is the DHCP scope exhausted? Is the VLAN trunk configured correctly between the AP and the DHCP server?
3. "Wi-Fi 5 was designed to maximize peak throughput — great for one or a few devices. Wi-Fi 6 was specifically engineered for dense environments with many simultaneous clients. In a conference center with 50+ clients competing for airtime, OFDMA lets the AP serve multiple clients in a single transmission window instead of one at a time, TWT reduces unnecessary airtime competition from background devices, and BSS Coloring reduces co-channel interference from neighboring APs in adjacent rooms. The result is better average throughput per client under real-world load — which is exactly the condition a conference center creates."
4. "Some Wi-Fi channels share frequency space with radar systems used for weather tracking and air traffic control. The law requires that if a Wi-Fi access point detects radar nearby, it must immediately stop using that channel and switch to a different one. That brief switch is what causes the momentary disconnection — it's actually the system working correctly to avoid interfering with safety-critical radar."

---

**Day 5 complete — rewritten to full depth.** Next: Chapter 6 — IEEE 802.11 Standard and Amendments (PHY types, channel plans, MCS tables, modulation schemes, and what each amendment technically changed at the PHY and MAC level).
