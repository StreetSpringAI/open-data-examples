# StreetSpring Business Survivability Datasets: documentation

Version 2026.09.25.1 (updated 2026-09-25). This document is generated from the published files by `build_documentation.py`; every count, range and fill rate below is computed from them.

## Contents

- [About StreetSpring](#about-streetspring)
- [What the data is](#what-the-data-is)
- [Accessing the data](#accessing-the-data)
- [How the data is organized](#how-the-data-is-organized)
- [File format](#file-format)
- [Files](#files)
- [Ranks and tiers](#ranks-and-tiers)
- [Data dictionary: metro files](#data-dictionary-metro-files)
- [Data dictionary: national file](#data-dictionary-national-file)
- [Coverage by metro](#coverage-by-metro)
- [Known limits](#known-limits)
- [Versions and update policy](#versions-and-update-policy)
- [License and citation](#license-and-citation)
- [Contact](#contact)

## About StreetSpring

StreetSpring is a location intelligence company for small businesses, based in Wilmington, Delaware, and founded in 2025 by Robert Koons. Its tool scores a specific address for a specific type of business and reports the projected chance that a business of that type lasts two or more years there. These datasets publish the neighborhood, town and metro aggregates of those scores under CC BY 4.0 so that researchers, journalists, planners and prospective business owners can compare places without an account. StreetSpring scores 500+ business types: 153 base business types, most of them at 5 price points, which is 569 type-and-price combinations you can choose in the tool. The files carry 110 of the base business types.

## What the data is

Every published score is the projected chance that a business of that type lasts two or more years at that place, as a percentage. It is never the model's raw output. Every file states this in its header, in a line beginning `# Scale:`.

- **Coverage:** 24 U.S. metropolitan areas, 3,514 places (3,022 neighborhoods and 492 towns), 110 business types, 389,135 rows in 25 CSV files.
- **Two scores per row.** `avg_survivability_score` is the chance at a typical address in the place. `p90_survivability_score` is the chance at a good address: the 90th percentile of the scored locations inside the place. Rankings are ordered on the good-address score. The best and weakest scored address (`max_` and `min_`) and confidence bounds on each sit beside them.
- **Price points.** Each metro file's header says: "This file carries one row per business type and place. Its score is the projected chance of lasting two or more years, averaged across the price points StreetSpring scores separately in the tool (most types have 5). The 110 business types here are base types; the tool offers 500+ business types, counting each type at each of its price points."
- **Observed range.** Across every score and bound column in the metro files, values run from 52.3 to 90.8.
- **Model.** Every file is produced by model version `v1-2026-03-12` (the `# Model version:` header line). Each file header states that the scores are based on 100+ location factors and 570,000+ real business outcomes. How the score is built, validated and calibrated is set out in the methodology: https://streetspring.com/resources/tutorial/aeo-methodology
- **What it is not.** The score is a projection for a type of business at a type of address. It is not an observed survival rate, not a guarantee for any operator, and not a revenue forecast.

## Accessing the data

The files are public and need no AWS account or credentials.

- S3: `s3://streetspring-seo/open-data/` (region `us-east-1`)
- HTTPS: `https://streetspring-seo.s3.amazonaws.com/open-data/<file name>`
- Website copy of record: https://streetspring.com/resources/datasets
- Zenodo archive: concept DOI 10.5281/zenodo.22287996 (always the newest version); this version is DOI 10.5281/zenodo.22964004
- GitHub mirror: https://github.com/StreetSpringAI/streetspring-survivability-datasets
- Tutorial notebook, Get To Know A Dataset: https://github.com/StreetSpringAI/open-data-examples/blob/main/streetspring-survivability/get-to-know-a-dataset.ipynb

```bash
aws s3 ls --no-sign-request s3://streetspring-seo/open-data/
aws s3 cp --no-sign-request s3://streetspring-seo/open-data/national-survivability-scores-2026.csv .
```

```python
import pandas as pd
url = "https://streetspring-seo.s3.amazonaws.com/open-data/philadelphia-survivability-scores-2026.csv"
df = pd.read_csv(url, comment="#")   # skips the comment lines at the top of the file
```

## How the data is organized

All objects sit directly under `open-data/`; there are no deeper prefixes and no partitions.

| Object | Bytes | What it is |
|---|---|---|
| `national-survivability-scores-2026.csv` | 1,101,431 | One row per metro and business type |
| `<metro>-survivability-scores-2026.csv` (24 files) | 233,840,067 in total | One row per place (neighborhood or town) and business type |
| `README.md` | 20,716 | Column dictionary, recipes and worked examples |
| `CHANGELOG.md` | 5,043 | Every version and what changed |
| `LICENSE` | | CC BY 4.0 summary and link to the legal code |
| `CITATION.cff` | | Citation metadata |

Metro slugs used in file names: atlanta, baltimore, boston, charlotte, chicago, dallas, denver, detroit, houston, los-angeles, miami, minneapolis, new-york-city, orlando, philadelphia, phoenix, portland, san-antonio, san-diego, san-francisco, seattle, st-louis, tampa-bay, washington-dc.

## File format

- Comma-separated values, plain ASCII text, one header row. Before the header, every file carries 22 to 26 comment lines that start with `#`: dataset name, source, license, citation, model version, scale, version, update date, a partial column dictionary and worked recipes.
- No data row contains a `#` character, so readers that treat `#` as a comment marker (pandas `comment="#"`, R `comment.char = "#"`) read the files correctly.
- SQL engines that do not recognize comment lines (for example a plain CSV table in Amazon Athena) will see the comment lines as rows. The number of comment lines differs between files, so filter rows whose first column starts with `#` rather than skipping a fixed count.
- An empty cell means no value (not measured, not applicable or not ranked); it never means zero. A zero is written as `0` or `0.0`.
- In the metro files, whole-number columns that can be empty (ranks, counts) are written with a trailing `.0`, for example `12.0`. Read them as integers.
- URL columns other than `methodology_url` hold paths relative to https://streetspring.com.

## Files

| File | Metro | Rows | Places | Neighborhoods | Towns | Business types | Places with an overall rank | Rows with a rank |
|---|---|---|---|---|---|---|---|---|
| `atlanta-survivability-scores-2026.csv` | atlanta | 29,260 | 266 | 252 | 14 | 110 | 70 | 6,510 |
| `baltimore-survivability-scores-2026.csv` | baltimore | 30,360 | 276 | 236 | 40 | 110 | 100 | 9,300 |
| `boston-survivability-scores-2026.csv` | boston | 14,300 | 130 | 58 | 72 | 110 | 124 | 11,532 |
| `charlotte-survivability-scores-2026.csv` | charlotte | 10,560 | 96 | 83 | 13 | 110 | 58 | 5,394 |
| `chicago-survivability-scores-2026.csv` | chicago | 18,260 | 166 | 165 | 1 | 110 | 99 | 9,207 |
| `dallas-survivability-scores-2026.csv` | dallas | 8,690 | 79 | 63 | 16 | 110 | 44 | 4,092 |
| `denver-survivability-scores-2026.csv` | denver | 23,760 | 216 | 206 | 10 | 110 | 196 | 18,228 |
| `detroit-survivability-scores-2026.csv` | detroit | 25,960 | 236 | 196 | 40 | 110 | 181 | 16,833 |
| `houston-survivability-scores-2026.csv` | houston | 9,460 | 86 | 57 | 29 | 110 | 86 | 7,998 |
| `los-angeles-survivability-scores-2026.csv` | los-angeles | 26,180 | 238 | 217 | 21 | 110 | 194 | 18,042 |
| `miami-survivability-scores-2026.csv` | miami | 18,810 | 171 | 141 | 30 | 110 | 122 | 11,346 |
| `minneapolis-survivability-scores-2026.csv` | minneapolis | 11,000 | 100 | 80 | 20 | 110 | 84 | 7,812 |
| `new-york-city-survivability-scores-2026.csv` | new-york-city | 21,010 | 191 | 182 | 9 | 110 | 189 | 17,577 |
| `orlando-survivability-scores-2026.csv` | orlando | 8,030 | 73 | 59 | 14 | 110 | 27 | 2,511 |
| `philadelphia-survivability-scores-2026.csv` | philadelphia | 15,840 | 144 | 132 | 12 | 110 | 140 | 13,020 |
| `phoenix-survivability-scores-2026.csv` | phoenix | 10,560 | 96 | 96 | 0 | 110 | 28 | 2,604 |
| `portland-survivability-scores-2026.csv` | portland | 8,580 | 78 | 66 | 12 | 110 | 59 | 5,487 |
| `san-antonio-survivability-scores-2026.csv` | san-antonio | 6,050 | 55 | 52 | 3 | 110 | 0 | 0 |
| `san-diego-survivability-scores-2026.csv` | san-diego | 14,190 | 129 | 113 | 16 | 110 | 100 | 9,300 |
| `san-francisco-survivability-scores-2026.csv` | san-francisco | 23,760 | 216 | 210 | 6 | 110 | 114 | 10,602 |
| `seattle-survivability-scores-2026.csv` | seattle | 14,630 | 133 | 110 | 23 | 110 | 117 | 10,881 |
| `st-louis-survivability-scores-2026.csv` | st-louis | 16,500 | 150 | 78 | 72 | 110 | 130 | 12,090 |
| `tampa-bay-survivability-scores-2026.csv` | tampa-bay | 3,850 | 35 | 30 | 5 | 110 | 18 | 1,674 |
| `washington-dc-survivability-scores-2026.csv` | washington-dc | 16,940 | 154 | 140 | 14 | 110 | 143 | 13,299 |
| **All metro files** | 24 | 386,540 | 3,514 | 3,022 | 492 | 110 | 2,423 | 225,339 |

`national-survivability-scores-2026.csv`: 2,595 rows, 24 metros, 110 business types (110 types in 15 metros, 105 in 9). Every metro carries a city rank for every business type it has in the file.

Total: 25 files, 389,135 rows.

## Ranks and tiers

- **Which rows are ranked.** A place is ranked only if it has at least 10 scored locations of its own and at least 5 peer places of the same `place_kind` in the metro. 1,076 of the 3,514 places have fewer than 10 scored locations. Neighborhoods and towns are ranked in separate lists and never against each other.
- **Which business types are ranked.** 93 of the 110 business types carry ranks: the ones StreetSpring publishes guides for. The other 17 keep their scores with empty rank columns: auto-repair-shop, belgian-restaurant, british-restaurant, burmese-restaurant, cambodian-restaurant, cantonese-restaurant, czech-restaurant, discount-store, electronics-store, eritrean-restaurant, falafel-restaurant, fast-food-restaurant, fish-chips-restaurant, hot-dog-stand, modern-european-restaurant, nicaraguan-restaurant, vitamin-supplement-store.
- **Rank columns** are ordered on the good-address score, `p90_survivability_score` (1 = best). `business_types_ranked_in_neighborhood` is 93 wherever a place is ranked.
- **Tiers** come from a rank's position in its list: top 10 percent Great, next 20 percent Good, middle 40 percent Average, next 20 percent Below-average, bottom 10 percent Poor.
- **Overall rank.** `neighborhood_overall_score` is the mean good-address score across the place's ranked business types; `neighborhood_overall_rank` orders places of the same kind on it, and is repeated on every row of the place. In the national file, `city_overall_rank` orders the metros on `city_overall_score`, the mean good-address score across the 105 business types every metro carries in that file.
- **San Antonio** ranks no places at all (0 ranked rows). Its file header says: "Its neighborhood ranking is withheld because most San Antonio neighborhood names in our boundary data have no matching boundary in the city, and too few of its towns have enough scored locations for a town ranking." Its scores are published and it is ranked among the metros in the national file.

## Data dictionary: metro files

50 columns, the same in all 24 metro files. Filled = share of the 386,540 metro rows with a value. Meanings are from the README beside the files.

| Column | Stored as | Unit | Filled | Observed values | Meaning |
|---|---|---|---|---|---|
| `city` | string | slug | 100.0% | 24 distinct, e.g. atlanta | Metro slug (for example philadelphia). |
| `neighborhood` | string | slug | 100.0% | 3,214 distinct, e.g. 441-corridor | Neighborhood slug inside the metro; the row's place. |
| `business_subtype` | string | slug | 100.0% | 110 distinct, e.g. acupuncture-clinic | Business type slug (for example italian-restaurant); the row's business. The file's own count is in the README and on its page. |
| `business_type_lookup` | string | display name | 100.0% | 110 distinct, e.g. Acupuncture | Display name of the business type (for example Italian Restaurant). |
| `avg_survivability_score` | decimal | percent, 0 to 100 | 100.0% | 55.7 to 89.1 | The projected chance, 0 to 100, that this business type lasts two or more years at a typical address in this neighborhood. The rankings use p90_survivability_score, the good-address score. |
| `max_survivability_score` | decimal | percent, 0 to 100 | 100.0% | 55.7 to 90 | Best address in the neighborhood for this business type, 0 to 100. |
| `min_survivability_score` | decimal | percent, 0 to 100 | 100.0% | 53 to 88.3 | Weakest address in the neighborhood for this business type, 0 to 100. |
| `UPPER_BOUND_MAX_SURVIVABILITY` | decimal | percent, 0 to 100 | 100.0% | 56.2 to 90.8 | Upper confidence bound on max_survivability_score, 0 to 100. |
| `LOWER_BOUND_MAX_SURVIVABILITY` | decimal | percent, 0 to 100 | 100.0% | 54.4 to 90 | Lower confidence bound on max_survivability_score, 0 to 100. |
| `UPPER_BOUND_MIN_SURVIVABILITY` | decimal | percent, 0 to 100 | 100.0% | 54 to 89.3 | Upper confidence bound on min_survivability_score, 0 to 100. |
| `LOWER_BOUND_MIN_SURVIVABILITY` | decimal | percent, 0 to 100 | 100.0% | 52.3 to 87.6 | Lower confidence bound on min_survivability_score, 0 to 100. |
| `neighborhood_rank_for_business_subtype` | integer (written with .0) | rank, 1 = best | 58.3% | 1 to 188 | Rank of this place among the ranked places of the same place_kind in the metro for this business type, on p90_survivability_score (1 = best). Empty when the place or the business type is not ranked (see Ranks and tiers). |
| `tier_of_neighborhood_for_business_subtype` | string | category | 58.3% | Average, Below-average, Good, Great, Poor | Great, Good, Average, Below-average or Poor, from that rank's position in its list: the top 10 percent is Great, the next 20 percent Good, the middle 40 percent Average, the next 20 percent Below-average and the bottom 10 percent Poor. Empty when the place or the business type is not ranked (see Ranks and tiers). |
| `business_subtype_rank_for_neighborhood` | integer (written with .0) | rank, 1 = best | 58.3% | 1 to 93 | Rank of this business type among the ranked types in this place, on p90_survivability_score (1 = the type most likely to last at a good address here). Empty when the place or the business type is not ranked (see Ranks and tiers). |
| `tier_of_business_subtype_for_neighborhood` | string | category | 58.3% | Average, Below-average, Good, Great, Poor | Great, Good, Average, Below-average or Poor, from that rank, with the same cut-offs as the neighborhood tier. Empty when the place or the business type is not ranked (see Ranks and tiers). |
| `total_ranked_neighborhoods_for_business_subtype` | integer (written with .0) | count of places | 58.3% | 5 to 188 | How many places of this place_kind are ranked for this business type in the metro: the length of the list the rank sits in. Empty when the place or the business type is not ranked (see Ranks and tiers). |
| `year` | integer | calendar year | 100.0% | always 2026 | Data year (2026). |
| `source_url_neighborhood_rank` | string | URL path on streetspring.com | 100.0% | 2,640 distinct, e.g. /resources/guides/atlanta/survivability/acupuncture-clinic | Page on streetspring.com that ranks neighborhoods for this business type. |
| `source_url_business_subtype_rank` | string | URL path on streetspring.com | 100.0% | 3,514 distinct, e.g. /resources/guides/atlanta/neighborhoods/aberdeen-forest/business-survivability | Page on streetspring.com that ranks business types for this neighborhood. |
| `methodology_url` | string | absolute URL | 100.0% | https://streetspring.com/resources/tutorial/aeo-methodology | How the score is built: https://streetspring.com/resources/tutorial/aeo-methodology |
| `source_article_url` | string | URL path on streetspring.com | 100.0% | 3,514 distinct, e.g. /resources/guides/atlanta/neighborhoods/aberdeen-forest/business-survivability | The neighborhood's own rankings page on streetspring.com. |
| `min_2yr_failure_rate` | decimal | percent, 0 to 100 | 100.0% | 10 to 44.3 | 100 minus max_survivability_score: the lowest projected two-year failure rate in the neighborhood, 0 to 100. |
| `max_2yr_failure_rate` | decimal | percent, 0 to 100 | 100.0% | 11.7 to 47 | 100 minus min_survivability_score: the highest projected two-year failure rate in the neighborhood, 0 to 100. |
| `employment_rate` | decimal | fraction of 1 | 69.1% | 0.573 to 1.000 | Share of the labor force employed, as a fraction of 1 (0.95 means 95 percent). Census ACS. Census ACS columns are empty where the place has no ACS context in this release (see Coverage by metro). |
| `vacancy_rate` | decimal | fraction of 1 | 69.1% | 0.000 to 0.588 | Share of housing units vacant, as a fraction of 1. Census ACS. This is residential vacancy (housing units) from the ACS, not commercial or retail vacancy. |
| `competitor_count` | integer (written with .0) | count of businesses | 27.5% | 0 to 278 | Number of businesses of that exact type located INSIDE the neighborhood boundary, counted as distinct places. An empty cell means not measured, which is not the same as none: either the neighborhood has no boundary in our polygon set or that business type was never collected in that metro. A 0 means we measured and found none. |
| `income_rank` | empty | none (empty) | 0.0% | always empty | Not populated in this release (empty in every row). Use income_metro_percentile. |
| `income_score` | integer (written with .0) | US dollars | 34.6% | 14,222 to 250,001 | Median household income in dollars. Census ACS; 250,001 is the Census top code. Filled in 34.6% of rows, fewer than the other income columns (68.8%); prefer income_metro_percentile for comparisons. |
| `pct_high_income` | decimal | fraction of 1 | 68.8% | 0.000 to 1.000 | Share of households earning 150,000 dollars or more, as a fraction of 1. Census ACS. |
| `income_tier` | string | category | 68.8% | Affluent, Low-Income, Middle, Upper-Middle, Working | Affluent, Upper-Middle, Middle, Working or Low-Income, from income_metro_percentile. |
| `income_metro_percentile` | decimal | percentile, 0 to 100 | 68.8% | 0.5 to 100 | Where the neighborhood's median income sits among the metro's neighborhoods, 0 to 100 (100 = highest). |
| `income_national_percentile` | decimal | percentile, 0 to 100 | 68.8% | 0 to 100 | Where the neighborhood's median income sits among all U.S. neighborhoods in our data, 0 to 100. |
| `poverty_rate` | decimal | fraction of 1 | 68.8% | 0.006 to 0.665 | Share of residents below the poverty line, as a fraction of 1. Census ACS. |
| `median_age` | decimal | years | 68.9% | 18.8 to 69.2 | Median resident age, years. Census ACS. |
| `pct_bachelor_plus` | decimal | fraction of 1 | 68.8% | 0.000 to 0.968 | Share of adults 25 and over with a bachelor's degree or higher, as a fraction of 1. Census ACS. |
| `avg_household_size` | decimal | persons | 68.8% | 1.2 to 5.2 | Average household size, persons. Census ACS. |
| `median_commute_minutes` | decimal | minutes | 68.9% | 9.7 to 69.4 | Median commute, minutes. Census ACS. |
| `pct_housing_post_2000` | decimal | fraction of 1 | 68.8% | 0.000 to 0.941 | Share of housing units built in 2000 or later, as a fraction of 1. Census ACS. |
| `data_source` | string | label | 100.0% | StreetSpring Survivability Dataset 2026 | Provenance label for the row. |
| `p90_survivability_score` | decimal | percent, 0 to 100 | 100.0% | 55.7 to 90 | The chance, 0 to 100, that this business type lasts two or more years at a good address in the place: the 90th percentile of the scored locations inside it. The rank columns are ordered on this. |
| `neighborhood_name` | string | display name | 100.0% | 3,216 distinct, e.g. 441 Corridor | Display name of the place. |
| `scored_locations_in_neighborhood` | integer | count of scored locations | 100.0% | 1 to 24,674 | How many scored locations the place's scores are built from. A place needs at least 10 to be ranked. |
| `place_kind` | string | category | 100.0% | neighborhood, town | neighborhood (a city neighbourhood) or town (a whole municipality). Rankings never mix the two. |
| `neighborhood_overall_score` | decimal | percent, 0 to 100 | 69.0% | 73.4 to 82.5 | Mean p90_survivability_score across the place's ranked business types, 0 to 100; the basis of the overall rank. |
| `neighborhood_overall_avg_score` | decimal | percent, 0 to 100 | 69.0% | 71.9 to 81.8 | Mean avg_survivability_score across the same rows. |
| `neighborhood_overall_max_score` | decimal | percent, 0 to 100 | 69.0% | 74.3 to 83.8 | Mean max_survivability_score across the same rows. |
| `neighborhood_overall_min_score` | decimal | percent, 0 to 100 | 69.0% | 66.9 to 80.4 | Mean min_survivability_score across the same rows. |
| `business_types_ranked_in_neighborhood` | integer (written with .0) | count of business types | 69.0% | always 93 | How many business types are ranked in the place. |
| `neighborhood_overall_rank` | integer (written with .0) | rank, 1 = best | 69.0% | 1 to 188 | The place's rank among places of the same kind on neighborhood_overall_score (1 = best), repeated on every row of the place. Empty when the place is not ranked. |
| `total_ranked_neighborhoods_overall` | integer (written with .0) | count of places | 69.0% | 5 to 188 | How many places of that kind carry an overall rank in the metro. |

## Data dictionary: national file

27 columns, 2,595 rows.

| Column | Stored as | Unit | Filled | Observed values | Meaning |
|---|---|---|---|---|---|
| `city` | string | slug | 100.0% | 24 distinct, e.g. atlanta | Metro slug (24 metros). |
| `business_subtype` | string | slug | 100.0% | 110 distinct, e.g. acupuncture-clinic | Business type slug; the file's own count is in the README and on its page. |
| `city_rank_for_business_subtype` | integer | rank, 1 = best | 100.0% | 1 to 24 | Rank of this metro among the ranked metros for this business type (1 = best). |
| `tier_of_city_for_business_subtype` | string | category | 100.0% | Average, Below-average, Good, Great, Poor | Great, Good, Average, Below-average or Poor, from city_rank_for_business_subtype: the top 10 percent of ranked metros is Great, the next 20 percent Good, the middle 40 percent Average, the next 20 percent Below-average and the bottom 10 percent Poor. |
| `total_ranked_cities_for_business_subtype` | integer | count of metros | 100.0% | 15 to 24 | How many metros are ranked for this business type: the length of the list the city rank sits in. Observed values: 24, 15. It is 15 for the 5 business types the national file carries in only 15 metros (see Known limits), although every metro file carries them. |
| `avg_survivability_score_for_business_subtype_in_city` | decimal | percent, 0 to 100 | 100.0% | 64.3 to 84.8 | The projected chance, 0 to 100, that this business type lasts two or more years at a typical address in this metro. The ranking uses the good-address score, p90_survivability_score_for_business_subtype_in_city. |
| `max_survivability_score_for_business_subtype_in_city` | decimal | percent, 0 to 100 | 100.0% | 75 to 90 | Best scored location in the metro for this business type, 0 to 100. |
| `min_survivability_score_for_business_subtype_in_city` | decimal | percent, 0 to 100 | 100.0% | 53 to 76 | Weakest scored location in the metro for this business type, 0 to 100. |
| `UPPER_BOUND_MAX_SURVIVABILITY` | decimal | percent, 0 to 100 | 100.0% | 75.4 to 90 | Upper confidence bound on the max score, 0 to 100. |
| `LOWER_BOUND_MAX_SURVIVABILITY` | decimal | percent, 0 to 100 | 100.0% | 73.6 to 90 | Lower confidence bound on the max score, 0 to 100. |
| `UPPER_BOUND_MIN_SURVIVABILITY` | decimal | percent, 0 to 100 | 100.0% | 54.3 to 77.3 | Upper confidence bound on the min score, 0 to 100. |
| `LOWER_BOUND_MIN_SURVIVABILITY` | decimal | percent, 0 to 100 | 100.0% | 52.5 to 75.6 | Lower confidence bound on the min score, 0 to 100. |
| `year` | integer | calendar year | 100.0% | always 2026 | Data year (2026). |
| `source_url_city_rank` | string | URL path on streetspring.com | 100.0% | 110 distinct, e.g. /resources/guides/national/city-survivability/acupuncture-clinic | Page on streetspring.com that ranks the metros for this business type. |
| `source_url_business_subtype_rank` | string | URL path on streetspring.com | 100.0% | 110 distinct, e.g. /resources/guides/national/neighborhood-survivability/acupuncture-clinic | Page on streetspring.com that ranks neighborhoods nationally for this business type. |
| `methodology_url` | string | absolute URL | 100.0% | https://streetspring.com/resources/tutorial/aeo-methodology | How the score is built: https://streetspring.com/resources/tutorial/aeo-methodology |
| `source_article_url` | string | URL path on streetspring.com | 100.0% | 110 distinct, e.g. /resources/guides/national/city-survivability/acupuncture-clinic | The national rankings page for this business type. |
| `min_2yr_failure_rate` | decimal | percent, 0 to 100 | 100.0% | 10 to 25 | 100 minus the max score: the lowest projected two-year failure rate in the metro, 0 to 100. |
| `max_2yr_failure_rate` | decimal | percent, 0 to 100 | 100.0% | 24 to 47 | 100 minus the min score: the highest projected two-year failure rate in the metro, 0 to 100. |
| `data_source` | string | label | 100.0% | StreetSpring Survivability Dataset 2026 | Provenance label for the row. |
| `p90_survivability_score_for_business_subtype_in_city` | decimal | percent, 0 to 100 | 100.0% | 67.9 to 89.2 | The chance, 0 to 100, that this business type lasts two or more years at a good address in the metro (90th percentile of its scored locations). city_rank_for_business_subtype is ordered on this. |
| `scored_locations_in_city` | integer | count of scored locations | 100.0% | 829 to 55,914 | How many distinct scored locations the metro's figures are built from. A location is scored once per price point; it is counted once. |
| `city_overall_score` | decimal | percent, 0 to 100 | 100.0% | 79.1 to 82.5 | The metro's overall score, 0 to 100: the mean p90_survivability_score_for_business_subtype_in_city (a good address) across the business types every metro carries. The basis of city_overall_rank; repeated on every row of the metro. |
| `city_overall_avg_score` | decimal | percent, 0 to 100 | 100.0% | 76.3 to 78.2 | The mean avg_survivability_score_for_business_subtype_in_city (a typical address) across the same business types, 0 to 100. |
| `city_overall_rank` | integer | rank, 1 = best | 100.0% | 1 to 24 | The metro's rank among the metros on city_overall_score (1 = best); ties break on city_overall_avg_score, then scored locations. Repeated on every row of the metro. |
| `total_ranked_cities_overall` | integer | count of metros | 100.0% | always 24 | How many metros carry an overall rank. |
| `business_types_in_city_overall` | integer | count of business types | 100.0% | always 105 | How many business types the overall score averages over: the types every metro carries with a good-address score, a typical-address score and a scored-location count. |

## Coverage by metro

Share of each metro file's rows that carry the Census ACS context columns (income, poverty, education, age, household size, commute, housing age) and `competitor_count`. An empty `competitor_count` means not measured, not zero.

| Metro | Rows | ACS context filled | income_score filled | competitor_count filled |
|---|---|---|---|---|
| atlanta | 29,260 | 33.5% | 8.6% | 7.5% |
| baltimore | 30,360 | 33.3% | 6.6% | 4.3% |
| boston | 14,300 | 99.2% | 60.8% | 52.5% |
| charlotte | 10,560 | 100.0% | 32.8% | 23.4% |
| chicago | 18,260 | 88.0% | 49.4% | 36.8% |
| dallas | 8,690 | 98.7% | 29.1% | 24.3% |
| denver | 23,760 | 45.8% | 0.9% | 0.7% |
| detroit | 25,960 | 40.7% | 15.3% | 10.4% |
| houston | 9,460 | 100.0% | 62.8% | 55.9% |
| los-angeles | 26,180 | 65.1% | 50.0% | 41.6% |
| miami | 18,810 | 72.5% | 33.9% | 25.2% |
| minneapolis | 11,000 | 100.0% | 49.0% | 40.5% |
| new-york-city | 21,010 | 100.0% | 85.9% | 71.0% |
| orlando | 8,030 | 98.6% | 37.9% | 30.5% |
| philadelphia | 15,840 | 100.0% | 77.8% | 66.4% |
| phoenix | 10,560 | 84.4% | 10.4% | 6.6% |
| portland | 8,580 | 93.6% | 40.4% | 28.1% |
| san-antonio | 6,050 | 96.4% | 15.6% | 7.4% |
| san-diego | 14,190 | 52.7% | 24.4% | 18.2% |
| san-francisco | 23,760 | 47.2% | 30.1% | 21.3% |
| seattle | 14,630 | 75.2% | 46.6% | 33.9% |
| st-louis | 16,500 | 48.0% | 19.7% | 15.8% |
| tampa-bay | 3,850 | 100.0% | 68.2% | 44.2% |
| washington-dc | 16,940 | 89.6% | 48.7% | 43.1% |
| **All** | 386,540 | 68.8% | 34.6% | 27.5% |

## Known limits

1. **One model version.** Every file comes from model `v1-2026-03-12`. The two-year framing rests on sector-level two-year survival benchmarks from the U.S. Bureau of Labor Statistics that anchor the published percentage, not on StreetSpring's own two-year follow-up of each business (see the methodology's limitations section). The percentages are projections anchored to those benchmarks and are most reliable for comparing places and business types with each other.
2. **San Antonio ranks nothing.** The file carries scores in all 6,050 rows and 0 ranked rows, for the reason its header gives (quoted under Ranks and tiers).
3. **Dallas Census inputs, rescore pending.** A defect in the Census inputs behind the Dallas scores was found on 2026-09-25: most of the Census context measured around Dallas scored locations had been stored as zero (these are model inputs, separate from the ACS columns in the file). The inputs have been repaired, but the Dallas scores in this release were computed from the defective inputs and will be replaced when Dallas is rescored. Until then treat differences between Dallas's 79 places as understated.
4. **Pacific Palisades uses pre-fire inputs.** Pacific Palisades is number 1 of 174 ranked Los Angeles neighborhoods overall (overall score 82.3, from 639 scored locations). Its location inputs predate the January 2025 Palisades Fire, so its scores describe the neighborhood as it was before the fire.
5. **National file gaps.** The national file has no row for 5 business types (grocery-store, modern-european-restaurant, moroccan-restaurant, south-african-restaurant, south-american-restaurant) in 9 metros (atlanta, baltimore, charlotte, orlando, portland, san-antonio, san-diego, st-louis, tampa-bay), 45 metro and business type pairs in all, although each of those metro files carries and scores these types. For those types the national ranking covers 15 metros.
6. **Sparse context columns.** The ACS context columns are filled in 68.8% of metro rows and `income_score` in 34.6%; `income_rank` is empty in every row. `income_score` is top-coded by the Census at 250,001 dollars (655 rows). `competitor_count` is filled in 27.5% of rows. See Coverage by metro.
7. **`vacancy_rate` is housing vacancy.** It is the share of housing units vacant (ACS), not commercial, office or retail vacancy.
8. **Small places are noisy.** Scores for a place are built from the scored locations inside it; 1,076 places have fewer than 10 and are never ranked. Use `scored_locations_in_neighborhood` to weight or filter.
9. **Ranges grow with list length.** The spread between the best and worst place for a business type grows with the number of places in the metro. Compare metros on a spread that does not depend on the count, such as the gap between the 10th and 90th percentile places.
10. **Price points are averaged.** Each row averages the price points the tool scores separately. Boundaries are Zillow neighborhoods and U.S. Census places, which may not match a city's own definitions.

## Versions and update policy

- A version is dated (`YYYY.MM.DD`, with a `.n` suffix for a same-day correction). Every file header carries a `# Version:` line and a `# Updated:` date. This document describes version 2026.09.25.1.
- A new version is released whenever any score, place, file header, README or page sentence changes. Score changes are called out explicitly in CHANGELOG.md; documentation-only releases do not change any number. Versions to date: 2026.09.25.1, 2026.09.25, 2026.09.13, 2026.09.03, 2026.04.
- Corrections are made in place and logged in the changelog: https://streetspring.com/resources/data/CHANGELOG.md
- Every version is archived on Zenodo under the concept DOI 10.5281/zenodo.22287996, which resolves to the newest version; each version also has its own DOI.
- File names keep the `-2026` suffix across versions within the year, so links do not break; use the `# Version:` line to tell versions apart.

## License and citation

Creative Commons Attribution 4.0 International (CC BY 4.0): https://creativecommons.org/licenses/by/4.0/. Use, share and adapt the data for any purpose, including commercially, with attribution.

> StreetSpring (2026). StreetSpring Survivability Datasets 2026, version 2026.09.25.1. https://streetspring.com/resources/datasets. DOI 10.5281/zenodo.22964004 (this version); DOI 10.5281/zenodo.22287996 (all versions). CC BY 4.0.

For a single figure, name the business type, the place and the year.

## Contact

info@streetspring.com. Questions about the method, corrections and custom cuts by city or business type.
