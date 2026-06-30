# Chapter 3: RF Behaviors — Part 1 (Core Concepts)
**CWNA-109 Domain:** RF Technologies (15%)

---

## 3.1 Why RF Behaviors Matter

- Chapter 1 = what a wave IS. Chapter 2 = how to MEASURE it. Chapter 3 = how it ACTS when it hits something
- Every RF signal in a real building interacts with walls, metal, glass, people, furniture
- Knowing these behaviors = the difference between guessing at coverage problems and diagnosing them precisely

**The 6 core RF behaviors:**

| Behavior | One-line definition |
|---|---|
| Reflection | Wave bounces off a surface |
| Refraction | Wave bends passing through a different density medium |
| Diffraction | Wave bends around an obstacle's edge |
| Scattering | Wave breaks into multiple weaker waves off an irregular surface |
| Absorption | Wave's energy is absorbed/converted to heat by a material |
| Attenuation | General weakening of signal (umbrella term covering loss from any cause) |

---

## 3.2 Reflection

- Occurs when an RF wave hits a smooth surface **larger than its wavelength** and bounces back
- Common reflective surfaces: metal, glass, water, polished concrete, whiteboards
- Angle of incidence = angle of reflection (same rule as light bouncing off a mirror)

**Direct consequence: Multipath (from Chapter 1)**
- Reflected copies of a signal arrive at the receiver later than the direct copy
- Causes constructive or destructive interference depending on phase alignment (recall Ch.1 — path difference vs. wavelength)

**Practical impact:**
- Metal warehouse shelving, elevator shafts, glass office partitions = major reflection sources
- Heavy reflection environments = more multipath nulls, less predictable coverage

---

## 3.3 Refraction

- Occurs when a wave passes from one medium into another medium of **different density**, causing it to bend
- Classic example: light bending when entering water — same principle applies to RF
- In Wi-Fi: less common as a major issue indoors, but relevant with:
  - Atmospheric layers (outdoor long-distance links)
  - Temperature/humidity gradients bending signal paths slightly over long distances

**Practical impact:**
- Mostly relevant for **outdoor, long-distance point-to-point links** (bridge links between buildings)
- Atmospheric refraction can cause a link that worked fine yesterday to degrade today due to weather changes

---

## 3.4 Diffraction

- Occurs when a wave encounters an obstacle's **edge** and bends around it, creating partial coverage in what should be a "shadow" zone
- Lower frequency = longer wavelength = better diffraction (direct callback to Chapter 1)
- This is *why* 2.4 GHz "wraps around corners" better than 5/6 GHz

**Practical impact:**
- Explains why you sometimes get partial/weak signal just around a corner from an AP, instead of zero signal
- Diffraction is why complete physical blocking (e.g., a corner) doesn't always equal complete signal loss — there's a gradient, not a hard cutoff

---

## 3.5 Scattering

- Occurs when a wave hits a surface that is **rough or irregular relative to its wavelength** (not smooth like reflection)
- The wave doesn't bounce as one coherent reflection — it breaks into many smaller, weaker waves radiating in multiple directions
- Common scattering surfaces: foliage, chain-link fences, rough drywall texture, rain (outdoor)

**Reflection vs. Scattering — the key distinguishing factor:**

| Factor | Reflection | Scattering |
|---|---|---|
| Surface | Smooth, larger than λ | Rough/irregular relative to λ |
| Result | One coherent bounced wave | Many weak waves in multiple directions |
| Predictability | High — angle in = angle out | Low — energy spreads unpredictably |

**Practical impact:**
- Scattering generally weakens signal more unpredictably than clean reflection
- Outdoor links: rain and foliage cause scattering — a known cause of intermittent outdoor link degradation

---

## 3.6 Absorption

- Occurs when an RF wave's energy is absorbed by a material and converted into heat (rather than reflected, refracted, or passed through)
- The water content of a material is a huge factor — water absorbs RF energy strongly
- Common absorptive materials: water, human bodies (mostly water), wood, drywall (some), brick

**Practical impact:**
- Explains "Wi-Fi gets worse when the conference room fills up with people" — human bodies absorb signal
- Explains why aquariums, water heaters, and even humid air can measurably degrade signal
- This is also the operating principle behind microwave ovens — and part of why they cause interference at 2.4 GHz specifically (2.4 GHz is close to a frequency water molecules absorb efficiently)

---

## 3.7 Attenuation — The Umbrella Term

- Attenuation = **any** weakening of signal strength, regardless of cause
- Reflection, refraction, diffraction, scattering, absorption, and Free Space Path Loss (Ch.2) are all specific *causes* of attenuation
- On the exam: if a question asks generally "why did the signal get weaker," attenuation is the general category; the specific behavior (absorption, scattering, etc.) is the precise mechanism

