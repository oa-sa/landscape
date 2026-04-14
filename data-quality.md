# Data Quality: Issues and How We Handle Them

This document records data quality issues found across Australian government open data sources and how the pipeline addresses them.

## State Values

### Problem
State fields are inconsistent across sources:
- Some use full names ("New South Wales", "South Australia")
- Some use codes ("NSW", "SA", "Qld")
- Some have empty values or "(blank)"
- The SA Community Directory uses "South Australia" for in-state records but full names for interstate ones

### Solution
The pipeline normalises all state values to standard 2-3 letter codes (NSW, VIC, QLD, SA, WA, TAS, NT, ACT). When the state is empty, it attempts to derive it from:
1. The postcode (using Australian postcode ranges)
2. The address string (pattern matching)

### Remaining Gap
~741 records still have no state - these have no postcode and no state in the address. Mostly from the SA Community Directory where organisations don't have a physical address.

## Categories

### Problem
Each data source uses its own category labels. The SA Community Directory alone has 21 different category values (e.g., "Recreation", "Community Organisation & Development", "Health & Disability", "Personal & Family Support"). Without mapping, 31% of records were tagged "other".

### Solution
The pipeline maps all known source category labels to a standard set of 17 categories. After mapping, only 0.6% of records remain as "other".

### Standard Categories
food, housing, health, mental_health, alcohol_drugs, legal, financial, employment, education, disability, family, community, information, transport, personal_care, technology, other

## Record Quality

### Problem
Not all records are equally useful. Some have a name but no address, no phone, no website - making it hard for someone to actually find or contact the service.

### Solution
Each record is scored with a `quality` field:
- **complete** - has location (address or coordinates) AND contact info (phone, website, or email). 84% of records.
- **partial** - has either location OR contact info, but not both. 15% of records.
- **minimal** - has neither. 0.3% of records.

This allows consumers of the data to filter by quality - e.g., only show "complete" records in a search interface.

## Duplicate Names

### Observation
Some service names appear hundreds of times (e.g., "MAX Employment" appears 501 times). These are **not duplicates** - they are individual branch locations of the same organisation across Australia. This is expected from the Employment Services dataset, which lists every branch of every provider.

## Data Freshness

### Problem
Government datasets are snapshots in time. Some are from 2016, others from 2021, others are regularly updated. A service listed in 2016 may have closed.

### How We Track It
- Every record has a `source_date` field showing when it was last fetched
- The original dataset age varies - the pipeline can only fetch what's published
- Government portals update at their own pace; we can't force freshness

### Sources by Data Age
| Source | Data Vintage |
|--------|-------------|
| Fed Emergency Relief | October 2016 |
| Fed Employment Services | May 2016 |
| SA Community Directory | 2021 |
| QLD datasets | Various, 2023-2025 |
| VIC council datasets | Various, 2013-2025 |
| OpenStreetMap | Continuously updated |

## QLD Data Access

### Problem
The QLD government portal (data.qld.gov.au) blocks direct CSV downloads from cloud/datacenter IPs (e.g., GitHub Actions). Requests return empty responses.

### Solution
QLD sources use the CKAN Datastore API (`datastore_search`) instead of direct CSV downloads. The API returns the same data and is not blocked from cloud IPs.

## OpenStreetMap Coverage

### Problem
The Overpass public API has fair-use rate limits. Querying all of Australia for multiple tag types can time out or get rate limited, especially from cloud IPs.

### Solution
- Queries are split state by state with 3-second pauses
- Timeout set to 90 seconds per state
- Partial results are accepted - some states may not come through every run
- Coverage improves over multiple runs

### Current Coverage
Consistently returns: VIC, QLD, TAS
Sometimes times out: NSW, SA, WA, NT, ACT (depends on Overpass server load)
