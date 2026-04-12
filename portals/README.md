# Australian Government Open Data Portals

Australia's open data is published across federal, state, and local government portals. This document covers how they work and how to access them programmatically.

## Overview

| Level | Portal | URL | Platform |
|-------|--------|-----|----------|
| Federal | data.gov.au | https://data.gov.au | CKAN |
| NSW | NSW Open Data | https://data.nsw.gov.au | CKAN |
| VIC | Data.Vic | https://discover.data.vic.gov.au | CKAN |
| QLD | QLD Open Data | https://www.data.qld.gov.au | CKAN |
| SA | Data SA | https://data.sa.gov.au | CKAN |
| WA | WA Data Catalogue | https://catalogue.data.wa.gov.au | CKAN |

All six portals run on **CKAN** (Comprehensive Knowledge Archive Network), an open-source data management system. This means they all share the same API structure.

## How CKAN Works

CKAN organises data into:

- **Organisations** — the government agency that publishes the data (e.g., Department of Social Services)
- **Datasets** (called "packages") — a collection of related data (e.g., "Emergency Relief Provider Outlets")
- **Resources** — the actual downloadable files within a dataset (e.g., a CSV file, a JSON file, a GeoJSON file)

A single dataset can have multiple resources in different formats.

## The CKAN API

All portals expose a REST API at the same path. No authentication is required for reading public data.

### Base URLs

```
Federal:  https://data.gov.au/data/api/3/action/
NSW:      https://data.nsw.gov.au/data/api/3/action/
VIC:      https://discover.data.vic.gov.au/api/3/action/
QLD:      https://www.data.qld.gov.au/api/3/action/
SA:       https://data.sa.gov.au/data/api/3/action/
WA:       https://catalogue.data.wa.gov.au/api/3/action/
```

### Key Endpoints

| Endpoint | Purpose |
|----------|---------|
| `package_search?q={query}&rows={count}&start={offset}` | Search datasets by keyword |
| `package_show?id={dataset_id}` | Get full metadata for a single dataset |
| `package_list` | List all dataset identifiers |
| `resource_show?id={resource_id}` | Get metadata for a specific downloadable file |
| `organization_list` | List all publishing organisations |
| `organization_show?id={org_id}` | Details about one organisation |
| `tag_list` | List all tags used across datasets |
| `license_list` | List all available licenses on the portal |

### Response Format

All endpoints return JSON in this structure:

```json
{
  "success": true,
  "result": {
    "count": 100,
    "results": [...]
  }
}
```

### Pagination

Use `rows` and `start` parameters:

```
?q=community+services&rows=50&start=0    # First 50 results
?q=community+services&rows=50&start=50   # Next 50 results
```

### Filtering by Organisation

```
?fq=organization:{org_id}&rows=100
```

---

## Example: Searching for Datasets

### Using curl

```bash
# Search for community services datasets on data.gov.au
curl "https://data.gov.au/data/api/3/action/package_search?q=community+services&rows=5"
```

### Using Python

```python
import requests

base_url = "https://data.gov.au/data/api/3/action/package_search"
params = {"q": "community services", "rows": 50}

response = requests.get(base_url, params=params)
data = response.json()

print(f"Total datasets found: {data['result']['count']}")

for dataset in data["result"]["results"]:
    print(f"\n{dataset['title']}")
    print(f"  Organisation: {dataset.get('organization', {}).get('title', 'N/A')}")
    print(f"  License: {dataset.get('license_title', 'N/A')}")
    for resource in dataset.get("resources", []):
        print(f"  Resource: {resource['format']} — {resource['url']}")
```

## Example: Downloading a Dataset

Once you find a dataset, each resource has a `url` field pointing to the actual file.

### Using curl

```bash
# Download City of Melbourne support services as CSV
curl -L "https://data.melbourne.vic.gov.au/api/v2/catalog/datasets/free-and-cheap-support-services-with-opening-hours-public-transport-and-parking-/exports/csv?delimiter=%2C" -o services.csv
```

### Using Python

```python
import requests
import csv
import io

url = "https://data.melbourne.vic.gov.au/api/v2/catalog/datasets/free-and-cheap-support-services-with-opening-hours-public-transport-and-parking-/exports/csv?delimiter=%2C"

response = requests.get(url)
reader = csv.DictReader(io.StringIO(response.text))

for row in reader:
    print(f"{row['name']} — {row['suburb']} — {row['phone']}")
```

## Example: Querying All Portals

Because every portal uses the same API, you can query them all with one function:

