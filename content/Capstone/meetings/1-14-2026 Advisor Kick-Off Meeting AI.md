# Advisor Kick Off – Meeting Notes (2026-01-14)

**Attendees:** Agishan, Callum, Graydon, Curtis, Liam, Dr. Mahmoudzadeh, Dr. Dimitrov

---

## Overview

We want to build a dashboard that:
- Shows how pick-lists differ across surgeons
- Groups items that appear to serve the same purpose
- Surfaces candidate interchangeable items
- Helps CMH decide where to consolidate
- Supports decision making, but does **not** optimize picklists or make clinical decisions

---

## Approach

### 1) LLMs for Item Categorization (Semantic Similarity)
- Goal: identify products that likely serve a similar purpose
- Limitation: does not reliably say “item A can replace item B”
- Value: narrows down the number of items to consider for substitution

### 2) Frequency / Usage Patterns
- Goal: more granular and defensible substitution suggestions
- Example signal:
  - Two items are used by different surgeons
  - They do not appear together (suggests possible overlap in purpose)

---

## Key Takeaways and Action Items

### LLM Labelling and Defensibility
- Continue with LLM labelling, and it is okay for LLMs to do a first pass
- Move away from ad-hoc methods and make the process defendable
- Look for support in the literature
- Use “Stan LLM”
- Experiment with Gemini 2.5 reasoning parameters
- Do not ask the model for a “score”
  - Instead, ask for Python code to compute confidence if that approach is used

### Validation Workflow With CMH
- Keep constant validation in the loop
- Do one more round of label validation
- Avoid repeatedly sending Excel sheets
- Build an interactive way for CMH to validate and edit labels
  - Iteratively surfaces the next “problematic” item for review
  - Ask CMH about their labeling process so we can mimic it
  - Consider head-to-head comparisons of potential labels
- Goal: avoid continuously asking CMH for label validation

### Product Direction and Outcomes
- Outcome should support new picklists over time
- Automate as much of the LLM validation process as possible
  - This should be part of the tool, not a separate pipeline
- The tool is decision support, not decision making
- The dashboard should distill relevant info to help them decide
- CMH should be able to:
  - Modify categories and labels
  - See how results change
- Relax the constraint that “all labelled items need to be substitutes”
- Define the level of error CMH considers acceptable for “potential substitutes”
- If labels are broader, incorporate CMH feedback over time
  - Present grouped item info and let CMH decide substitutions
  - Two-step approach:
    1) Initial labelling algorithm
    2) Algorithm that learns from CMH input
- Do not get stuck chasing perfect labels
  - Consider an online learning approach

### Data Requirements
- Determine what CMH input data will look like
- Ensure the data is usable in the tool
- Do not rely on a separate “pipeline” in the future

---

## Meeting Process Improvements
- Send an agenda with specific asks before meetings
- If there are no asks, we probably should not meet
- Ensure the right people are in the room
- When presenting LLM-related updates, keep it succinct and in bullet points
