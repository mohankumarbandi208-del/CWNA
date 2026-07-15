# Chapter 7: Spread Spectrum Technologies — Part 2 (Applied + Practice)
**CWNA-109 | Book Chapter 6**

---

## 7.12 PLCP Frame Structure — Full Detail

Understanding how the PLCP preamble and header are structured lets you diagnose PHY-level issues and understand why certain legacy compatibility decisions cost throughput.

### 7.12.1 DSSS PLCP Frame (Long Preamble)

```
|<---- PLCP Preamble ---->|<-- PLCP Header -->|<-- PSDU (MAC frame) -->|
|  Sync  | SFD (16 bits)  | Signal | Service | Length | CRC | Data...  |
| 128 bits|               |  8 bits| 8 bits  | 16 bits|16bit|          |
|<------------- 192 μs at 1 Mbps DBPSK ------->|       Variable        |
```

**Field definitions:**
- **Sync (128 bits):** Alternating 0s and 1s — allows receiver to synchronize clock and detect the signal
- **SFD (Start Frame Delimiter, 16 bits):** Fixed pattern (1111 0011 1010 0000) — signals end of preamble, start of header
- **Signal (8 bits):** Indicates the data rate to be used for the PSDU (the MAC frame payload)
- **Service (8 bits):** Reserved bits (originally defined for extensions; mostly unused in practice)
- **Length (16 bits):** Duration of the PSDU in microseconds — used to set the NAV (Network Allocation Vector) for virtual carrier sense
- **CRC (16 bits):** Error check on the PLCP header fields

**Key point:** The PLCP preamble and header are ALWAYS transmitted at the base rate (1 Mbps DBPSK for DSSS) regardless of the data rate used for the PSDU — because all devices in the BSS must be able to hear and decode the preamble, even those that can't receive at the higher data rate

### 7.12.2 DSSS PLCP Frame (Short Preamble — 802.11b optional)

```
|<- PLCP Preamble ->|<-- PLCP Header -->|<-- PSDU -->|
|  Sync  |   SFD    | Signal |Service|Length| CRC | Data|
| 56 bits| 8 bits   | 8 bits | 8 bits|16 bits|16bit|    |
|<---- 96 μs at 1 Mbps DBPSK --->|<- Header at 2 Mbps DQPSK ->|
```

**Differences from long preamble:**
- Sync reduced from 128 to 56 bits — saves 72 bits × 1 μs/bit = 72 μs
- SFD changed to 8 bits
- Header transmitted at 2 Mbps DQPSK instead of 1 Mbps DBPSK → saves additional time
- Total overhead: 96 μs vs. 192 μs → 96 μs saved per frame

**Throughput calculation of short preamble benefit:**
- At high frame rates (e.g., 1000 frames/second), saving 96 μs/frame = 96 ms/second saved on overhead
- That 96 ms/second reclaimed for data transmission → meaningful throughput improvement at high traffic loads

### 7.12.3 OFDM PLCP Frame (802.11a)

```
|<-- PLCP Preamble -->|<- SIGNAL field ->|<-- DATA field -->|
| Short training (8×) | Long training (2×)| Rate|Reserved|Length|Parity|Tail| SERVICE | PSDU | Pad |
|    10 OFDM symbols  |   2 OFDM symbols  |4bits| 1 bit  |12bits| 1bit |6bit| 16 bits | Var  |     |
|<-------------- 20 μs preamble -------->|<----------- 4 μs SIGNAL -------->|
```

**Short training symbols:** 10 repetitions of a known pattern — receiver uses for:
- Signal detection (is there a signal present?)
- Automatic Gain Control (AGC) — set receiver amplifier gain
- Coarse frequency offset correction
- Timing synchronization

**Long training symbols:** 2 copies of known OFDM symbols — receiver uses for:
- Fine frequency offset correction
- Channel estimation (measuring how the channel affects each subcarrier)

**SIGNAL field:** Transmitted at BPSK, 1/2 coding (most robust rate) — contains the data rate and length info — even if the PSDU uses 64-QAM, the SIGNAL field is always BPSK so every OFDM device can read it

