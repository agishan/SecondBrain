# Advisor Kick-Off Meeting

**Date:** January 14, 2026  
**Attendees:**  
Agishan Thaya, Callum Gillies, Curtis Tse, Graydon Power, Liam Mitchell  
Dr Mahmoudzadeh, Dr Dimitrov

## Purpose
- Align on project direction
- Validate approach to labeling and swappability
- Define expectations for CMH interaction
- Clarify validation strategy

## Confirmed Project Goal
Build a **decision support tool**, not an optimization system, that:
- Shows how pick-lists differ across surgeons
- Groups items serving similar purposes
- Surfaces candidate interchangeable items
- Allows CMH to make final decisions
- Accepts imperfect accuracy in exchange for usability and transparency

## Technical Approach

### LLM-Based Labeling
- Used for first-pass semantic grouping
- Narrows search space, does not imply interchangeability
- Broader labels are acceptable

**Advisor Guidance**
- Continue with LLM first pass
- Use higher reasoning parameters
- Do not ask LLMs for numeric confidence scores
- Instead, ask LLMs to generate Python code to compute confidence

### Frequency / Usage-Based Algorithm
- Identifies items used by different surgeons but not together
- More precise than labeling alone
- Produces candidate substitutions, not final answers

**Advisor Guidance**
- Current logic must be defensible
- Ground in literature where possible
- Explicitly state assumptions and limitations

## Validation Strategy Shift
- Relax constraint that all labeled items must be substitutes
- Labels represent potential similarity
- CMH feedback refines results over time
- Focus on learning CMH decision process

## CMH Interaction Improvements
Problems:
- Excel-based validation causes fatigue
- Binary validation is inefficient
- Meetings lacked concrete asks

New Direction:
- One final labeling validation round
- Interactive validation interface
- Surface only most uncertain items
- Learn from CMH input over time

## Learning & Adaptation
Two-layer approach:
1. Initial LLM labeling
2. Learning algorithm informed by CMH feedback

Potential framing:
- Online learning
- Preference learning from experts

## Visualization Direction
- Heatmaps supported and preferred
- Show usage, cost, frequency context
- Allow manual adjustment of labels and groups

## Action Items
- Refine frequency-based algorithm
- Explore defensible heuristics in literature
- Prototype validation interface concept
- Clarify CMH input data format
- Improve meeting agendas

## Related Docs
- [Labeling Strategy](../technical/labeling-strategy.md)
- [Swappability Logic](../technical/swappability-logic.md)
- [Validation Strategy](../technical/validation-strategy.md)