```python
import requests

PORTALS = {
    "federal": "https://data.gov.au/data/api/3/action/",
    "nsw": "https://data.nsw.gov.au/data/api/3/action/",
    "vic": "https://discover.data.vic.gov.au/api/3/action/",
    "qld": "https://www.data.qld.gov.au/api/3/action/",
    "sa": "https://data.sa.gov.au/data/api/3/action/",
    "wa": "https://catalogue.data.wa.gov.au/api/3/action/",
}

def search_all_portals(query, rows=10):
    for name, base_url in PORTALS.items():
        try:
            url = f"{base_url}package_search"
            response = requests.get(url, params={"q": query, "rows": rows}, timeout=15)
            data = response.json()
            count = data["result"]["count"]
            print(f"\n[{name.upper()}] {count} datasets found for '{query}'")
            for dataset in data["result"]["results"]:
                print(f"  - {dataset['title']}")
        except Exception as e:
            print(f"\n[{name.upper()}] Error: {e}")

search_all_portals("food relief")
```

---

## Council-Level Open Data Portals

Many local councils also publish open data. These use various platforms (not always CKAN).

### Victoria

| Council | Portal URL | Platform |
|---------|-----------|----------|
| City of Melbourne | data.melbourne.vic.gov.au | OpenDataSoft |
| City of Casey | data.casey.vic.gov.au | CKAN (via Data.Vic) |
| City of Ballarat | data.ballarat.vic.gov.au | OpenDataSoft |

### New South Wales

| Council | Portal URL | Platform |
|---------|-----------|----------|
| City of Sydney | data.cityofsydney.nsw.gov.au | ArcGIS Open Data |
| City of Wollongong | data-wollongong.opendata.arcgis.com | ArcGIS Open Data |
| Lake Macquarie | data-lakemac.opendata.arcgis.com | ArcGIS Open Data |
| City of Newcastle | newcastle.nsw.gov.au/open-data | Various |
| Northern Beaches | data.northernbeaches.nsw.gov.au | ArcGIS Open Data |
| City of Parramatta | data.cityofparramatta.nsw.gov.au | ArcGIS Open Data |

Council portals using **OpenDataSoft** have their own API (different from CKAN). Council portals using **ArcGIS Open Data** expose REST endpoints for querying.

---

## OpenStreetMap

OpenStreetMap (OSM) is a community-maintained open map of the world. It contains social facility data that can be queried via the Overpass API.

### License

Open Database License (ODbL) — free to use, share, and adapt. Attribution required: "© OpenStreetMap contributors".

### Relevant Tags

| Tag | What It Represents |
|-----|--------------------|
| `amenity=social_facility` + `social_facility=food_bank` | Food banks |
| `amenity=social_facility` + `social_facility=soup_kitchen` | Soup kitchens |
| `amenity=social_facility` + `social_facility=shelter` | Homeless shelters |
| `amenity=social_facility` + `social_facility=counselling` | Counselling services |
| `amenity=social_facility` + `social_facility=outreach` | Outreach services |
| `amenity=social_facility` + `social_facility=clothing_bank` | Clothing banks |
| `amenity=community_centre` | Community centres |
| `amenity=library` | Libraries |
| `amenity=clinic` / `amenity=doctors` | Health clinics / GPs |
| `office=government` | Government offices |
| `office=ngo` / `office=charity` | NGOs and charities |

### Population Served Tags

`social_facility:for=` can be set to: `homeless`, `refugee`, `abused`, `senior`, `disabled`, `underprivileged`, `mental_health`, `juvenile`

### Querying via Overpass API

```
POST https://overpass-api.de/api/interpreter

# All social facilities in Australia
[out:json][timeout:120];
area["ISO3166-1"="AU"]->.australia;
(
  nwr["amenity"="social_facility"](area.australia);
  nwr["amenity"="community_centre"](area.australia);
  nwr["amenity"="library"](area.australia);
  nwr["office"="ngo"](area.australia);
  nwr["office"="charity"](area.australia);
);
out center;
```

### Interactive Testing

You can test Overpass queries interactively at [overpass-turbo.eu](https://overpass-turbo.eu).

---

## Notes

- **SA's portal** harvests datasets from data.gov.au (federal) and other state portals. Search result counts may appear inflated — filter for SA-specific data.
- **WA's portal** has two URLs: `data.wa.gov.au` (Drupal frontend) and `catalogue.data.wa.gov.au` (CKAN backend). Use the catalogue URL for API access.
- **Council portals** vary in platform and API. OpenDataSoft and ArcGIS have their own query patterns.
- All dataset counts and availability documented here are as of April 2026 and may change.
