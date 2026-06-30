# Chapter 2: RF Math — Part 2 (Applied + Practice)
**CWNA-109 Domain:** RF Technologies (15%)

---

## 2.12 Free Space Path Loss (FSPL)

- FSPL = signal power lost simply from spreading out over distance, in a vacuum/open space with NO obstacles
- This is the *theoretical minimum* loss — real-world loss is always worse (due to walls, reflection, absorption)
- Why it happens: power spreads over an ever-larger sphere as distance increases → same total energy, spread thinner

**Formula (simplified, in dB, commonly used form for Wi-Fi frequencies):**
```
FSPL (dB) = 20log₁₀(d) + 20log₁₀(f) + 32.44
```
- d = distance in km
- f = frequency in MHz

- CWNA typically tests the **conceptual doubling rule** more than this full formula:

**The Doubling Rule (memorize this — exam-critical):**
- Every time you **double the distance**, FSPL increases by **+6 dB**
- This holds true regardless of starting distance or frequency

**Worked Example — Doubling Rule in practice**
- At 10 meters: FSPL = X dB (some baseline)
- Step 1: Double distance to 20m → FSPL = X + 6 dB
- Step 2: Double again to 40m → FSPL = X + 12 dB
- Step 3: Double again to 80m → FSPL = X + 18 dB
- **Pattern: distance ×2 every time = FSPL +6dB every time, consistently**

**Why this matters practically:**
- Explains why moving an AP twice as far away doesn't cause a small/linear signal drop — it's a real, predictable +6dB loss every doubling
- Lets you estimate range problems without the full logarithmic formula in the field

---

## 2.13 The Fresnel Zone

- Fresnel Zone = an elliptical 3D zone around the direct line-of-sight path between transmitter and receiver
- Matters most for **outdoor point-to-point links** (bridge links, building-to-building)
- Obstacles inside this zone — even if NOT blocking direct line of sight — can still cause signal degradation via diffraction/reflection

**Key concept:**
- Line of sight (LOS) ≠ enough for a clean link
- You need **Fresnel zone clearance** — typically at least 60% of the first Fresnel zone must be obstruction-free

**Simplified formula for first Fresnel zone radius at the midpoint:**
```
r = 72.6 × √(d / 4f)
```
- r = radius in feet
- d = total distance in miles
- f = frequency in GHz

- CWNA-level understanding needed: **concept + when it applies**, not necessarily memorizing this exact formula under exam pressure (formula provided for completeness/deeper understanding)

**Practical takeaway:**
- A tree, building, or hill that doesn't block direct line of sight can still degrade a long-distance outdoor link if it intrudes into the Fresnel zone
- This is why outdoor bridge links need real clearance analysis, not just "can I see the other antenna"

---

## 2.14 Scenario: Diagnosing a Weak Long-Distance Link

**Setup:**
- Point-to-point bridge link between two buildings, 2 km apart
- Clear visual line of sight confirmed
- Link is unstable, frequent drops

**Wrong instinct:**
- "Line of sight is clear, so it must be a hardware/config issue"

**Correct reasoning:**
- Step 1: Clear LOS only guarantees the *direct path* is unobstructed
- Step 2: Check Fresnel zone clearance — is there a tree, rooftop structure, or terrain feature intruding into the elliptical zone around that direct path, even without blocking the direct line?
- Step 3: If yes → diffraction/reflection off that obstruction is interfering with the direct signal, even though "you can see the other antenna"
- Step 4: Real fix = raise antenna height to clear the Fresnel zone, not just the LOS path

**Conclusion to state out loud:**
> "Line of sight isn't the same as a clean RF path — the Fresnel zone is the real clearance requirement for outdoor links."

---

## 2.15 Harder Math: Combining EIRP + FSPL + SNR

This is the level of problem CWNA scenario questions actually combine — using multiple formulas from this chapter together.

**Problem:**
- AP transmit power: 23 dBm
- Cable loss: 3 dB
- Antenna gain: 6 dBi
- FSPL at the receiver's distance: 80 dB
- Noise floor at receiver: −92 dBm

**Find: received signal strength, and SNR.**

