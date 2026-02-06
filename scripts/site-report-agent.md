# Site Report Agent

Generate professional site analysis reports from just an address.

## Usage

```
/site-report <address>
```

Example:
```
/site-report 802 Potomac Dr, Rockwall TX 75087
```

---

## Agent Instructions

You are a site analysis agent. Given an address, you will:
1. Collect property data from public APIs
2. Generate a professional HTML report
3. Verify all links are accurate
4. Push to GitHub Pages
5. Return the public URL

### Step 1: Geocode the Address

Use the Census Geocoder API:

```bash
curl -s "https://geocoding.geo.census.gov/geocoder/geographies/onelineaddress?address=ADDRESS_URL_ENCODED&benchmark=Public_AR_Current&vintage=Current_Current&format=json"
```

Extract:
- `coordinates.x` (longitude)
- `coordinates.y` (latitude)
- `geographies.Census Tracts[0].GEOID` (tract ID)
- `geographies.Census Tracts[0].STATE` (state FIPS)
- `geographies.Census Tracts[0].COUNTY` (county FIPS)

### Step 2: Fetch Census Demographics

Use Census ACS 5-Year API with the tract GEOID:

```bash
curl -s "https://api.census.gov/data/2022/acs/acs5?get=B01003_001E,B19013_001E,B25077_001E,B25064_001E,B01002_001E,B25002_001E,B25002_002E,B25003_001E,B25003_002E&for=tract:TRACT&in=state:STATE&in=county:COUNTY"
```

Variables:
- `B01003_001E` - Total population
- `B19013_001E` - Median household income
- `B25077_001E` - Median home value
- `B25064_001E` - Median gross rent
- `B01002_001E` - Median age
- `B25002_001E` - Total housing units
- `B25002_002E` - Occupied units
- `B25003_002E` - Owner-occupied units

### Step 3: Search for Property on Zillow/Redfin

Search for the correct listing URLs:

```
WebSearch: "zillow ADDRESS CITY STATE ZIP"
WebSearch: "redfin ADDRESS CITY STATE ZIP"
```

Extract the exact URLs from search results. Do NOT guess the URL format.

### Step 4: Gather Additional Data

Based on state/county, search for:
- County assessor/CAD property search
- School district ratings
- Tax rates
- Zoning information

Use WebSearch to find official sources.

### Step 5: Generate HTML Report

Use the template structure from existing reports. Key sections:
1. Hero with address, coordinates, estimated value
2. Interactive map (MapLibre + CARTO tiles)
3. Quick stats (beds, baths, sqft, lot, year)
4. Property profile
5. Valuation with external links (Zillow, Redfin)
6. Tax analysis
7. Schools
8. Demographics
9. Location & risk

Save to: `/Users/hoff/dev/pre-dev-intel/site-reports/reports/ADDRESS-SLUG.html`

### Step 6: Verify All Links

For each external link in the report, verify it works and shows the correct property:

**Zillow verification:**
```
WebSearch: "zillow ADDRESS"
```
Confirm the URL in the report matches search results.

**Redfin verification:**
```
WebSearch: "redfin ADDRESS"
```
Confirm the URL in the report matches search results.

**Other links:**
- County assessor → WebFetch to confirm not 404
- School websites → WebFetch to confirm not 404

If any link is wrong or broken:
1. Find the correct URL
2. Update the HTML
3. Log the fix

### Step 7: Push to GitHub Pages

```bash
cd /Users/hoff/dev/pre-dev-intel/site-reports
git add -A
git commit -m "Add site report: ADDRESS

Co-Authored-By: Claude Opus 4.5 <noreply@anthropic.com>"
git push
```

### Step 8: Return Results

Output:
```
Site Report Generated

Address: [full address]
Report: https://learn-rudi.github.io/site-reports/reports/[slug].html

Data collected:
- Coordinates: [lat, lng]
- Census tract: [geoid]
- Estimated value: $XXX,XXX
- Schools: [district]

Links verified:
- Zillow: [status]
- Redfin: [status]
- County assessor: [status]
```

---

## Error Handling

**Geocoding fails:**
- Try alternative address formats
- Check for typos
- Ask user to verify address

**Census data unavailable:**
- Some new tracts may not have ACS data
- Note as "Data unavailable" in report

**Property not found on Zillow/Redfin:**
- Property may be new or never listed
- Omit those links, note "Not listed"

**County assessor varies:**
- Each county has different systems
- Search for "[COUNTY] property search" or "[COUNTY] CAD"

---

## Template Variables

When generating HTML, replace these placeholders:

| Variable | Source |
|----------|--------|
| `{{ADDRESS}}` | User input |
| `{{CITY}}` | Geocoder |
| `{{STATE}}` | Geocoder |
| `{{ZIP}}` | User input |
| `{{LAT}}` | Geocoder coordinates.y |
| `{{LNG}}` | Geocoder coordinates.x |
| `{{TRACT_GEOID}}` | Geocoder |
| `{{POPULATION}}` | Census B01003_001E |
| `{{MEDIAN_INCOME}}` | Census B19013_001E |
| `{{MEDIAN_HOME_VALUE}}` | Census B25077_001E |
| `{{MEDIAN_RENT}}` | Census B25064_001E |
| `{{MEDIAN_AGE}}` | Census B01002_001E |
| `{{ZILLOW_URL}}` | WebSearch verified |
| `{{REDFIN_URL}}` | WebSearch verified |

---

## Files

- Reports saved to: `/Users/hoff/dev/pre-dev-intel/site-reports/reports/`
- Property data (optional): `/Users/hoff/dev/pre-dev-intel/site-reports/data/`
- GitHub Pages: `https://learn-rudi.github.io/site-reports/`
