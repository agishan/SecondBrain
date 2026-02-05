**Date:** January 23, 2026  
**Context:** Pre–January 28 CMH Meeting

## Tool Goal Clarification

### Primary Objective
Enable nurses to:
- Identify variation across surgeon pick-lists
- Consolidate SKUs by surfacing interchangeable items

### Key Insight
The goal is **variation**, not rarity or price.

**Example**
Six surgeons perform the same surgery using six different saw blades.
These are potentially swappable even if none are rare.

### Swappable Item Criteria
- Same surgery type
- Same functional purpose
- Limited use in other surgeries
- Configurable usage threshold

## Dashboard Design

### Screen 1: Surgery Overview
- Surgery type dropdown
- Summary stats bar
- Surgeon list with sortable metrics
- Heat map of item variation
- Toggle between category-level and item-level views

### Screen 2: Individual Pick-List
- Surgeon-level statistics
- Item table with cost and usage context
- Swap interface with:
  - Candidate substitutes
  - Cost comparisons
  - Multi-item swaps
  - Explicit confirmation steps

## Visualization Decisions
Alternatives considered:
- Line charts
- Bar charts
- Circo diagrams

Decision:
- Heat maps remain best choice
- Preferred by CMH
- Easy to scan

## Swappability Logic
- Frequency-based identification
- Transitive swappability allowed with confidence levels
- User confirmations stored in database
- No learning model required initially

Open question:
- Swappability per surgery only, or cross-surgery?

## Technical Decisions
- Frontend: React
- Backend: Django
- Database: SQLite

Rationale:
- Python-native logic
- Portable data storage
- Strong UI control

## Labeling
- One-shot LLM labeling
- Manual verification baseline
- Labels represent categories, not guarantees

## Questions for CMH
- Output format
- Upload workflow
- Streamlit vs web app
- Swappability scope

## Next Steps
- Prepare UI mockups
- Validate output format
- Build minimal prototype

## Related Docs
- [Architecture](../technical/architecture.md)
- [Swappability Logic](../technical/swappability-logic.md)
