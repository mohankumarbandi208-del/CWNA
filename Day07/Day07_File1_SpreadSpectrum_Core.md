# Chapter 7: Spread Spectrum Technologies — Part 1 (Core Concepts)
**CWNA-109 | Book Chapter 6**

---

## 7.1 Why Spread Spectrum Exists — The Problem It Solves

- Narrowband transmission = all signal power concentrated at one frequency
- Problem with narrowband: a single source of interference at that frequency wipes out the entire signal
- Also vulnerable to intentional jamming — a jammer only needs to target one frequency
- Spread spectrum = deliberately spreading signal power across a much wider frequency range than the data rate strictly requires
- Result: interference at any single frequency affects only a small portion of the spread signal → dramatically improved noise and interference resistance

**Three fundamental benefits of spread spectrum:**
1. **Interference resistance** — narrowband interference only degrades a fraction of the spread signal
2. **Low probability of intercept** — signal looks like noise to a receiver not synchronized to the spreading code
3. **Multiple access** — different spreading codes allow multiple users to share the same frequency band simultaneously (basis of CDMA cellular)

**Wi-Fi uses spread spectrum because:**
- It operates in unlicensed ISM/UNII bands shared with microwave ovens, Bluetooth, cordless phones, and other devices
- Spread spectrum provides resilience in this noisy, contested shared medium
- FCC Part 15 rules specifically require spread spectrum techniques for unlicensed transmitters above certain power levels in the ISM band

---

## 7.2 The 802.11 PHY Sublayer Architecture

Before covering individual spread spectrum technologies, you need to understand how the PHY layer is structured — because spread spectrum is implemented within the PHY.

### 7.2.1 PHY Sublayer Split

The 802.11 PHY is divided into two sublayers:

**PLCP (Physical Layer Convergence Procedure):**
- Upper PHY sublayer
- Prepares MAC frames for transmission over the wireless medium
- Adds the **PLCP preamble** and **PLCP header** to each frame
- The preamble contains synchronization information so the receiver can lock onto the signal
- The header contains: data rate, frame length, and other PHY-specific parameters
- PLCP is the interface between the MAC and the PMD

**PMD (Physical Medium Dependent):**
- Lower PHY sublayer
- Responsible for actual modulation, encoding, and transmission of bits onto the RF medium
- Implements the specific spread spectrum technique (DSSS, OFDM, etc.)
- Different amendments (b, a, g, n, ac, ax) differ primarily in what the PMD does

**Frame flow (transmit direction):**
```
MAC Frame (MPDU)
       ↓
PLCP adds preamble + header → PPDU (PLCP Protocol Data Unit)
       ↓
PMD modulates and spreads the PPDU onto RF
       ↓
Antenna radiates signal
```

**Frame flow (receive direction):**
```
Antenna receives RF signal
       ↓
PMD demodulates and despreads → recovers PPDU
       ↓
PLCP strips preamble + header → recovers MAC frame
       ↓
MAC processes the frame
```

### 7.2.2 PLCP Preamble Types

The preamble is critical — it's what allows the receiver to synchronize with the transmitter before actual data is decoded.

| Preamble Type | Used in | Length | Purpose |
|---|---|---|---|
| Long preamble | 802.11b HR-DSSS | 192 μs | Mandatory for 1/2 Mbps; compatible with all b devices |
| Short preamble | 802.11b HR-DSSS (optional) | 96 μs | Optional; reduces overhead; not all b devices support it |
| OFDM preamble | 802.11a | 20 μs | Shorter; faster; used with OFDM only |
| HT preamble (Mixed Mode) | 802.11n | Variable | Backward compatible with a/g devices |
| HT preamble (Greenfield) | 802.11n | Shorter | HT-only mode; no legacy compatibility |

**Why preamble length matters:**
- Preamble is pure overhead — data is not carried in the preamble
- Longer preamble = more overhead per frame = lower effective throughput
- Short preamble in 802.11b reduces overhead from 192 μs to 96 μs → measurable throughput improvement
- But short preamble only works if ALL devices in the BSS support it — one long-preamble-only device forces the AP back to long preamble for all transmissions

---

## 7.3 FHSS — Frequency Hopping Spread Spectrum (Deep Dive)

### 7.3.1 How FHSS Works

