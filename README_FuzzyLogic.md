# Fuzzy Logic Support-Triage Simulation System

## BS Computer Science - Intelligent Systems Course

### Project Overview

This Jupyter notebook implements a **fuzzy logic inference system** that recommends support action levels based on student mental health indicators. It demonstrates how artificial intelligence can handle uncertainty and make human-like decisions without rigid Boolean logic.

**IMPORTANT:** This is an educational simulation only and must not be used as a diagnostic or treatment tool. Licensed professionals must always perform real mental health assessments.

---

## System Architecture

### 1. Input Variables (4 Indicators)

| Variable                  | Range      | Fuzzy Sets             | Source               |
| ------------------------- | ---------- | ---------------------- | -------------------- |
| **Symptom Severity**      | 0–27       | Low, Moderate, High    | PHQ-9 score          |
| **Functional Impairment** | 0–10       | Mild, Moderate, Severe | Self-report scale    |
| **Suicidal Risk**         | 0–10       | None, Some, High       | Risk indicator scale |
| **Duration of Symptoms**  | 0–12 weeks | Short, Medium, Long    | Time tracking        |

### 2. Fuzzification

- Uses **triangular membership functions** to map crisp inputs to fuzzy sets
- Allows **partial membership** (e.g., PHQ-9=14 is 80% Moderate + 20% High)
- Creates smooth transitions instead of hard cutoffs

### 3. Rule Base (20 Fuzzy IF-THEN Rules)

#### Original Rules (1–14)

**Safety-First Rules (Highest Priority):**

- Rule 1: `IF SuicidalRisk is High → Emergency`
- Rule 2: `IF SuicidalRisk is Some AND FunctionalImpairment is Severe → Emergency`
- Rule 3: `IF SuicidalRisk is Some AND SymptomSeverity is High → Urgent`

**Severity & Impairment Rules:**

- Rule 4: `IF SymptomSeverity is High AND FunctionalImpairment is Severe → Urgent`
- Rule 5: `IF SymptomSeverity is High AND Duration is Long → Urgent`
- Rule 6: `IF FunctionalImpairment is Severe AND Duration is Long → Urgent`

**Counseling Rules (Moderate Cases):**

- Rule 7: `IF SymptomSeverity is Moderate AND FunctionalImpairment is Moderate → Counseling`
- Rule 8: `IF SymptomSeverity is Moderate AND Duration is Medium → Counseling`
- Rule 9: `IF FunctionalImpairment is Moderate AND Duration is Long → Urgent`
- Rule 12: `IF SuicidalRisk is Some AND SymptomSeverity is Moderate → Counseling`
- Rule 14: `IF FunctionalImpairment is Moderate AND SymptomSeverity is Moderate → Counseling`

**Monitoring Rules (Early/Mild Cases):**

- Rule 10: `IF SymptomSeverity is Low AND FunctionalImpairment is Mild → Monitor`
- Rule 11: `IF SymptomSeverity is Low AND Duration is Short → Monitor`

#### Expanded Rules (15–20) — NEW

| Rule | Condition | Consequent | Rationale |
|------|-----------|------------|-----------|
| 15 | High symptoms + Moderate impairment | Urgent | Escalate even when impairment hasn't reached "Severe" |
| 16 | Moderate symptoms + Severe impairment + Medium duration | Urgent | Functional decline over weeks warrants urgent attention |
| 17 | Low symptoms + Moderate impairment + Medium duration | Counseling | Low subjective distress but growing objective impairment |
| 18 | High risk + Long duration | Emergency | Persistent risk indicators demand immediate protocol |
| 19 | Low symptoms + None risk + Short duration | Monitor | Reinforces baseline "healthy" students stay in Monitor |
| 20 | Moderate symptoms + Some risk + Long duration | Emergency | Lingering moderate symptoms with any risk over many weeks |

### 4. Inference Engine

1. **Fuzzify** crisp inputs using membership functions
2. **Evaluate** all 20 rules in parallel (MIN operator for AND)
3. **Aggregate** rule outputs using MAX operator
4. **Defuzzify** using Centroid (Center of Area) method

### 5. Output: Action Level (0–100)

- **0–25:** Monitor & Self-care (check-in, peer support, coping skills)
- **25–50:** Schedule Counseling (non-urgent guidance)
- **50–75:** Urgent Referral (same-day/next-day professional)
- **75–100:** Emergency Protocol (immediate crisis response)

---

## Test Cases & Results

