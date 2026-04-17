# Access Barriers: Public Data That Isn't Open

This document records datasets that contain Australian support services data, are funded by government or operated by public institutions, but are not available for bulk download or open reuse.

## Listed as open but not downloadable

### Centrelink Office Locations (National 2019)

- **Listed on**: data.gov.au
- **Source**: Department of Human Services
- **Format**: WFS (Web Feature Service)
- **Barrier**: Download is routed through AURIN (Australian Urban Research Infrastructure Network), which requires an Australian university or research institution login
- **What it contains**: ~300 Centrelink office locations with coordinates
- **Irony**: Centrelink offices are public buildings with addresses published on the Services Australia website, but the structured dataset with coordinates is locked behind academic credentials

### Centrelink Office Locations (SA)

- **Listed on**: data.gov.au
- **Source**: Department of Human Services
- **Format**: Shapefile only
- **Barrier**: No CSV or JSON available. Requires GIS software or a conversion library to use

## Gated APIs - requires partner approval

### National Health Services Directory (NHSD)

- **Operator**: healthdirect Australia / Australian Digital Health Agency
- **Records**: 390,000+ health services
- **Coverage**: GPs, hospitals, pharmacies, emergency departments, mental health, allied health
- **Barrier**: API access requires approval as a partner organisation. No bulk download. No open license.

### HealthPathways

- **Operator**: 31 Primary Health Networks
- **Records**: Unknown (one of the largest clinical referral systems in Australia)
- **Barrier**: Entirely locked behind clinician logins. Not accessible to the public at all.

## No bulk access - search only

### My Aged Care

- **Operator**: Department of Health and Aged Care
- **What it contains**: National directory of aged care providers and services
- **Barrier**: Search interface only. No API, no download, no open data.

### NDIS Provider Finder

- **Operator**: National Disability Insurance Agency
- **What it contains**: Registered NDIS provider locations
- **Barrier**: Partial search interface. No bulk download. Some provider lists available via data.gov.au but not comprehensive.

### Service NSW

- **Operator**: NSW Government
- **What it contains**: Government service locations and programs across NSW
- **Barrier**: No open data published on data.nsw.gov.au for service locations.

## Sector-specific directories - no open access

| Directory | Operator | Coverage | Barrier |
|-----------|----------|----------|---------|
| Salvation Army | Salvation Army Australia | Emergency relief nationally | No API, no download |
| St Vincent de Paul | St Vincent de Paul Society | Emergency relief nationally | No API, no download |
| Foodbank Australia | Foodbank | Food relief nationally | No API, no download |
| Anglicare | Anglicare Australia | Welfare services nationally | No API, no download |
| Lifeline | Lifeline Australia | Crisis support nationally | No API, no download |
| ReachOut | ReachOut Australia | Youth mental health nationally | No API, no download |

## The pattern

The data exists. In most cases it was collected with public funding or by publicly funded organisations. But there is no mechanism for bulk download, no open license, and no API for independent developers or researchers to build on.

This means:

- Anyone wanting to build tools for vulnerable people must negotiate access with each provider individually
- Researchers cannot study service coverage or accessibility gaps across the landscape
- There is no way to verify whether services are accurately listed or discoverable
- The public has no fallback if any single provider changes their access terms

These are not obscure datasets. They represent the core infrastructure of support services in Australia.
