# Hotel Deals Hunter

Find 4-star hotels with the highest discounts across Booking.com, Expedia, Hotels.com, Priceline, Kayak, and Trivago.

## Instructions

When this skill is invoked, follow these steps:

### 1. Parse User Input
Extract from the user's message:
- **Destination** (city/region)
- **Check-in / Check-out dates**
- **Number of guests** (default: 2 if not specified)
- **Number of results** (default: top 4 deals)

If destination or dates are missing, ask for them before proceeding.

### 2. Search Each Platform
Run parallel WebSearch queries for each site. Use search queries like:
- `site:booking.com "[destination]" 4-star hotel discount "[check-in]"`
- `site:expedia.com "[destination]" 4-star hotel deal "[check-in]"`
- `"hotels.com" "[destination]" 4-star hotel discount`
- `site:priceline.com "[destination]" hotel 4-star deal`
- `site:kayak.com "[destination]" hotel 4-star lowest price`
- `site:trivago.com "[destination]" 4-star hotel`

Also run a general cross-platform search:
- `"[destination]" 4-star hotel best discount "[check-in date]" -"5 star" -"3 star"`

### 3. Fetch Deal Pages
For each promising result URL returned, use WebFetch to extract:
- Hotel name
- Star rating (confirm 4-star)
- Original price (crossed-out / "was" price)
- Current price
- Discount percentage or savings amount
- Source platform
- Direct booking URL

### 4. Calculate & Rank Discounts
- Compute discount % if not shown: `(original - current) / original * 100`
- Rank all found hotels by discount % descending
- Select the **top 4** with the highest discounts

### 5. Present Results
Output a clean comparison table:

```
## Top 4 Hotel Deals: [Destination] | [Check-in] → [Check-out]

| # | Hotel | Stars | Original | Deal Price | Discount | Platform | Link |
|---|-------|-------|----------|------------|----------|----------|------|
| 1 | ...   | ⭐⭐⭐⭐ | $XXX/night | $XXX/night | XX% off | Booking.com | [Book] |
| 2 | ...   | ⭐⭐⭐⭐ | $XXX/night | $XXX/night | XX% off | Expedia | [Book] |
| 3 | ...   | ⭐⭐⭐⭐ | $XXX/night | $XXX/night | XX% off | Hotels.com | [Book] |
| 4 | ...   | ⭐⭐⭐⭐ | $XXX/night | $XXX/night | XX% off | Kayak | [Book] |
```

Below the table, add a **Quick Take** for the top deal: what makes it special (location, amenities, reviews), and any caveats (non-refundable, limited availability, etc.).

### Notes
- If a site blocks WebFetch, skip it and note it in the output.
- If fewer than 4 deals are found with confirmed discounts, show what was found and state the limitation.
- Always include the direct booking URL so the user can verify prices themselves — prices change frequently.
- Do not fabricate prices. Only report prices actually retrieved from pages.
