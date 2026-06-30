# Chapter 1: RF Fundamentals — Part 1 (Core Concepts)
**CWNA-109 Domain:** RF Technologies (15%)

---

## 1.1 What Is RF?

- RF (Radio Frequency) = a slice of the electromagnetic spectrum, ~3 kHz to 300 GHz
- Wi-Fi uses 3 slices of this: **2.4 GHz**, **5 GHz**, **6 GHz**
- An AP doesn't send data through a "tube" like a cable does — it **radiates** electromagnetic energy through open air via an antenna
- Analogy: wired = water through a pipe; wireless = ripples spreading on a pond, in 3D, bouncing off everything

**Core mental model:**
- Wired = controlled, shielded, predictable medium
- Wireless = uncontrolled, unshielded, shared medium
- → WLAN admin's entire job = **predicting and shaping** something you can't fully control

---

## 1.2 The Sine Wave — 4 Properties

Every RF signal is a sine wave, fully described by 4 properties:

| Property | What it measures | Unit |
|---|---|---|
| Frequency | cycles per second | Hz |
| Wavelength | physical distance per cycle | meters |
| Amplitude | signal strength/power | dBm (Ch. 2) |
| Phase | position within the cycle | degrees (0-360°) |

```
Amplitude
   ^
   |    ___          ___
   |   /   \        /   \
   |--/-----\------/-----\---> Time
   | /       \    /       \
   |/         \__/         \__
   |
   <--- one cycle = 360° --->
```

---

### 1.2.1 Frequency

- Number of complete cycles per second
- Unit: Hz → 1 Hz = 1 cycle/sec
- 2.4 GHz = 2,400,000,000 cycles/sec
- Analogy: shaking a jump rope fast = high frequency; slow = low frequency
- **Key point:** frequency alone does NOT determine speed/throughput — that's channel width + modulation (later chapters)

---

### 1.2.2 Wavelength

- Physical distance one complete cycle takes up in space
- **Inverse relationship with frequency:** higher frequency → shorter wavelength

**Formula:**
```
λ = c / f
```
- λ = wavelength (meters)
- c = speed of light = 3 × 10⁸ m/s
- f = frequency (Hz)

**Worked Example 1 — 2.4 GHz:**
- Step 1: Convert GHz → Hz: 2.4 GHz = 2.4 × 10⁹ Hz
- Step 2: Plug in: λ = (3×10⁸) / (2.4×10⁹)
- Step 3: Divide: 3 ÷ 2.4 = 1.25, and 10⁸÷10⁹ = 10⁻¹
- Step 4: λ = 1.25 × 10⁻¹ m = 0.125 m = **12.5 cm**

**Worked Example 2 — 5 GHz:**
- Step 1: 5 GHz = 5 × 10⁹ Hz
- Step 2: λ = (3×10⁸) / (5×10⁹) = 0.06 m = **6 cm**

**Worked Example 3 — 6 GHz:**
- Step 1: 6 GHz = 6 × 10⁹ Hz
- Step 2: λ = (3×10⁸) / (6×10⁹) = 0.05 m = **5 cm**

**Quick reference table:**

| Band | Wavelength | Size comparison |
|---|---|---|
| 2.4 GHz | 12.5 cm | width of your hand |
| 5 GHz | 6 cm | width of two fingers |
| 6 GHz | 5 cm | width of a large coin |

**Why it matters:**
- Longer wavelength = better diffraction (bending around corners) + better penetration through walls
- This is the *actual physical reason* 2.4 GHz has better range/penetration than 5/6 GHz
- Not folklore — it's directly derivable from λ = c/f

---

### 1.2.3 Amplitude

- Height of the wave = signal strength/power
- Measured in **dBm** (full math in Chapter 2)
- Frequency and amplitude are **independent** — knowing one tells you nothing about the other
- Higher amplitude → signal travels farther / through more obstacles before becoming unusable

---

### 1.2.4 Phase

- Position of a wave within its cycle (0°–360°)
- Only meaningful when **comparing 2+ waves**
- Analogy: two kids on swings, same speed/height, but offset in timing = phase difference

**Why it matters — Multipath:**
- AP signal travels to receiver via multiple paths: direct + reflections (walls, furniture, ceiling)
- Reflected copies travel longer distances → arrive **out of phase** with the direct signal

**Interference rules:**