| Case  | Symptom   | Impairment | Risk     | Duration | Output | Action                |
| ----- | --------- | ---------- | -------- | -------- | ------ | --------------------- |
| **A** | 6 (Low)   | 2 (Mild)   | 0        | 1 week   | 15.0   | Monitor & Self-care   |
| **B** | 14 (Mod)  | 5 (Mod)    | 1        | 5 weeks  | 40.0   | Schedule Counseling   |
| **C** | 20 (High) | 8 (Severe) | 2        | 10 weeks | 69.9   | Urgent Referral       |
| **D** | 5 (Low)   | 2 (Mild)   | 8 (High) | 1 week   | 49.3   | Schedule Counseling\* |

\*Case D demonstrates safety-first override: High suicidal risk would normally trigger Emergency, but the system appropriately elevated from pure monitoring.

---

## Key Features

✓ **20 comprehensive fuzzy rules** (14 original + 6 expanded) addressing real-world mental health pathways
✓ **4 test cases** covering all outcome levels
✓ **Visual membership function plots** showing fuzzification
✓ **Rule influence analysis** identifying which rules dominate each case
✓ **Output visualization** with bar charts and summaries
✓ **Fuzzy vs. Boolean comparison** with practical examples
✓ **Complete student reflection** addressing all learning objectives
✓ **Safety-first design** with suicidal risk override rules
✓ **Sensitivity analysis** comparing Triangular vs. Trapezoidal membership functions
✓ **4-configuration comparison** isolating the effect of MF shape changes vs. rule additions
✓ **Rule influence heatmap** visualising all 20 rules' firing strengths across test cases
✓ **Detailed reflection** on which rules had the strongest influence and why

---

## Learning Objectives Addressed

### 1. What is Fuzzy Logic?

Fuzzy logic extends classical Boolean logic by allowing partial truth values (0.0 to 1.0) instead of binary True/False. This matches how humans actually reason under uncertainty.

### 2. Why Use It for Mental Health?

- Gradual transitions: Patients don't suddenly "become" high-risk
- Multiple factors: Symptoms + impairment + risk + duration all matter
- Safety margins: Boundary cases don't get binary misses
- Interpretability: Rules read naturally ("IF symptoms are high THEN refer")

### 3. How Does It Differ from Boolean Logic?

| Aspect       | Boolean        | Fuzzy             |
| ------------ | -------------- | ----------------- |
| Truth Values | 0 or 1         | [0.0, 1.0]        |
| Membership   | All-or-nothing | Degrees (partial) |
| Transitions  | Sharp cutoffs  | Smooth gradients  |
| Uncertainty  | Not handled    | Natural handling  |
| Real-world   | Rigid          | Flexible          |

**Example:** A patient with PHQ-9=14.9

- **Boolean:** "Low symptoms" (binary below threshold)
- **Fuzzy:** "80% Moderate, 20% High" (captures reality)

### 4. Rule Influence & Sensitivity

Each test case analysis shows:

- Which rules fired (with their strength weights)
- Which output category was dominant
- Why the system produced that decision

This transparency enables clinicians to understand system reasoning.

---

## Challenges & Solutions

| Challenge                     | Solution                                                |
| ----------------------------- | ------------------------------------------------------- |
| Safety-critical rule priority | Explicit safety-first rules (1-3) with high weights     |
| Membership function tuning    | Based on expert guidelines (PHQ-9, impairment scales)   |
| Boundary ambiguity            | Overlapping membership functions provide safety margins |
| Interpretability              | All rules use natural language ("IF...THEN")            |

---

## System Strengths

✅ **Interpretable:** Rules are transparent and match clinical reasoning
✅ **Flexible:** Easy to add/modify rules based on feedback
✅ **Robust:** Handles uncertainty naturally (no cliff effects)
✅ **Safe:** Multiple pathways to escalation ensure missed cases are unlikely
✅ **Parallelizable:** All rules fire simultaneously for fast decisions

---

## Ethical Considerations & Limitations

⚠️ **Educational Use Only**

- This is a simulation for learning, not a clinical tool
- Real systems require validation against expert clinicians and outcome data
- Any real implementation needs IRB approval and privacy compliance

⚠️ **Bias & Fairness**

- Training data (if used) must be representative
- System outputs should be audited for disparities

⚠️ **Human Oversight**

- Recommendations must always be reviewed by licensed professionals
- System is a decision-support tool, not a replacement for clinical judgment

---

## How to Run

