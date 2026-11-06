# Data sources

## Statistics Norway table 14944: Obstacles to enterprise AI use by industry and size (2026)

- Landing page: https://www.ssb.no/en/statbank/table/14944
- Licence: **CC-BY-4.0**
- Attribution: Statistics Norway table 14944: Obstacles to enterprise AI use by industry and size (2026), https://www.ssb.no/en/statbank/table/14944 (licence: CC-BY-4.0)
- Download method: `{'kind': 'http_csv', 'url': 'https://data.ssb.no/api/pxwebapi/v2/tables/14944/data?lang=en&outputFormat=csv&outputFormatParams=UseTexts&valueCodes[NACE2025]=*&valueCodes[SyssGrpIKT]=*&valueCodes[ContentsCode]=*&valueCodes[Tid]=*&stub=NACE2025,SyssGrpIKT,ContentsCode&heading=Tid', 'max_mb': 300}`
- Inspected by the factory on 2026-11-05 (sha256 of the inspected sample `9d346f12d314db03…`)

| Column | Validated type |
|---|---|
| `industry (SIC2025)` | string |
| `employed persons` | string |
| `contents` | string |
| `2026` | integer |

### Limitations

- Automated inspection: Snapshot inspected 2026-11-05; the source may change after this date.
- Single year (2026); value column is named '2026'; cross-sectional comparisons only.
- Survey of enterprises with 10+ employees; percentages as defined by SSB. The 'Has considered to use AI' row is a share, not an obstacle.

## Statistics Norway table 14365: Use of generative AI by sex and age (2024-2026)

- Landing page: https://www.ssb.no/en/statbank/table/14365
- Licence: **CC-BY-4.0**
- Attribution: Statistics Norway table 14365: Use of generative AI by sex and age (2024-2026), https://www.ssb.no/en/statbank/table/14365 (licence: CC-BY-4.0)
- Download method: `{'kind': 'http_csv', 'url': 'https://data.ssb.no/api/pxwebapi/v2/tables/14365/data?lang=en&outputFormat=csv&outputFormatParams=UseTexts&valueCodes[Kjonn]=*&valueCodes[Alder]=16-24,25-34,35-44,45-54,55-64,16-74&valueCodes[ContentsCode]=BruktKIprivat,BruktKIjobb&valueCodes[Tid]=*&stub=Tid,Kjonn,Alder&heading=ContentsCode', 'max_mb': 300}`
- Inspected by the factory on 2026-11-05 (sha256 of the inspected sample `9aac73c429ac5c51…`)

| Column | Validated type |
|---|---|
| `year` | integer |
| `sex` | string |
| `age` | string |
| `Used generative AI tools for private purposes` | float |
| `Used generative AI tools for professional (work) purposes` | float |

### Limitations

- Automated inspection: Snapshot inspected 2026-11-05; the source may change after this date.
- Restricted to working-age groups and two measures (private, work use) to avoid SSB missing values ('..') and commas in column names; population survey, not employees of one company.
- Only 3 years (2024-2026): describe trends, do not forecast.

## Alternatives considered and rejected

- **Customer Support Tickets**: Dataset card describes generated (synthetic) customer-support emails; licence is CC-BY-NC-4.0; no timestamps. Real PII-redacted IT tickets (zenodo-7648117) preferred.
- **Console-AI/IT-helpdesk-synthetic-tickets**: Third-party SYNTHETIC tickets (per dataset name) with generated requester email addresses; only 500 rows; real IT ticket data is available.
- **robloxguard200/IBM_hr_employee_attrition_predicted_model_dataset**: Prompt/input/output text rows derived from an HR attrition table, not tabular data; only 25 rows.
- **Semantic Similarity of IT Support Tickets**: Contains only id,x,y numeric coordinates; no ticket text or attributes usable by agent tools.
- **IT HelpDesk Dataset**: Only file is cleaned_data.xlsx; XLSX is not supported by the factory parser or the app template's CSV/parquet loader.