- The transmitter and receiver share a **pseudo-random hopping sequence** — a pre-agreed pattern of frequencies to hop through
- Both devices hop through the sequence in perfect synchronization
- Signal dwells on each frequency for a brief **dwell time** before hopping to the next

**FHSS parameters in original 802.11:**
- Hop sequence: 79 channels (each 1 MHz wide) across the 2.4 GHz ISM band (2.402–2.480 GHz)
- Dwell time: maximum 400 ms per channel (FCC requirement)
- Hop rate: must hop at least 2.5 times per second (minimum FCC requirement)
- Actual 802.11 FHSS: hops much faster than minimum — typically every 20–50 ms

### 7.3.2 FHSS Spreading Gain

- Spreading gain = measure of how much the signal is spread vs. its information bandwidth
- For FHSS: signal spreads across 79 MHz of bandwidth even though the data rate only needs ~1 MHz
- This spreading provides interference resistance — an interferer on any one frequency only disrupts the signal for the brief dwell time before it hops away

### 7.3.3 FHSS Modulation — GFSK

- Original 802.11 FHSS used **GFSK (Gaussian Frequency Shift Keying)**
- 1 Mbps: 2-level GFSK (2GFSK) — two frequency deviations represent 0 and 1
- 2 Mbps: 4-level GFSK (4GFSK) — four frequency deviations represent 00, 01, 10, 11

### 7.3.4 Multiple FHSS Networks Coexisting

- Different FHSS networks use different hopping sequences
- Because sequences are pseudo-random and independent, two FHSS networks on different sequences only occasionally land on the same frequency at the same time
- When they do collide on the same frequency simultaneously → brief interference → only affects that hop
- This allows multiple FHSS networks to share the 2.4 GHz band with acceptable performance degradation

**Why FHSS was abandoned for Wi-Fi (beyond 802.11):**
- Maximum data rate achievable with FHSS in 2.4 GHz ISM band: ~2 Mbps (regulatory dwell time limits constrain how long you can stay on any frequency, limiting throughput)
- FCC Part 15.247 rules limit dwell time — you can't stay on one frequency long enough to achieve high data rates
- DSSS and OFDM can achieve much higher rates in the same band
- Bluetooth retained FHSS because it was optimized for low-power, short-range personal area networking — not high-throughput LAN applications

---

## 7.4 DSSS — Direct Sequence Spread Spectrum (Deep Dive)

### 7.4.1 How DSSS Works

- DSSS takes each data bit and replaces it with a longer sequence of chips using a spreading code
- This process is called **chipping** — the spreading code is called a **chip sequence** or **chipping code**
- The resulting signal occupies a wider bandwidth than the original data

**Step-by-step DSSS transmission:**
- Step 1: Data bit (0 or 1) arrives at the transmitter
- Step 2: Each data bit is XOR'd (exclusive-OR) with the chipping code sequence
- Step 3: The result is a longer chip sequence at a higher chip rate (chips per second)
- Step 4: The chip sequence is modulated onto the RF carrier (DBPSK or DQPSK)
- Step 5: The wider spread signal is transmitted

**Step-by-step DSSS reception:**
- Step 1: Receiver knows the same chipping code
- Step 2: Received signal is XOR'd again with the same code → original data bit is recovered
- Step 3: This process is called **despreading** — it correlates the received signal with the local copy of the chip sequence
- Step 4: Despreading also provides processing gain — interference that doesn't match the chip code is spread out and reduced in power

### 7.4.2 Processing Gain

- **Processing gain** = ratio of chip rate to data rate, expressed in dB
- Formula:
```
Processing Gain (dB) = 10 × log₁₀(chip rate / data rate)
```
- Original 802.11 DSSS: chip rate = 11 Mcps (mega chips per second); data rate = 1 Mbps
```
Processing Gain = 10 × log₁₀(11/1) = 10 × log₁₀(11) ≈ 10 × 1.041 = 10.41 dB
```
- This means DSSS provides approximately **10.4 dB of interference rejection** — interfering signals must be 10.4 dB stronger than the DSSS signal to cause significant degradation
- In practice: DSSS can operate in environments where narrowband interference is up to ~10 dB stronger than the signal

### 7.4.3 The Barker Code — 11-Chip Sequence

Original 802.11 DSSS and 802.11b at 1 and 2 Mbps uses the **Barker code:**

```
Barker code (11 chips): +1 −1 +1 +1 −1 +1 +1 +1 −1 −1 −1
```

