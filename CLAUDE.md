# Dremio Workshop — Claude Code Context

## Credentials — Critical Rule

**Never ask the user to paste a PAT, token, password, or any credential into this chat.**

If no Dremio profile exists, generate the following command and instruct the user to run it themselves in their terminal:

```bash
dremio profile create default \
  --type cloud \
  --base-url https://api.dremio.cloud \
  --project-id YOUR_PROJECT_ID \
  --auth-type pat \
  --token YOUR_PAT_HERE
```

Tell them to replace `YOUR_PROJECT_ID` (found in Dremio Cloud → Settings → Project) and `YOUR_PAT_HERE` with their actual values. Once they say "done", verify the connection by running `dremio space list`.

## What You Can Do

You have the Dremio CLI installed (`dremio`). Use it to interact with Dremio Cloud on my behalf.
Always run commands via your bash tool. Format results clearly.

## Core Commands

```bash
# Run SQL
dremio sql execute "YOUR SQL HERE"

# Explore catalog
dremio space list
dremio folder list --parent "space_name"

# Create spaces and folders
dremio space create --name "space_name"
dremio folder create --path '["space_name", "folder_name"]'

# Create Iceberg tables
dremio sql execute "CREATE TABLE path.to.table (col1 TYPE, col2 TYPE) STORE AS (type => 'iceberg')"

# Insert data
dremio sql execute "INSERT INTO path.to.table VALUES (...)"

# Describe a table or view
dremio sql execute "SELECT column_name, data_type FROM INFORMATION_SCHEMA.\"COLUMNS\" WHERE table_schema = 'schema' AND table_name = 'table'"

# Sample data
dremio sql execute "SELECT * FROM path.to.table LIMIT 10"

# Lineage — requires catalog ID, not path
dremio catalog get-by-path "Space/Folder/ViewName"   # returns the catalog ID
dremio lineage show <catalog-id> --format tree

# Reflections
dremio reflection create --json '{"datasetId": "<id>", "name": "...", "type": "AGGREGATION", "dimensionFields": [...], "measureFields": [...], "enabled": true}'
dremio reflection get <reflection-id>

# Wikis
dremio wiki update "space.folder.view_name" "markdown content here"

# Tags
dremio tag update "space.folder.view_name" "tag1,tag2,tag3"
```

## Workshop Data Model

We are building a Strategic Supply Chain Intelligence platform for critical minerals.
All data lives in `StrategicMaterialsDB`:

### Bronze Layer — Raw Iceberg Tables

| Table | Rows | Key Columns |
|-------|------|-------------|
| `Bronze.MineralSources` | 50 | ShipmentID, SupplierName, CountryOfOrigin, MineralType, WeightTons, ShipmentDate |
| `Bronze.GeopoliticalRiskIndex` | 10 | Country, RiskScore (1-10), RiskCategory, Notes |
| `Bronze.SupplierAuditFindings` | 50 | AuditID, SupplierName, AuditDate, AuditType, ComplianceScore, CriticalFindingsCount, PrimaryFindingCategory, Auditor, RemediationDueDate, Status |

### Silver Layer — Enriched Views

| View | Description |
|------|-------------|
| `Silver.ShipmentRisk` | MineralSources joined to GeopoliticalRiskIndex, adds WeightedRiskExposure |

### Gold Layer — Business Intelligence Views

| View | Description |
|------|-------------|
| `Gold.VulnerabilityDashboard` | By mineral type: total volume, avg risk, high-risk volume, high-risk dependence % |
| `Gold.SupplierRiskSummary` | By supplier: shipment volume, risk score, avg compliance score, total critical findings |

### Catalog IDs (for lineage and reflections)

| Object | Catalog ID |
|--------|-----------|
| Gold.VulnerabilityDashboard | 65a450aa-a94e-4999-a4c6-1dd587cc43b7 |
| Silver.ShipmentRisk | dc5fe946-6f02-471d-ae8c-1192fa2ad1d5 |

## Workshop Namespace

```
StrategicMaterialsDB/
├── Bronze/
│   ├── MineralSources          ← Iceberg table (50 shipment records)
│   ├── GeopoliticalRiskIndex   ← Iceberg table (10 countries, risk scores 1-10)
│   └── SupplierAuditFindings   ← Iceberg table (50 audit records)
├── Silver/
│   └── ShipmentRisk            ← View: shipments + risk scores + weighted exposure
└── Gold/
    ├── VulnerabilityDashboard  ← View: mineral risk dependence by type
    └── SupplierRiskSummary     ← View: supplier risk + compliance combined
```

## Business Context

A global manufacturer sources 6 critical minerals — Lithium, Cobalt, Neodymium,
Palladium, Nickel, and Niobium — from 8 suppliers across 8 countries.

Key business questions we are answering:
- Which minerals have the highest dependence on high-risk countries?
- Which suppliers combine high geopolitical risk with poor audit compliance?
- What is our total exposure if a specific country restricts exports?
- Which audit findings are overdue and from the highest-risk suppliers?

## Suppliers and Risk Profiles

| Supplier | Country | Risk Score |
|----------|---------|------------|
| Pacific Rim Mining | Australia | 1 — Low |
| Northern Star Resources | Canada | 1 — Low |
| USA Rare Earth LLC | USA | 1 — Low |
| Chilean Copper Corp | Chile | 3 — Low-Medium |
| Kazakhstan Metals | Kazakhstan | 6 — Medium |
| Sino Rare Metals | China | 7 — High |
| Congo Mineral Group | DRC | 8 — High |
| Ural Mining Co | Russia | 10 — Critical |

## SQL Style Notes

- Always use `CREATE OR REPLACE VIEW` for view creation
- Always use `STORE AS (type => 'iceberg')` when creating tables
- Reference workshop tables as: `StrategicMaterialsDB.Bronze.TableName`
- Reference workshop views as: `StrategicMaterialsDB.Silver.ViewName` or `StrategicMaterialsDB.Gold.ViewName`
- Use `LIMIT` on exploratory queries for speed
- For lineage, always look up the catalog ID first with `dremio catalog get-by-path`

## Important

- Always show SQL before executing so the user can review it.
- After creating each object, confirm it worked with a quick SELECT LIMIT 3.
- Credentials are handled by the Dremio CLI profile — you do not need a PAT or project ID from the user.
- When creating reflections, do NOT include displayFields for AGGREGATION type — use only dimensionFields and measureFields.