---

## 3.8 VSWR — Voltage Standing Wave Ratio

- VSWR measures **impedance mismatch** between RF components (e.g., AP, cable, antenna)
- When impedance isn't matched, some signal energy reflects backward into the system instead of radiating out — wasted power, potential equipment damage at high power levels
- Expressed as a ratio: e.g., 1.5:1, 2:1

**Key reference points:**

| VSWR | Meaning |
|---|---|
| 1:1 | Perfect match — no reflected power (theoretical ideal) |
| 1.5:1 | Good — minimal reflection, normal in real systems |
| 2:1 | Acceptable but notable loss |
| 3:1+ | Poor — significant reflected power, investigate connections/cabling |

**Practical impact:**
- A damaged cable, bad connector, or mismatched antenna causes high VSWR
- High VSWR = wasted transmitter power + potential equipment damage = real troubleshooting target when an AP "should" have good coverage but doesn't

---

## 3.9 Common Misconceptions

| Misconception | Reality |
|---|---|
| "Reflection and scattering are the same thing" | Reflection = smooth surface, one coherent bounce. Scattering = rough surface, energy spreads into many weak waves |
| "Diffraction means the signal goes straight through the obstacle" | No — diffraction is bending AROUND an edge, not passing through. Passing through with loss is absorption/penetration |
| "VSWR is about signal strength dropping over distance" | No — VSWR is about impedance mismatch in the hardware chain (cables/connectors/antenna), unrelated to distance |
| "Absorption only matters outdoors (rain, etc.)" | Absorption happens indoors constantly — human bodies, water, building materials all absorb RF energy |

---

## 3.10 Chapter Summary (Say This Cold)

- RF waves interact with the environment in 6 core ways: reflection (bounce), refraction (bend through density change), diffraction (bend around edges), scattering (break apart on rough surfaces), absorption (energy converted to heat), and attenuation (general umbrella term for all signal weakening)
- Lower frequency = longer wavelength = better diffraction — same physics as Chapter 1's wavelength logic
- Reflection causes multipath (Ch.1); scattering causes unpredictable weak energy spread; absorption is driven heavily by water content (bodies, humidity, materials)
- VSWR is a separate concept — impedance mismatch in hardware (cables/connectors), not environmental signal loss

---

## 3.11 Practice Problems

**Tier 1 — Recall**
1. Name the 6 core RF behaviors.
2. What's the key surface difference between reflection and scattering?
3. What does VSWR measure?

**Tier 2 — Applied**
4. A conference room's Wi-Fi noticeably weakens once it fills with 20 people, despite no equipment changes. Which behavior explains this, and why?
5. An AP shows much lower throughput than expected, but signal strength readings look fine. Cabling was recently replaced. What should you check, and why?
6. Why does 2.4 GHz "wrap around" office corners better than 5 GHz? Name the specific behavior and connect it to Chapter 1's wavelength math.

**Tier 3 — Teach-back**
7. Explain to a non-technical coworker why a microwave oven can disrupt Wi-Fi, using the word "absorption" correctly.
8. Explain the difference between reflection and scattering to a junior technician using a real-world surface example for each.

---

## 3.12 Answer Key

1. Reflection, refraction, diffraction, scattering, absorption, attenuation
2. Reflection = smooth surface, larger than wavelength, coherent bounce. Scattering = rough/irregular surface relative to wavelength, energy spreads into multiple weak waves
3. Impedance mismatch between RF hardware components (cable, connectors, antenna) — how much signal reflects backward instead of radiating out
4. Absorption — human bodies are mostly water, and water absorbs RF energy strongly, so a room full of people measurably weakens the signal compared to an empty room
5. Check VSWR — newly replaced cabling could have a bad connector or impedance mismatch, causing reflected power loss in the hardware chain even though the AP itself reports normal transmit settings
6. Diffraction — 2.4 GHz's longer wavelength (12.5cm vs 5GHz's 6cm, from Ch.1) diffracts/bends around obstacle edges more effectively than the shorter 5GHz wavelength
7. "A microwave oven works by using RF energy at a frequency that water molecules absorb very efficiently — that's literally how it heats food. Since it operates close to the 2.4 GHz Wi-Fi band, some of that energy leaks out and gets absorbed/interferes with your Wi-Fi signal too."
8. "Reflection is like a signal bouncing off a smooth mirror — picture it bouncing cleanly off a whiteboard or metal cabinet, going one predictable direction. Scattering is like a signal hitting something rough and bumpy, like a chain-link fence or thick foliage — instead of one clean bounce, it breaks into lots of weaker waves going many directions."

---

**Next:** Part 2 — Multipath deep dive, VSWR troubleshooting scenarios, and combined RF behavior diagnostic problems.