**Properties of the Barker code:**
- Has very low autocorrelation for any shift other than zero — means the receiver can reliably detect the start of the sequence even in noisy conditions
- 11 chips per data bit at 1 Mbps → chip rate = 11 Mcps → spreads 1 MHz data across 11 MHz bandwidth
- At 2 Mbps (DQPSK): each symbol carries 2 bits → chip rate still 11 Mcps → 5.5 Mcps per bit equivalent

**Modulation for original 802.11 DSSS:**
- 1 Mbps: **DBPSK** (Differential Binary Phase Shift Keying) — each chip shifts phase by 180° for a 1, no shift for 0
- 2 Mbps: **DQPSK** (Differential Quadrature Phase Shift Keying) — four phase states, 2 bits per symbol

### 7.4.4 CCK — Complementary Code Keying (802.11b at 5.5 and 11 Mbps)

- Barker code is too inefficient for higher rates — 11 chips per 1-2 bits is a poor ratio for 5.5/11 Mbps
- 802.11b at 5.5 and 11 Mbps uses **CCK (Complementary Code Keying)** instead
- CCK uses an 8-chip complex code word where the specific code word chosen encodes data bits

**CCK at 5.5 Mbps:**
- Chip rate: 11 Mcps (same as Barker)
- 8 chips per symbol
- Each symbol encodes 4 data bits (2 bits determine the code word from a set of 64; 2 bits determine phase)
- Data rate: 11 Mcps ÷ 8 chips/symbol × ... → 5.5 Mbps

**CCK at 11 Mbps:**
- Chip rate: 11 Mcps
- 8 chips per symbol
- Each symbol encodes 8 data bits (6 bits choose from 64 code words; 2 bits determine phase)
- More bits per symbol than 5.5 Mbps → higher data rate in same chip rate

**Why CCK is cleverer than just using higher QAM:**
- CCK's complementary code properties make it robust against multipath — the codes are chosen so that multipath reflections don't corrupt the code word detection
- Maintaining spread spectrum properties even at 11 Mbps was a key design goal

### 7.4.5 PBCC — Packet Binary Convolutional Coding (Optional in 802.11b/g)

- An alternative optional PHY for 802.11b and 802.11g at 5.5 and 11 Mbps (and 22/33 Mbps in 802.11g)
- Uses convolutional coding (forward error correction) instead of CCK
- PBCC provides slightly better range than CCK at the same data rate because of its superior error correction
- **Rarely implemented** — most vendors chose CCK; PBCC is optional and uncommon
- CWNA tests that you know it exists as an optional alternative, not that you can implement it

---

## 7.5 OFDM as a Spread Spectrum Technology

OFDM is often taught separately from "spread spectrum" but it shares key properties:

**How OFDM achieves spread spectrum benefits:**
- Signal is spread across many subcarriers simultaneously → inherently resistant to narrowband interference
- If one subcarrier is wiped out by narrowband interference, only a small fraction of the total data is affected — FEC (forward error correction) can recover it
- The spreading is in the frequency domain (multiple carriers) rather than through a chipping sequence

**OFDM vs. DSSS interference resistance:**
- DSSS: resists interference through processing gain (spreading + despreading)
- OFDM: resists interference through frequency diversity (narrowband interference only affects a fraction of the total subcarriers) + FEC

**Why OFDM replaced DSSS for high-throughput:**
- DSSS at 11 Mbps (CCK) uses 22 MHz of bandwidth efficiently
- OFDM in the same 20 MHz channel achieves 54 Mbps (802.11a/g) to hundreds of Mbps (802.11n+)
- OFDM's spectral efficiency is dramatically higher — more bits per Hz of bandwidth

---

## 7.6 DSSS vs. FHSS vs. OFDM — Full Comparison

