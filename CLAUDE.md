# Site Reports

Professional site analysis reports for US property addresses.

## Skills

### /site-report

Generate a complete site analysis report from just an address.

```
/site-report 802 Potomac Dr, Rockwall TX 75087
```

See `.claude/skills/site-report.md` for full instructions.

## Project Structure

```
site-reports/
├── reports/          # Generated HTML reports
├── drafts/           # Email drafts
├── scripts/          # Agent documentation
├── .claude/skills/   # Skill definitions
└── index.html        # Landing page
```

## GitHub Pages

Reports are published to: https://learn-rudi.github.io/site-reports/

## Data Sources

- **Census Geocoder** - Address coordinates, tract ID
- **Census ACS 5-Year** - Demographics (income, age, housing)
- **Zillow/Redfin** - Valuation estimates (linked, not scraped)
- **County assessors** - Property details, tax records

## Report Features

- Interactive MapLibre map with POI markers
- Print-friendly CSS for PDF export
- Dark theme with gold accents
- Mobile responsive
- Verified external links