| Path difference | Phase result | Outcome |
|---|---|---|
| 0λ, 1λ, 2λ, 3λ... (whole numbers) | In phase (0°) | **Constructive** — signal strengthens |
| 0.5λ, 1.5λ, 2.5λ... (half-integers) | Out of phase (180°) | **Destructive** — signal cancels (a "null") |

- A **null** = real-world Wi-Fi dead spot in clear line of sight
- Explains why moving a few inches can fix a dead spot — you've moved out of the cancellation zone

---

## 1.3 The Electromagnetic Spectrum — Where Wi-Fi Lives

| Band | Frequency Range | 802.11 Standards | Classification |
|---|---|---|---|
| 2.4 GHz | 2.400–2.4835 GHz | b, g, n | ISM |
| 5 GHz | 5.150–5.895 GHz (region-dependent) | a, n, ac, ax | UNII |
| 6 GHz | 5.925–7.125 GHz | ax (6E), be (Wi-Fi 7) | UNII |

- **ISM** = Industrial, Scientific, Medical band — historically unlicensed
- Unlicensed = no government license needed to transmit, BUT shared with microwaves, Bluetooth, baby monitors, etc.
- → root cause of 2.4 GHz being noisier/more congested than 5/6 GHz
- Full regulatory detail comes in Chapter 5

---

## 1.4 Wired vs. Wireless — The Core Shift

| | Wired (Ethernet) | Wireless (RF) |
|---|---|---|
| Path | Guided, shielded, fixed | Radiated, open, 3D |
| Predictability | High — engineered medium | Low — shared, uncontrolled environment |
| Admin's job | Mostly fixed once installed | Constant prediction + shaping (AP placement, power, antenna choice) |

- You can't reshape walls or stop a neighbor's Wi-Fi
- Every later topic (antennas, channel planning, site surveys, troubleshooting) = learning to predict/shape RF, since you can never fully control it

---

## 1.5 Common Misconceptions

| Misconception | Reality |
|---|---|
| "5 GHz is just faster than 2.4 GHz" | Frequency ≠ speed. 5 GHz performs better due to more channels/width + less congestion, not the frequency number itself |
| "Phase and frequency are the same" | Frequency = how fast it cycles. Phase = where it is *right now* in that cycle |
| "Longer reflected path = more cancellation" | Only matters relative to wavelength. 1λ, 2λ, 3λ diffs = constructive, NOT destructive. Only half-integer multiples cancel |

---

## 1.6 Chapter Summary (Say This Cold)

- RF = electromagnetic sine wave, radiated (not guided) through open space
- 4 properties: frequency (Hz), wavelength (λ=c/f, inverse to frequency), amplitude (dBm), phase (degrees)
- Wi-Fi bands: 2.4/5/6 GHz — lower frequency = longer wavelength = better penetration/range
- Multipath + phase → constructive (whole λ) or destructive (half λ) interference → explains real dead spots
- Wireless = uncontrolled shared medium → WLAN admin predicts/shapes it, never fully controls it

---

## 1.7 Practice Problems

**Tier 1 — Recall**
1. State the formula for wavelength.
2. Name the 4 sine wave properties.
3. What unit is frequency measured in?

**Tier 2 — Applied**
4. Calculate wavelength for 5.8 GHz.
5. A path difference is 1.5λ. Constructive or destructive?
6. Why does 2.4 GHz penetrate walls better than 5 GHz? (Use wavelength reasoning, not "it just does")

**Tier 3 — Teach-back**
7. In 2 sentences, correct a coworker who says "we should use 5 GHz everywhere because it's faster."
8. Explain why moving a laptop a few inches fixes a dead spot, to someone non-technical, without the word "phase."

---

## 1.8 Answer Key

1. λ = c / f
2. Frequency, wavelength, amplitude, phase
3. Hertz (Hz)
4. λ = (3×10⁸)/(5.8×10⁹) = 0.0517 m ≈ **5.17 cm**
5. 1.5λ = half-integer multiple → **Destructive**
6. Lower frequency → longer wavelength → better diffraction around obstacles + better penetration through materials than the shorter 5 GHz wavelength
7. "5 GHz isn't faster because of its frequency — it performs better because it has more channels and less congestion than 2.4 GHz. Frequency itself doesn't determine speed."
8. "Two copies of the same signal bounce around the room and meet at your laptop — sometimes they cancel each other out like two waves in a pool meeting and flattening out. Moving a few inches changes that meeting point, so they stop canceling."

---

**Next:** Part 2 — worked scenarios, harder math, teach-back drills.