| Property | FHSS | DSSS | OFDM |
|---|---|---|---|
| Spreading method | Hops between narrow frequency channels | Chips with spreading code across wide band | Multiple parallel orthogonal subcarriers |
| 802.11 amendment | Original 802.11 | Original 802.11, 802.11b | 802.11a, g, n, ac, ax |
| Max data rate | 2 Mbps | 11 Mbps (802.11b) | 54 Mbps (a/g) → 9.6 Gbps (ax) |
| Interference resistance | Excellent (hops away from interference) | Good (processing gain ~10 dB) | Good (frequency diversity + FEC) |
| Narrowband interference | Excellent — only affects brief dwell time | Good — processing gain reduces impact | Good — only affects affected subcarriers |
| Multipath resistance | Poor — entire hop affected by multipath | Moderate — Barker/CCK provide some resistance | Excellent — long symbol duration absorbs multipath |
| Spectral efficiency | Low | Low-moderate | High |
| Complexity | Moderate (sync required) | Moderate | High (FFT processing required) |
| Still used in Wi-Fi? | No (abandoned after original 802.11) | Legacy only (802.11b backward compatibility) | Yes — all modern Wi-Fi |

---

## 7.7 The ISM Band and Coexistence

### 7.7.1 Bluetooth and FHSS Coexistence with Wi-Fi

- Bluetooth uses FHSS in the 2.4 GHz ISM band
- Bluetooth hops through 79 channels at 1600 hops/second (much faster than 802.11 FHSS)
- Bluetooth and Wi-Fi DSSS/OFDM can interfere with each other in 2.4 GHz

**Bluetooth interference mechanisms with Wi-Fi:**
- When a Bluetooth device hops onto the same frequency currently used by a Wi-Fi channel → collision → packet loss
- Frequency of collision depends on: channel overlap, proximity of devices, transmission timing

**Coexistence mechanisms:**
- **Adaptive Frequency Hopping (AFH):** Bluetooth feature — detects which frequencies are occupied by Wi-Fi and avoids hopping to them → reduces collision rate
- **Bluetooth/Wi-Fi coexistence signaling:** Some devices use a hardware signal between Bluetooth and Wi-Fi chips to time-division multiplex — they take turns, one pauses while the other transmits
- **5 GHz/6 GHz migration:** Moving Wi-Fi to 5/6 GHz eliminates Bluetooth interference entirely since Bluetooth only operates at 2.4 GHz

### 7.7.2 Microwave Oven Interference

- Microwave ovens operate at ~2.45 GHz — directly in the middle of Wi-Fi 2.4 GHz channels
- Emit broadband RF noise when operating (due to magnetron cycling)
- Primarily affects channels 6–11 (those nearest 2.45 GHz center)
- Interference is intermittent (only when oven is on) and broadband (affects multiple channels simultaneously)

**Diagnosis of microwave interference:**
- Spectrum analyzer shows periodic, broadband noise bursts in 2.4 GHz band
- Interference correlates with oven usage times (lunch hours, break times)
- Fix: migrate affected clients/APs to 5 GHz or 6 GHz; physically separate APs from kitchen areas

---

## 7.8 Common Misconceptions

| Misconception | Reality |
|---|---|
| "FHSS is just a slower version of DSSS" | They are fundamentally different spreading techniques. FHSS hops between frequencies; DSSS spreads a single channel with a chipping code. Different mechanisms, different trade-offs |
| "DSSS uses the entire 2.4 GHz band" | DSSS spreads across a 22 MHz channel — not the entire 83.5 MHz ISM band. The signal occupies one 22 MHz chunk |
| "Processing gain means the signal is amplified" | Processing gain is not power amplification — it's interference rejection gain from the despreading process. The signal isn't made stronger; interfering noise is effectively reduced |
| "CCK is a different spread spectrum type than DSSS" | CCK is an evolution of DSSS — it still uses the same 11 Mcps chip rate and the same 22 MHz channel. CCK replaces the Barker code with complementary codes for higher data rates but remains within the DSSS family |
| "OFDM is not spread spectrum" | While OFDM isn't spread spectrum in the traditional code-spreading sense, it achieves the same benefits (narrowband interference resistance, frequency diversity) through a different mechanism — parallel orthogonal subcarriers |

---

## 7.9 Chapter Summary (Say This Cold)

- Spread spectrum spreads signal power across wider bandwidth than strictly necessary → provides interference resistance, low intercept probability, multiple access capability
- PHY has two sublayers: PLCP (adds preamble + header for synchronization/framing) and PMD (actual modulation/spreading onto RF)
- FHSS: hops through 79 channels pseudo-randomly; 2.4 GHz; max 2 Mbps; abandoned for Wi-Fi due to rate ceiling; Bluetooth still uses it
- DSSS: spreads data with Barker code (11 chips, 1/2 Mbps) or CCK (8 chips, 5.5/11 Mbps); processing gain ≈10.4 dB; 22 MHz channel
- PBCC: optional alternative to CCK in 802.11b/g; better FEC; rarely implemented
- OFDM: not code-spreading but achieves same benefits via frequency diversity across orthogonal subcarriers; dramatically higher spectral efficiency than DSSS → all modern Wi-Fi
- 2.4 GHz coexistence challenges: Bluetooth (AFH mitigates), microwave ovens (broadband burst noise near 2.45 GHz), other DSSS/OFDM networks

