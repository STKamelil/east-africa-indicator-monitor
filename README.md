[README.md](https://github.com/user-attachments/files/33535445/README.md)
# east-afri# East Africa Resilience Monitor

A data pipeline and Power BI dashboard tracking development and vulnerability indicators across East Africa, built from UN and World Bank open data APIs.

**Status:** Phase 1 in progress (World Bank data extracted; dashboard in early build)

## Project goal

Answer one policy question: *Which East African countries face the highest compounded vulnerability, and how is it changing over time?*

## Scope

- **Countries (11):** Burundi (BDI), Djibouti (DJI), Eritrea (ERI), Ethiopia (ETH), Kenya (KEN), Rwanda (RWA), Somalia (SOM), South Sudan (SSD), Sudan (SDN), Tanzania (TZA), Uganda (UGA)
- **Period:** 2000-2024
- **Tools:** Python (requests, pandas) in Google Colab; Power BI

## Data sources

| Source | Access method | Status |
|---|---|---|
| World Bank Indicators API (v2) | REST API, JSON | Active |
| UNHCR Refugee Statistics API | REST API | Planned |
| UNICEF SDMX API | SDMX | Planned |
| UN SDG Global Database API | REST API | Planned |
| OCHA HDX HAPI | REST API | Planned |

## Data dictionary

Dataset: `ea_indicators.csv` (275 rows = 11 countries x 25 years)

| Column | Type | Description | Source code | Unit | Nature |
|---|---|---|---|---|---|
| `iso3` | Text | ISO 3166-1 alpha-3 country code | n/a | n/a | n/a |
| `country` | Text | Country name as given by the World Bank | n/a | n/a | n/a |
| `year` | Whole number | Calendar year of observation | n/a | Year | n/a |
| `population` | Whole number | Total population | SP.POP.TOTL | People | Estimate |
| `gdp_per_capita` | Decimal | GDP per capita, current US dollars | NY.GDP.PCAP.CD | Current US$ | Reported (national accounts) |
| `under5_mortality` | Decimal | Under-5 mortality rate | SH.DYN.MORT | Deaths per 1,000 live births | Modelled estimate (UN IGME) |

Note on current US$: values are not adjusted for inflation, so comparisons across years reflect price changes as well as real growth.

## Data quality log

| Date | Dataset | Finding | Decision |
|---|---|---|---|
| 2026-10-16 | `gdp_per_capita` | Eritrea has no data for 2012-2024 (13 years) | Left blank; not imputed |
| 2026-10-16 | `gdp_per_capita` | South Sudan has no data for 2000-2007 or 2016-2024 (17 years) | Left blank; not imputed |
| 2026-10-16 | `population`, `under5_mortality` | No missing values for any country or year | None needed |

**Open question:** The reasons for the South Sudan and Eritrea gaps have not been verified against World Bank metadata. Do not state a cause in published material until checked.

**Principle:** Missing values are never filled in or set to zero. Dashboards must display "no data" and show the latest available year for each country so that values from different years are not compared as if equal.

## Pipeline steps

1. Request each indicator for all 11 countries in a single API call (countries joined with `;`).
2. Flatten the JSON response with `pd.json_normalize`.
3. Keep and rename the needed columns; convert `year` to integer and values to numeric.
4. Sort by country and year; merge indicators on `iso3`, `country`, `year` (left join).
5. Check shape (expected 275 rows) and missing values.
6. Export to CSV and load into Power BI.

## Changelog

- **2026-10-16:** Extracted population, GDP per capita, and under-5 mortality. Created first CSV export and this README.
ca-indicator-monitor
