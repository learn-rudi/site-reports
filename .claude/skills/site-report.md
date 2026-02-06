# /site-report

Generate a professional site analysis report for any US property address.

## Trigger
```
/site-report <address>
```

## Examples
```
/site-report 802 Potomac Dr, Rockwall TX 75087
/site-report 123 Main St, Cincinnati OH 45202
/site-report 456 Oak Ave, Austin TX 78701
```

## What This Skill Does

1. **Geocodes the address** using Census Geocoder API
2. **Fetches demographics** from Census ACS 5-Year data
3. **Finds property listings** on Zillow and Redfin
4. **Generates professional HTML report** with:
   - Interactive map (MapLibre)
   - Property details
   - Valuation estimates
   - Tax analysis
   - School ratings
   - Demographics
   - Risk assessment
5. **Verifies all links** are accurate and working
6. **Pushes to GitHub Pages**
7. **Returns the public URL**

## Agent Workflow

### Phase 1: Data Collection

```bash
# 1. Geocode address
curl -s "https://geocoding.geo.census.gov/geocoder/geographies/onelineaddress?address=ENCODED_ADDRESS&benchmark=Public_AR_Current&vintage=Current_Current&format=json"

# 2. Fetch census demographics (use tract/state/county from geocoder)
curl -s "https://api.census.gov/data/2022/acs/acs5?get=B01003_001E,B19013_001E,B25077_001E,B25064_001E,B01002_001E,B25002_002E,B25003_002E&for=tract:XXXX&in=state:XX&in=county:XXX"
```

### Phase 2: Find Listing URLs

Use WebSearch to find the EXACT URLs (do not guess):
```
WebSearch: zillow [full address]
WebSearch: redfin [full address]
```

### Phase 3: Generate Report

Copy the template from the existing report:
`/Users/hoff/dev/pre-dev-intel/site-reports/reports/802-potomac-dr-rockwall.html`

Replace all address-specific values:
- Title, address, coordinates
- Census demographics
- Property details (from listings)
- Map center point
- External links

Save to:
`/Users/hoff/dev/pre-dev-intel/site-reports/reports/[address-slug].html`

### Phase 4: Verify Links

For each external link, verify it points to the correct property:

```
WebSearch: zillow [address]
→ Compare URL in report to search result
→ Fix if different

WebSearch: redfin [address]
→ Compare URL in report to search result
→ Fix if different
```

### Phase 5: Deploy

```bash
cd /Users/hoff/dev/pre-dev-intel/site-reports
git add -A
git commit -m "Add site report: [ADDRESS]

Co-Authored-By: Claude Opus 4.5 <noreply@anthropic.com>"
git push
```

### Phase 6: Return URL

```
Report generated: https://learn-rudi.github.io/site-reports/reports/[slug].html

Data collected:
- Coordinates: [lat], [lng]
- Census tract: [geoid]
- Median income: $XXX,XXX
- Median home value: $XXX,XXX

Links verified:
- Zillow: [URL] ✓
- Redfin: [URL] ✓
```

## Census Variables Reference

| Variable | Description |
|----------|-------------|
| B01003_001E | Total population |
| B19013_001E | Median household income |
| B25077_001E | Median home value |
| B25064_001E | Median gross rent |
| B01002_001E | Median age |
| B25002_001E | Total housing units |
| B25002_002E | Occupied housing units |
| B25003_002E | Owner-occupied units |
| B23025_005E | Unemployed population |
| B23025_003E | Labor force |

## File Locations

- Reports: `/Users/hoff/dev/pre-dev-intel/site-reports/reports/`
- Template: `/Users/hoff/dev/pre-dev-intel/site-reports/reports/802-potomac-dr-rockwall.html`
- GitHub Pages: `https://learn-rudi.github.io/site-reports/`

## Error Handling

| Issue | Resolution |
|-------|------------|
| Geocoding fails | Try alternate address format, ask user to verify |
| Property not on Zillow/Redfin | Omit link, note "Not listed" |
| Census data unavailable | Note "Data unavailable" for that field |
| Link verification fails | Search again, find correct URL |
