# Retail Data Pipeline - Databricks Asset Bundle

## 📊 Project Overview

This is a production-grade data engineering pipeline built on Databricks using the Medallion Architecture (Bronze → Silver → Gold) for a retail analytics use case.

**Key Features:**
- ✅ Multi-environment deployment (dev, uat, prod)
- ✅ Automated CI/CD with GitHub Actions
- ✅ Serverless compute for cost optimization
- ✅ Delta Lake optimizations (OPTIMIZE, Z-ORDER, VACUUM)
- ✅ Spark optimizations (broadcast joins, partitioning)
- ✅ Incremental data processing with deduplication
- ✅ Self-contained notebooks in bundle

## 🏛️ Architecture

### Medallion Layers

```
Bronze Layer (Raw)
    ↓
Silver Layer (Cleaned & Transformed)
    ↓
Gold Layer (Analytics-Ready)
```

**Bronze Layer:**
- Raw CSV ingestion from volumes
- Append-only Delta tables
- Ingestion metadata tracking

**Silver Layer:**
- Deduplication by primary keys
- Data quality checks
- Business transformations
- Incremental processing

**Gold Layer:**
- Fact & dimension tables
- Pre-aggregated metrics
- Optimized for BI/analytics
- Delta optimizations applied

### Catalogs & Schemas

| Environment | Catalog   | Schemas                 |
|-------------|-----------|-------------------------|
| Development | `1_dev`   | bronze, silver, gold    |
| UAT/QA      | `1_uat`   | bronze, silver, gold    |
| Production  | `1_prod`  | bronze, silver, gold    |

## 📁 Repository Structure

```
databricks_assesment/
├── databricks.yml                    # Bundle configuration
├── .github/
│   └── workflows/
│       └── deploy.yml               # CI/CD workflow
├── src/                             # Pipeline notebooks
│   ├── Bronze                       # Bronze layer ingestion
│   ├── Silver                       # Silver layer transformation
│   └── Gold_Optimization            # Gold layer with optimizations
├── resources/
│   └── medallion.job.yml        # Medallion pipeline job definition
├── CHECKLIST.md                     # Step-by-step deployment checklist
├── DEPLOYMENT_GUIDE.md              # Comprehensive deployment guide
├── README.md
└── .gitignore
```

**All notebooks are self-contained within the bundle's src/ directory!**

## 🚀 Quick Start

### Prerequisites

1. Databricks CLI installed: `pip install databricks-cli`
2. GitHub repository with this code
3. GitHub secrets configured (see DEPLOYMENT_GUIDE.md)

### Local Deployment

```bash
# Authenticate with Databricks
databricks auth login --host https://dbc-9bb91c66-a0e1.cloud.databricks.com

# Navigate to bundle directory
cd databricks_assesment

# Validate configuration
databricks bundle validate -t dev

# Deploy to dev
databricks bundle deploy -t dev

# Run the pipeline
databricks bundle run medallion_pipeline -t dev
```

### Deploy to Other Environments

```bash
# Deploy to UAT
databricks bundle deploy -t uat

# Deploy to Production
databricks bundle deploy -t prod
```

## 🔄 CI/CD with GitHub Actions

### Branch Strategy

| Branch    | Environment | Auto-Deploy | Auto-Run |
|-----------|-------------|-------------|----------|
| `develop` | dev         | ✅ Yes       | ✅ Yes    |
| `uat`     | uat         | ✅ Yes       | ✅ Yes    |
| `main`    | prod        | ✅ Yes       | ❌ No     |

### GitHub Secrets Required

Add these secrets in GitHub repository settings:
- **Settings** → **Secrets and variables** → **Actions**

```
DATABRICKS_HOST=https://dbc-9bb91c66-a0e1.cloud.databricks.com
DATABRICKS_TOKEN=<your-personal-access-token>
```

**To generate token:**
1. In Databricks workspace: User Settings → Developer → Access Tokens
2. Click "Generate new token"
3. Copy token and add to GitHub secrets

### Deployment Workflow

1. **Dev deployment:**
   ```bash
   git checkout develop
   git add .
   git commit -m "Updated transformation logic"
   git push origin develop
   ```
   → Automatically deploys to `1_dev` and runs pipeline

2. **UAT deployment:**
   ```bash
   git checkout uat
   git merge develop
   git push origin uat
   ```
   → Automatically deploys to `1_uat` and runs pipeline

3. **Prod deployment:**
   ```bash
   git checkout main
   git merge uat
   git push origin main
   ```
   → Automatically deploys to `1_prod` (manual run required)

## 📊 Pipeline Details

### Job: `medallion_pipeline`

**Tasks:**
1. **bronze_layer** - Raw data ingestion from volumes
2. **silver_layer** - Data cleaning & transformation (depends on bronze)
3. **gold_layer** - Analytics aggregations & optimizations (depends on silver)

**Schedule:** Daily at 2 AM Pacific (paused in dev/uat)

**Compute:** Serverless (auto-scaling, cost-optimized)

**Notifications:** Email alerts on success/failure

### Tables Created

**Bronze:**
- `customers`, `orders`, `order_items`, `products`

**Silver:**
- `customers`, `orders`, `order_items`, `products` (deduplicated & cleaned)

**Gold:**
- `dim_customers`, `dim_products` (dimensions)
- `fact_sales` (fact table)
- `agg_revenue_by_state`, `agg_top_products`, `agg_sales_trends` (aggregations)

## 🔧 Optimizations Applied

### Spark Optimizations
- ✅ Broadcast joins for small dimension tables
- ✅ Date-based partitioning (year, month)
- ✅ Serverless compute auto-scaling

### Delta Lake Optimizations
- ✅ OPTIMIZE for file compaction
- ✅ Z-ORDER on frequently filtered columns
- ✅ VACUUM for storage cleanup
- ✅ Schema evolution support

## 📚 Getting Started

**For complete step-by-step instructions, see:**
- [CHECKLIST.md](CHECKLIST.md) - Quick deployment checklist
- [DEPLOYMENT_GUIDE.md](DEPLOYMENT_GUIDE.md) - Comprehensive deployment guide

**Key steps:**
1. ✅ All notebooks are in src/ directory
2. ✅ Job configuration ready (medallion.job.yml)
3. ✅ CI/CD workflow configured
4. Next: Push to GitHub and configure secrets

## 📚 Additional Resources

- [Databricks Asset Bundles Documentation](https://docs.databricks.com/dev-tools/bundles/index.html)
- [Medallion Architecture Best Practices](https://www.databricks.com/glossary/medallion-architecture)
- [Delta Lake Optimization Guide](https://docs.databricks.com/delta/optimizations/index.html)

## ❓ Troubleshooting

**Bundle validation fails:**
- Check `databricks.yml` syntax
- Verify catalog names match your workspace
- Ensure notebook paths are correct (they should be `../src/[NotebookName]`)

**Deployment succeeds but job fails:**
- Check notebook parameters are passed correctly
- Verify catalog permissions
- Review job run logs in Databricks UI

**GitHub Actions fails:**
- Verify `DATABRICKS_HOST` and `DATABRICKS_TOKEN` secrets
- Check token has not expired
- Ensure proper permissions on workspace

## 👥 Contact

For questions or issues: singhsir789@gmail.com
