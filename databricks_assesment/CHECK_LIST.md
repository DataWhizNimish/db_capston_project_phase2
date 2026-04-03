# ✅ Deployment Checklist

Follow this checklist to deploy your retail pipeline with CI/CD.

## 📌 Pre-Deployment

- [ ] Databricks CLI installed: `pip install databricks-cli`
- [ ] Git installed and configured
- [ ] GitHub account created
- [ ] Databricks workspace access: https://dbc-9bb91c66-a0e1.cloud.databricks.com

---

## 1️⃣ Create GitHub Repository

- [ ] Go to https://github.com/new
- [ ] Repository name: `databricks-retail-pipeline`
- [ ] Visibility: Private (recommended)
- [ ] ❌ DO NOT initialize with README
- [ ] Click "Create repository"
- [ ] Copy repository URL (e.g., `https://github.com/YOUR_USERNAME/databricks-retail-pipeline.git`)

---

## 2️⃣ Download Workspace Folder Locally

```bash
# Create local directory
mkdir -p ~/databricks-retail-pipeline
cd ~/databricks-retail-pipeline

# Authenticate with Databricks
databricks auth login --host https://dbc-9bb91c66-a0e1.cloud.databricks.com

# Export workspace folder
databricks workspace export-dir \
  /Users/singhsir789@gmail.com/DB_Phase2_Assesment/databricks_assesment \
  . --profile DEFAULT
```

**Verify files downloaded:**
- [ ] `databricks.yml`
- [ ] `resources/retail_pipeline.job.yml`
- [ ] `.github/workflows/deploy.yml`
- [ ] `README.md`
- [ ] `DEPLOYMENT_GUIDE.md`
- [ ] `CHECKLIST.md` (this file)

---

## 3️⃣ Initialize Git & Push to GitHub

```bash
cd ~/databricks-retail-pipeline

# Initialize git
git init

# Add all files
git add .

# Initial commit
git commit -m "Initial commit: Retail pipeline with CI/CD"

# Add remote (replace YOUR_USERNAME)
git remote add origin https://github.com/YOUR_USERNAME/databricks-retail-pipeline.git

# Create and push main branch
git branch -M main
git push -u origin main
```

- [ ] Files pushed to GitHub
- [ ] Verify on GitHub.com that files are visible

---

## 4️⃣ Create Additional Branches

```bash
# Create develop branch
git checkout -b develop
git push -u origin develop

# Create uat branch
git checkout -b uat
git push -u origin uat

# Return to main
git checkout main
```

- [ ] `main` branch created
- [ ] `develop` branch created
- [ ] `uat` branch created
- [ ] All branches visible on GitHub

---

## 5️⃣ Generate Databricks Access Token

1. [ ] Open Databricks: https://dbc-9bb91c66-a0e1.cloud.databricks.com
2. [ ] Click profile icon → **Settings**
3. [ ] **Developer** → **Access tokens**
4. [ ] Click **Generate new token**
5. [ ] Comment: "GitHub Actions CI/CD"
6. [ ] Lifetime: 90 days
7. [ ] Click **Generate**
8. [ ] **COPY TOKEN IMMEDIATELY** (shown only once)

---

## 6️⃣ Configure GitHub Secrets

1. [ ] Go to: `https://github.com/YOUR_USERNAME/databricks-retail-pipeline`
2. [ ] Click **Settings** tab
3. [ ] Left sidebar → **Secrets and variables** → **Actions**
4. [ ] Click **New repository secret**

**Add Secret 1:**
- [ ] Name: `DATABRICKS_HOST`
- [ ] Value: `https://dbc-9bb91c66-a0e1.cloud.databricks.com`
- [ ] Click **Add secret**

**Add Secret 2:**
- [ ] Name: `DATABRICKS_TOKEN`
- [ ] Value: `<paste-your-token>`
- [ ] Click **Add secret**

**Verify:**
- [ ] Both secrets show up in the list

---

## 7️⃣ Test Local Deployment

