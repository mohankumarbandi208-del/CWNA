# Chapter 1: RF Fundamentals — Part 2 (Applied + Practice)
**CWNA-109 Domain:** RF Technologies (15%)

---

## 2.1 Scenario: Conference Room Dead Spot

**Setup:**
- Single AP, ceiling-mounted, center of room, 5 GHz
- Clear line of sight, no obstructions
- One specific corner = dead. Move 3 feet = fine.

**Wrong instinct:**
- "Must be interference from another network"
- Doesn't fit evidence — interference from a competing network usually causes *broad* degradation, not a pinpoint dead spot surrounded by good signal

**Correct diagnostic reasoning (step by step):**
- Step 1: Direct signal reaches the corner fine (no obstruction)
- Step 2: Reflected copies (off table, whiteboard, window) also reach that corner, having traveled a longer path
- Step 3: Reflected copy ≠ same distance as direct copy → arrives out of phase
- Step 4: If path difference ≈ half-integer multiple of λ → destructive interference → null
- Step 5: 5 GHz wavelength = 6 cm (small) → null zones are physically tiny and sensitive to small position changes → explains why moving a few inches fixes it

**Conclusion to state out loud:**
> "This is a multipath null, not coverage or external interference — direct and reflected signals are canceling at that exact point."

---

## 2.2 Harder Math

### Problem 1 — Prove the inverse relationship algebraically

**Goal:** Prove doubling frequency always halves wavelength (general proof, not just an example).

- Step 1: Start with λ₁ = c / f₁
- Step 2: Let f₂ = 2f₁ (frequency doubled)
- Step 3: λ₂ = c / f₂ = c / (2f₁) = (c/f₁) × (1/2)
- Step 4: Since c/f₁ = λ₁, substitute: λ₂ = λ₁ / 2
- **Result: λ₂ = λ₁ / 2 — proven for ANY frequency, not just specific numbers**

---

### Problem 2 — Path difference = 6.25 cm at 2.4 GHz

- Step 1: λ at 2.4 GHz = 12.5 cm (from Part 1)
- Step 2: Ratio = path difference / λ = 6.25 / 12.5 = **0.5**
- Step 3: 0.5 = half-integer multiple → **destructive interference**
- Step 4: Real-world result: signal null at that point

---

### Problem 3 — Trap question: Path difference = 25 cm at 2.4 GHz

- Step 1: λ = 12.5 cm
- Step 2: Ratio = 25 / 12.5 = **2** (whole number)
- Step 3: Whole number → **constructive interference**, NOT destructive
- **Trap being tested:** "longer path difference" ≠ "more cancellation." It's the ratio to λ that matters, not raw distance. 25 cm sounds bigger than 6.25 cm, but produces the opposite result.

---

## 2.3 Teach-Back Drills (Do This Out Loud)

### Drill A — Non-technical office manager
**Q:** "Why does 5 GHz Wi-Fi have more weak spots near the back wall than our old router?"

**Model answer:**
> "It's a tradeoff. 2.4 GHz is like a low bass sound that bends around corners and through walls easily. 5 GHz is like a higher-pitched sound — faster, less crowded, but it doesn't bend around obstacles as well, so far corners can feel weaker."

---

### Drill B — Junior IT technician
**Q:** "What is multipath and why does it cause dead spots?"

**Model answer:**
> "The AP's signal bounces off walls and furniture, so multiple copies reach your laptop after traveling different distances. Because they traveled different distances, they can arrive out of sync — sometimes that makes the signal stronger, sometimes it cancels out almost completely. That cancellation is a dead spot."

---

### Drill C — Fellow CWNA candidate (full technical depth)
**Q:** "Prove mathematically why a half-wavelength path difference causes destructive interference."

**Model answer:**
> "A full wavelength = one 360° cycle. A half-wavelength difference = 180° phase shift. At 180°, a wave's value is the exact negative of the original wave's value at every instant. Equal amplitude + opposite sign at every point in the cycle = sum is zero across the entire waveform, not just momentarily. That's destructive interference, derived — not just labeled."

---

## 2.4 Self-Check Before Moving to Chapter 2

- [ ] Can derive λ = c/f and explain it in your own words
- [ ] Can calculate wavelength for any frequency in under 60 seconds
- [ ] Can explain why 2.4 GHz penetrates better than 5/6 GHz using wavelength (not "it just does")
- [ ] Can diagnose a clear-line-of-sight localized dead spot as multipath, not generic interference
- [ ] Can correctly classify any path-difference problem as constructive/destructive — including catching the "bigger number ≠ more cancellation" trap
- [ ] Have said Drill A, B, and C out loud at least once

---

## 2.5 Final Practice Set

1. **(Recall)** What does a 2.5λ path difference produce?
2. **(Applied)** Path difference = 9 cm at 5 GHz (λ=6cm). Constructive or destructive?
3. **(Teach)** Explain in 2 sentences, no jargon, why your phone loses signal in the bathroom but not the bedroom.

### Answers
1. Destructive (half-integer multiple)
2. Ratio = 9/6 = 1.5 → destructive
3. "Walls and pipes reflect and absorb signals differently depending on material — bathrooms often have more metal and tile, causing more cancellation between the direct and bounced signal than drywall does."

---

**Day 1 complete.** Next: Chapter 2 — RF Math (dBm, dBi, EIRP, SNR, Free Space Path Loss, Fresnel Zone).