- Step 1: Calculate EIRP: 23 − 3 + 6 = **26 dBm EIRP**
- Step 2: Subtract FSPL to get received signal: 26 dBm − 80 dB = **−54 dBm received signal**
- Step 3: Calculate SNR: −54 − (−92) = −54 + 92 = **38 dB SNR**
- Step 4: Classify: 38 dB = Excellent (from Part 1's table: 25-40dB = Excellent)

**Why this matters:**
- This is the *actual sequence* a real RF engineer thinks through: transmitter setup → path loss → what arrives at the receiver → is it usable given the noise floor
- Each formula alone is incomplete; mastery = chaining them correctly in the right order

---

## 2.16 Trap Problem: Watch the Order of Operations

**Problem:**
- TX power: 20 dBm, cable loss: 4 dB, antenna gain: 10 dBi, FSPL: 75 dB
- A student calculates: 20 + 10 − 4 − 75 = −49 dBm. Is this right?

**Checking the work:**
- Step 1: EIRP = TX − cable loss + gain = 20 − 4 + 10 = 26 dBm (order doesn't matter here since it's all addition/subtraction — correct so far)
- Step 2: Received = EIRP − FSPL = 26 − 75 = **−49 dBm**
- **Result: the student's answer is actually correct** — addition/subtraction is commutative, so 20+10−4−75 produces the same result as doing it step by step
- **Lesson:** with EIRP/FSPL chains, order of the individual +/− terms doesn't matter mathematically, but doing it **step by step** (as shown in 2.15) prevents sign errors under exam pressure — recommended method, not because the math demands it, but because clean step-by-step work avoids careless mistakes

---

## 2.17 Teach-Back Drills

### Drill A — Non-technical stakeholder
**Q:** "Why does our office Wi-Fi feel fine but the warehouse Wi-Fi link to the other building keeps dropping, even though we can see the other building clearly?"

**Model answer:**
> "Being able to see the other building isn't the same as having a clean radio path. There's an invisible zone around that line of sight that also needs to be clear — if a tree or rooftop edge dips into that zone, it can scatter the signal even without blocking your view."

### Drill B — Junior technician
**Q:** "Why do we calculate EIRP instead of just looking at the AP's transmit power setting?"

**Model answer:**
> "Transmit power alone doesn't tell you the real radiated power — cable loss reduces it, and antenna gain increases it in a given direction. EIRP combines all three, and it's also the number regulators actually limit by law, so it's the number that matters for both performance and compliance."

### Drill C — Fellow CWNA candidate
**Q:** "Walk through, with numbers, why doubling distance costs exactly +6dB of path loss, not some other amount."

**Model answer:**
> "Power spreads outward over the surface of an expanding sphere as distance increases, and that surface area grows with the square of the distance. Path loss in dB is a logarithmic measure, and doubling a squared quantity corresponds to a fixed +6dB increase on the dB scale, every single time, regardless of starting distance — which is why it's a constant, predictable +6dB per doubling rather than a number that changes depending on how far you already are."

---

## 2.18 Self-Check Before Moving to Chapter 3

- [ ] Can convert mW to dBm using the Rule of 10s/3s without a calculator
- [ ] Can calculate EIRP given TX power, cable loss, and antenna gain
- [ ] Can calculate SNR given signal and noise floor, and classify quality
- [ ] Understand why FSPL increases by a fixed +6dB per distance doubling
- [ ] Can explain Fresnel zone clearance and why LOS alone isn't sufficient for outdoor links
- [ ] Can chain EIRP → FSPL → received signal → SNR in one combined problem

---

## 2.19 Final Practice Set

1. **(Recall)** How much does FSPL increase when distance triples? (Hint: triple = double + half again — think in terms of doublings, or note 3x ≈ 2x then another ~1.5x)
2. **(Applied)** TX power 18dBm, cable loss 2dB, antenna gain 12dBi, FSPL 70dB, noise floor −88dBm. Find received signal and SNR.
3. **(Teach)** Explain to a manager, in 2 sentences, why a clear line of sight isn't a guarantee of a stable outdoor wireless link.

### Answers
1. Tripling isn't a clean doubling, so the exact dB value requires the full logarithmic formula — but conceptually, going from 1x→2x adds 6dB, and the additional 2x→3x distance adds roughly another ~3.5dB, for a combined increase of approximately +9.5dB (CWNA generally tests the clean doubling rule; non-power-of-2 multiples like 3x are less commonly tested numerically)
2. Step 1: EIRP = 18−2+12 = 28dBm. Step 2: Received = 28−70 = −42dBm. Step 3: SNR = −42−(−88) = 46dB (Excellent)
3. "Line of sight only confirms the direct path between antennas is unobstructed — it doesn't account for the Fresnel zone, an invisible area around that path where nearby obstacles can still degrade the signal through diffraction."

---

**Day 2 complete.** Next: Chapter 3 — RF Behaviors (reflection, refraction, diffraction, scattering, absorption, VSWR, multipath in depth).