```bash
cd ~/databricks-retail-pipeline

# Validate configuration
databricks bundle validate -t dev

# Deploy to dev
databricks bundle deploy -t dev

# Run the pipeline
databricks bundle run retail_data_pipeline -t dev
```

- [ ] Validation successful
- [ ] Deployment successful
- [ ] Job running in Databricks
- [ ] Check Databricks UI: **Workflows** → **Jobs** → "Retail Data Pipeline - dev"

---

## 8️⃣ Test CI/CD Pipeline

```bash
# Switch to develop
git checkout develop

# Make a test change
echo "\n## CI/CD Test - $(date)" >> README.md

# Commit and push
git add README.md
git commit -m "Test: Trigger CI/CD"
git push origin develop
```

- [ ] Pushed to GitHub
- [ ] Go to GitHub → **Actions** tab
- [ ] See workflow "Deploy Retail Pipeline to Databricks" running
- [ ] All steps complete with green checkmarks
- [ ] Job appears in Databricks UI

---

## 9️⃣ Verify Data Pipeline

Run these queries in Databricks SQL Editor:

```sql
-- Bronze layer
SELECT COUNT(*) FROM 1_dev.bronze.customers;  -- Should show 50,000
SELECT COUNT(*) FROM 1_dev.bronze.orders;     -- Should show 100,000

-- Silver layer
SELECT COUNT(*) FROM 1_dev.silver.customers;
SELECT COUNT(*) FROM 1_dev.silver.order_items;

-- Gold layer
SELECT COUNT(*) FROM 1_dev.gold.fact_sales;
SELECT * FROM 1_dev.gold.agg_revenue_by_state ORDER BY total_revenue DESC LIMIT 5;
```

- [ ] Bronze tables populated
- [ ] Silver tables populated
- [ ] Gold tables populated
- [ ] Aggregations look correct

---

## 🔟 Optional: Promote to UAT & Production

### Promote Dev → UAT

```bash
git checkout uat
git merge develop
git push origin uat
```

- [ ] UAT deployment triggered
- [ ] Job created in Databricks: "Retail Data Pipeline - uat"
- [ ] Tables created in `1_uat` catalog

### Promote UAT → Production

**⚠️ Important:** Ensure production data is ready first!

```bash
git checkout main
git merge uat
git push origin main
```

- [ ] Production deployment triggered
- [ ] Job created in Databricks: "Retail Data Pipeline - prod"
- [ ] Tables exist in `1_prod` catalog
- [ ] Manually trigger prod job in Databricks UI

---

## 🎉 Success Criteria

- ✅ GitHub repository created and all files pushed
- ✅ Three branches (main, develop, uat) created
- ✅ GitHub secrets configured
- ✅ Local deployment successful
- ✅ CI/CD pipeline running on push
- ✅ All tables populated in dev environment
- ✅ Job orchestration working (Bronze → Silver → Gold)
- ✅ Optimizations applied (OPTIMIZE, Z-ORDER)

---

## 📝 Notes & Commands Reference

### Useful Git Commands

```bash
# Check current branch
git branch

# Switch branch
git checkout <branch-name>

# View commit history
git log --oneline

# Check remote
git remote -v
```

### Useful Databricks CLI Commands

```bash
# Validate bundle
databricks bundle validate -t <env>

# Deploy bundle
databricks bundle deploy -t <env>

# Run job
databricks bundle run <job-name> -t <env>

# List resources
databricks bundle resources list -t <env>

# Destroy deployment
databricks bundle destroy -t <env>
```

### Troubleshooting

If something fails:
1. Check GitHub Actions logs for error details
2. Verify secrets are correctly configured
3. Test local deployment first
4. Review `DEPLOYMENT_GUIDE.md` for detailed troubleshooting

---

## 🚀 You're All Set!

Your retail pipeline is now fully deployed with CI/CD automation. Any push to `develop`, `uat`, or `main` branches will automatically deploy to the respective environment.

**Next Steps:**
- Monitor pipeline runs in Databricks UI
- Create dashboards on gold tables
- Set up data quality alerts
- Optimize schedule based on business needs

For detailed information, see: `DEPLOYMENT_GUIDE.md`