**Key comparison to DSSS preamble:**
- OFDM preamble: 20 μs total vs. DSSS long preamble: 192 μs
- This 172 μs difference per frame is significant at high throughput — one of many reasons OFDM is more efficient than DSSS

### 7.12.4 HT (802.11n) Mixed Mode PLCP Frame

802.11n introduced a Mixed Mode preamble for backward compatibility:

```
|<Non-HT Legacy preamble>|<HT-SIG>|<HT-STF>|<HT-LTF>|<HT Data>|
|  L-STF | L-LTF | L-SIG  |  2 syms | 1 sym  |  1+ sym | PSDU    |
|<-- Legacy OFDM (20 μs) -->|<-------- HT-specific fields -------->|
```

- **Non-HT Legacy portion:** Identical to 802.11a OFDM preamble — allows 802.11a/g devices to decode the duration and set their NAV correctly (even though they can't decode the HT data)
- **HT-SIG (HT Signal):** Contains HT-specific parameters: MCS index, channel width (20/40 MHz), guard interval, spatial stream count, etc.
- **HT-STF (HT Short Training Field):** Helps receiver prepare for MIMO signal processing
- **HT-LTF:** One per spatial stream — receiver uses for per-stream channel estimation in MIMO

**Greenfield mode (alternative — no backward compatibility):**
- No legacy preamble at the start
- Shorter total preamble → higher efficiency
- **Problem:** 802.11a/g devices cannot detect the start of Greenfield frames and cannot set their NAV — creates collision risk in mixed environments
- **Result:** Greenfield mode was almost never deployed in practice — Mixed Mode is the real-world standard

---

## 7.13 Scenario: Diagnosing PLCP-Level Issues with a Protocol Analyzer

**Setup:**
- Enterprise 802.11n network on 2.4 GHz
- Users report inconsistent throughput — sometimes fast, sometimes slow — with no obvious pattern
- RSSI is consistently good (−55 dBm), SNR is good (30 dB)
- No spectrum analyzer anomalies detected

**Analysis using PLCP knowledge:**
- Step 1: Good RF (Layer 1 physical signal quality) rules out antenna/propagation problems
- Step 2: Inconsistent throughput with good signal → look at PHY overhead and frame-level behavior
- Step 3: Protocol analyzer in monitor mode captures 802.11 frames
- Step 4: Analysis reveals: AP is frequently switching between Mixed Mode and long preamble DSSS transmissions
- Step 5: Why? A legacy 802.11b device (long preamble only) is associated somewhere in the BSS — not visibly "causing problems" because it's barely transmitting, but its presence is forcing the AP to use protection mechanisms and long preambles for all transmissions
- Step 6: Each frame now carries an extra 96–172 μs of preamble overhead that wouldn't be there without the legacy device

**Conclusion:**
> "The throughput variability is being caused by a legacy device (802.11b) forcing higher preamble overhead across all transmissions. Identify and remove or isolate the 802.11b device."

**Tool used:** Protocol analyzer (Wireshark with Wi-Fi adapter in monitor mode, or dedicated WLAN analyzer like Ekahau Sidekick)

---

## 7.14 Scenario: FHSS vs. DSSS Coexistence in the 2.4 GHz Band

**Setup:**
- A legacy industrial control system uses FHSS radios in the 2.4 GHz band (non-802.11 devices)
- A new 802.11n WLAN is deployed in the same facility
- Users report intermittent but brief packet loss across all channels

**Analysis:**
- Step 1: FHSS radios hop through 79 channels across the 2.4 GHz band pseudo-randomly
- Step 2: 802.11n DSSS/OFDM operates on fixed channels (e.g., 1, 6, 11)
- Step 3: Every time the FHSS radio hops onto channel 1, 6, or 11 frequency range → brief collision with 802.11n transmissions
- Step 4: The interference is brief (FHSS dwell time is short — typically 20–50 ms) but happens across all three 802.11n channels over time
- Step 5: This explains "intermittent, brief packet loss across all channels" — the hop sequence hits each channel periodically

**Key diagnostic sign:** Spectrum analyzer shows a signal hopping rapidly across the entire 2.4 GHz band — not sitting on any one frequency

**Solutions:**
- Option 1: Replace FHSS industrial radios with 900 MHz or 5 GHz alternatives (removes the coexistence problem entirely)
- Option 2: Implement FHSS AFH (Adaptive Frequency Hopping) if the FHSS equipment supports it → FHSS radios learn to avoid the 802.11n channel frequencies
- Option 3: Migrate 802.11n to 5 GHz — FHSS devices won't follow to 5 GHz

---

## 7.15 CCK vs. Barker Code — Performance Comparison

**Worked comparison: why CCK was necessary for 11 Mbps**

**Barker code at 11 Mbps scenario (why it doesn't work):**
- Barker code: 11 chips per data bit
- At 11 Mbps: would need 11 Mcps × 11 chips/bit = 121 Mcps chip rate
- Problem: 121 Mcps would require approximately 121 MHz of bandwidth (far exceeds the 22 MHz available channel)
- Therefore: Barker code physically cannot achieve 11 Mbps in a 22 MHz channel

**CCK solution:**
- CCK uses 8-chip code words — each code word encodes multiple bits, not one
- At 11 Mbps: 8 chips/code word × 1.375 Mbps (code words/sec) × 8 bits/code word = 11 Mbps
- Chip rate remains 11 Mcps → bandwidth remains 22 MHz
- More bits per code word (8) → higher data rate in the same chip rate and channel width

**This is the exact engineering tradeoff CCK solved:**
- Same chip rate (11 Mcps) → same bandwidth (22 MHz) → regulatory compliance maintained
- More bits per symbol through code word selection → data rate multiplied
- Spread spectrum properties maintained through complementary code design

---

## 7.16 Deeper Problem: Short GI vs. Long GI in DSSS Context

**Question:** Does the guard interval concept (short GI vs. long GI from Chapter 6) apply to DSSS transmissions?

**Answer:** No — guard interval is an OFDM concept, not a DSSS concept. Here's why:

**In OFDM:**
- Multiple subcarriers transmit simultaneously
- Each subcarrier's symbol has a defined duration
- Multipath causes late-arriving copies of a symbol to interfere with the next symbol → guard interval needed

**In DSSS:**
- Single carrier transmission using spreading code
- Multipath is handled differently:
  - The Barker code and CCK codes have autocorrelation properties that make them naturally resistant to multipath
  - The DSSS receiver uses a RAKE receiver — a type of receiver that deliberately collects multiple multipath copies of the signal and combines them constructively
  - This is the DSSS equivalent of multipath handling — not a guard interval, but a receiver architecture

**RAKE receiver concept:**
- Multiple correlators (fingers) each lock onto a different multipath arrival of the signal
- Each finger independently despreads its copy
- The copies are then time-aligned and combined with appropriate weighting → better SNR than any single copy alone
- This is why DSSS systems can perform well in multipath environments without guard intervals

---

## 7.17 Teach-Back Drills

### Drill A — Non-technical manager
**Q:** "Why does our old wireless scanner slow down the whole network even though it barely transmits anything?"

**Model answer:**
> "When an old device joins a modern Wi-Fi network, the access point has to switch into a compatibility mode for every single transmission — not just the ones going to that old device. It's like having everyone in a meeting speak slowly and repeat themselves just because one participant doesn't speak English well — even conversations that have nothing to do with that person are affected. The extra overhead those compatibility measures add up across hundreds of transmissions per second and noticeably reduces everyone's speed."

---

### Drill B — Junior technician
**Q:** "What's the actual difference between the PLCP and PMD sublayers, and why does the split matter?"

**Model answer:**
> "The PLCP is like the envelope and cover letter — it wraps the data in the headers and preamble that tell the receiver what's coming: how fast the data will be sent, how long the frame is, and enough synchronization information that the receiver can lock on. The PMD is the mail truck — it takes that envelope and physically delivers it by modulating and spreading the bits onto the radio waves. The split matters because the PLCP is largely the same regardless of which specific spread spectrum technology you use, while the PMD changes completely between DSSS and OFDM. It's a clean separation between 'framing and synchronization' and 'actual transmission technology.'"

---

### Drill C — Fellow CWNA candidate
**Q:** "Walk through exactly how DSSS despreading provides interference rejection — at the mathematical level."

**Model answer:**
> "When the receiver despresses the incoming signal, it multiplies the received signal by a local copy of the same chipping code. For the desired signal: desired signal × chipping code × chipping code = desired signal × 1 = desired signal recovered (because XOR with the same code twice returns the original). For an interfering signal: the interferer doesn't carry the chipping code, so multiplying it by the chipping code SPREADS it across the bandwidth rather than concentrating it. After despreading, the narrowband interferer becomes a low-level noise floor spread across the entire chip bandwidth — its peak power relative to the data signal is reduced by the processing gain (~10.4 dB for 802.11b). The narrowband filter at the despreader output passes the concentrated desired signal but passes only a fraction of the now-spread interference. This is how ~10 dB of interference rejection is achieved through the XOR despreading operation — it's correlation gain, not amplification."

---

## 7.18 Self-Check Before Moving to Chapter 8

- [ ] Can draw the DSSS long preamble structure from memory and name each field's purpose
- [ ] Can explain why the PLCP preamble is always transmitted at the base rate even if the PSDU uses a higher rate
- [ ] Can calculate processing gain for any chip rate / data rate combination
- [ ] Can explain why CCK was necessary for 11 Mbps and what specifically limits the Barker code to 1/2 Mbps
- [ ] Can explain the RAKE receiver concept and how it differs from OFDM's guard interval approach to multipath
- [ ] Can diagnose FHSS coexistence interference (spectrum analyzer signature: hopping signal across entire 2.4 GHz band)
- [ ] Can diagnose legacy device overhead impact using a protocol analyzer
- [ ] Can explain the Mixed Mode vs. Greenfield HT preamble tradeoff in 802.11n

---

## 7.19 Final Practice Set

1. **(Recall)** What is the SFD in a DSSS PLCP frame, and what is its function?
2. **(Applied)** An 802.11n AP on 2.4 GHz shows protocol analyzer data indicating that all frames use long preamble. The network was supposed to be optimized for short preamble. What is the most likely cause?
3. **(Applied)** A facility uses both 802.11ac (5 GHz) APs and legacy FHSS industrial radios (2.4 GHz). An engineer proposes the FHSS radios will interfere with the 802.11ac network. Is this correct? Explain fully.
4. **(Teach)** Explain to a junior technician, in 3 sentences, why the OFDM preamble at 20 μs is so much shorter than the DSSS long preamble at 192 μs, and what this means for throughput.

### Answers
1. SFD = Start Frame Delimiter — a fixed 16-bit pattern (1111 0011 1010 0000) appended immediately after the Sync field in the PLCP preamble. Its function: signals to the receiver that the synchronization (Sync) field has ended and the PLCP header is about to begin. The receiver uses it as a framing marker to know when to switch from sync processing to header decoding
2. Most likely cause: at least one device in the BSS only supports long preamble — either a very old 802.11b device or an original 802.11 DSSS device. Short preamble is an optional feature in 802.11b; if any associated device only understands long preamble, the AP must use long preamble for all transmissions for backward compatibility. Solution: identify the non-short-preamble device using the protocol analyzer (look for which device's frames are triggering long-preamble responses) and upgrade or remove it
3. Incorrect — FHSS industrial radios operate in the 2.4 GHz ISM band; 802.11ac operates exclusively in the 5 GHz band. These two bands do not overlap. The FHSS radios cannot interfere with the 802.11ac network because they transmit on completely different frequencies. IF the facility also had 802.11n (2.4 GHz) or 802.11ax (2.4 GHz) APs, the FHSS radios COULD interfere with those — but not with the 5 GHz 802.11ac network
4. "The DSSS preamble is long because it needs 128 bits of alternating ones and zeros just for the receiver's clock to synchronize, all transmitted at only 1 Mbps — that takes 128 microseconds just for that one field. OFDM is smarter about synchronization — it uses known mathematical patterns called training symbols that can be processed much faster with modern DSP hardware, achieving full synchronization in 20 microseconds total. That 172 microsecond saving per frame doesn't sound huge, but at thousands of frames per second it adds up to a significant percentage of recovered airtime going to actual data instead of overhead."

---

**Day 7 complete.** Next: Chapter 8 — WLAN Topologies (BSS, IBSS, ESS, MBSS, DS, distribution system components, roaming, and how 802.11 topologies map to real-world deployments).