---

## 7.10 Practice Problems

**Tier 1 — Recall**
1. What chipping code does original 802.11 DSSS and 802.11b use at 1 and 2 Mbps?
2. What is the chip rate of 802.11b DSSS/CCK?
3. What two sublayers make up the 802.11 PHY?

**Tier 2 — Applied**
4. Calculate the processing gain of 802.11b DSSS at 1 Mbps (chip rate 11 Mcps, data rate 1 Mbps). What does this value mean in practical terms?
5. A network uses 802.11b devices. Users near the office kitchen report intermittent connectivity loss around noon every day, while users elsewhere are unaffected. What is the most likely cause, and what tool would you use to confirm?
6. An 802.11b device uses the short preamble option. A legacy 802.11 DSSS device (that only supports long preamble) is also associated to the same AP. What must the AP do, and what is the throughput impact?

**Tier 3 — Teach-back**
7. Explain to a junior technician why 802.11 abandoned FHSS after the original standard, even though FHSS has excellent interference resistance.
8. Explain processing gain to a non-technical person using a real-world analogy.

---

## 7.11 Answer Key

1. Barker code — 11-chip sequence: +1 −1 +1 +1 −1 +1 +1 +1 −1 −1 −1
2. 11 Mcps (mega chips per second) — same for both Barker code (1/2 Mbps) and CCK (5.5/11 Mbps)
3. PLCP (Physical Layer Convergence Procedure) and PMD (Physical Medium Dependent)
4. Processing Gain = 10 × log₁₀(11/1) = 10 × 1.041 ≈ **10.4 dB**. This means interfering signals must be approximately 10.4 dB stronger than the DSSS signal to cause significant degradation — the spread spectrum processing rejects interference weaker than that threshold when despreading
5. Most likely cause: microwave oven interference. The kitchen microwave operates near 2.45 GHz and generates broadband RF noise bursts when in use — correlates with noon lunch usage. Users near the kitchen on 2.4 GHz channels close to 2.45 GHz (especially channels 6–11) are most affected. Confirmation tool: **spectrum analyzer** — will show periodic broadband noise bursts in the 2.4 GHz band correlating with oven operation
6. The AP must use the **long preamble** for all transmissions — because at least one device in the BSS (the legacy 802.11 device) only understands long preamble. Short preamble support is optional and backward compatibility requires the lowest common denominator. Throughput impact: every frame carries an extra 96 μs of overhead (192 μs − 96 μs = 96 μs saved per frame when short preamble is used) × total frame count → measurable but modest throughput reduction across all devices in the BSS
7. "FHSS's fundamental problem is a regulatory ceiling on data rate. The FCC limits how long a FHSS transmitter can dwell on any single frequency — this prevents a FHSS device from staying on one frequency long enough to transmit at high data rates. The maximum achievable rate in the ISM band under FCC FHSS rules is about 2 Mbps. That was acceptable in 1997, but DSSS could already do 11 Mbps in 1999 and OFDM could do 54 Mbps in the same band by 2003. FHSS simply couldn't scale to the data rates the market needed, so it was dropped for Wi-Fi — Bluetooth kept it because Bluetooth was designed for short-range, low-rate personal area networking where 2 Mbps is fine."
8. "Imagine you're trying to have a conversation in a loud factory. A narrowband radio is like trying to speak one normal word at a time — if the factory noise drowns out a word, it's gone. DSSS is like speaking each word by repeating every syllable 11 times at high speed — if the factory noise drowns out 3 or 4 syllables, you still have 7 or 8 copies that got through, and the listener can reconstruct the full word. Processing gain is the measure of how much extra noise the system can tolerate before the message is lost — the more copies of each piece of data you send, the more noise you can survive."

---

**Next:** Part 2 — PHY preamble analysis, DSSS vs. CCK performance comparison scenarios, coexistence troubleshooting, and PLCP/PMD diagnostic problems.