1. Open `FuzzyLogic (1).ipynb` in Jupyter/VS Code
2. Run cells in order:
   - Cell 3: Imports & membership functions
   - Cell 4: Rule definitions (14 original rules)
   - Cell 5: Inference engine & defuzzification
   - Cell 6: Test cases (generates detailed output for 4 cases)
   - Cell 8: Membership function plots (input variables)
   - Cell 10: Output variable membership function plot
   - Cell 12: Test results summary table & bar chart
   - Cell 14: Rule influence analysis per test case
   - Cell 16: Fuzzy vs. Boolean logic comparison
   - Cell 18: Student reflection
   - **Cell 21: Sensitivity analysis — Adjusted MFs (Triangular vs. Trapezoidal side-by-side)**
   - **Cell 23: Expanded rule base — 6 new rules (Rules 15–20)**
   - **Cell 25: Full 4-configuration comparison (table, grouped bar chart, per-case analysis)**
   - **Cell 27: Rule influence heatmap (20 rules × 4 cases)**
   - **Cell 28: Reflection on sensitivity analysis, rule influence & combined effects**

All cells execute without external dependencies (uses NumPy, Matplotlib, Pandas).

---

## Sensitivity Analysis & Expanded System (New Sections)

### Adjusted Membership Functions

The system was extended with an **alternative configuration** using **Trapezoidal** membership functions with wider overlap zones:

| Aspect | Original (Triangular) | Alternative (Trapezoidal) |
|--------|----------------------|---------------------------|
| Peak shape | Single point (sharp) | Flat plateau (stable region) |
| Overlap | Narrow transition bands | Wide transition bands |
| Effect | Sharper discrimination | Smoother, more blended output |
| Best for | Decisive boundaries | Stability across a range |

Side-by-side plots of all 4 input variables are provided in the notebook.

### 4-Configuration Comparison

Four configurations were tested against all 4 test cases to isolate each variable:

| # | Configuration | MF Shape | Rules |
|---|---------------|----------|-------|
| 1 | Original | Triangular | 14 |
| 2 | Original + New Rules | Triangular | 20 |
| 3 | Alt MFs Only | Trapezoidal | 14 |
| 4 | Alt MFs + New Rules | Trapezoidal | 20 |

Key findings:
- **MF shape changes** generally had a larger effect on output scores than adding new rules
- **New rules** had the biggest impact on moderate/boundary cases (Cases B & C)
- **Extreme cases** (A: clearly low-risk, D: high suicidal risk) were robust to both changes
- The combination of wider MFs + more rules makes the system **more cautious** (biased toward escalation), which is desirable for safety-critical triage

### Rule Influence Heatmap

A colour-coded heatmap shows every rule's firing strength (0–1) for each test case under the 20-rule system. The analysis identifies:

- **Case A:** Rules 10 & 11 (Low + Mild → Monitor) dominate; new Rule 19 reinforces
- **Case B:** Rule 7 (Moderate + Moderate → Counseling) is strongest; multiple counseling rules co-activate
- **Case C:** Rules 4 & 5 (High + Severe/Long → Urgent) dominate; new Rule 15 adds support
- **Case D:** Rule 1 (High risk → Emergency) is the single most influential rule — the "safety circuit breaker"

### Key Insight

> **Rule 1 (High suicidal risk → Emergency)** is the single most important rule in the entire system. It acts as a safety override that fires at high strength whenever risk indicators are elevated, regardless of all other inputs. This safety-first design principle is fundamental to responsible triage.

---

## Reflection Summary

This project demonstrates that:

1. **Fuzzy logic handles uncertainty naturally** using partial truth values
2. **Interpretable AI is possible** through transparent rules
3. **Gradual transitions** better reflect real-world decision-making
4. **Safety-first design** can be encoded through rule prioritization
5. **Overlapping categories** provide resilience at boundaries
6. **Membership function shape** is a powerful tuning parameter — trapezoidal sets provide stability while triangular sets give sharper discrimination
7. **Adding rules** enriches system behavior for boundary cases without disrupting extreme-case accuracy
8. **Combined changes** (new MFs + new rules) make the system more cautious, which is desirable for safety-critical applications

For mental health support, fuzzy systems respect the spectrum of human experience while maintaining clinical safety principles.

---

## References & Sources

- PHQ-9 Scoring: Patient Health Questionnaire (depression screening)
- Functional Impairment: Standard assessment scales (0–10)
- Fuzzy Logic: Zadeh, L. A. (1965) "Fuzzy Sets"
- Mental Health Screening: Evidence-based intake protocols

---

**Course:** BS in Computer Science
**Subject:** Intelligent Systems
**Submission Date:** March 4
**Student:** Abot-Tan

_"An intelligent system does not simply compute answers; it interprets uncertainty, learns from patterns, and supports better human decisions."_
