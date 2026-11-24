# 🚀 SpringBoot CI/CD Pipeline

**Auto-triggered quality gates for Spring Boot applications**

---

## 📋 What This Does

```
GitHub Push/PR/Merge → 🔔 Auto Trigger → 7 Quality Gates → ✅ Ready to Deploy
```

**Pipeline automatically runs:**
- ✅ Code quality checks (Lint)
- ✅ Security scans (Secret Scan, Coverity, Black Duck)
- ✅ Build & test your Spring Boot app
- ✅ Creates deployable JAR artifact

**Runtime:** ~15 minutes | **Platform:** Harness Cloud

---

## 🎯 7 Pipeline Steps

| Step | What It Does | Time |
|------|-------------|------|
| 1️⃣ **Lint** | Code quality check | 30s |
| 2️⃣ **Secret Scan** | Detects exposed credentials | 45s |
| 3️⃣ **Dependencies** | Downloads from JFrog | 2m |
| 4️⃣ **Build** | Creates JAR file | 1m |
| 5️⃣ **Unit Tests** | Runs JUnit tests | 45s |
| 6️⃣ **Coverity SAST** | Security vulnerability scan | 5m |
| 7️⃣ **Black Duck SCA** | Dependency vulnerability scan | 5m |

---

## 🔀 Branch Configuration

### **Current Setup:**
```yaml
build: <+input>.default(<+trigger.branch>)
```

### **What This Means:**

| Scenario | What Happens |
|----------|-------------|
| **Push to GitHub** | Automatically uses pushed branch ✅ |
| **Create PR** | Automatically uses PR source branch ✅ |
| **Manual Run** | Popup lets you choose branch ✅ |

### **Why This is Best:**

```diff
- <+input>                              ❌ Always pauses, waits for input
- <+trigger.branch>                     ⚠️  Auto-only, no manual override
+ <+input>.default(<+trigger.branch>)  ✅ Smart defaults with manual override option!
```

**90% automatic, 10% manual flexibility when needed!**

---

## 🚀 Quick Setup (4 Steps)

### **1. Create GitHub Connectors**

In Harness → Connectors → New Connector:

```yaml
# Connector 1: App Repository
Name: github_app_connector
URL: https://github.aepsc.com
Auth: Personal Access Token

# Connector 2: DevOps Scripts
Name: github_devops_connector
URL: https://github.aepsc.com
Auth: Personal Access Token
```

---

### **2. Add Secrets**

In Harness → Secrets:

| Secret Name | Description |
|-------------|-------------|
| `github_app_token` | App repo access token |
| `github_devops_token` | DevOps repo access token |
| `jfrog_username` | JFrog username |
| `jfrog_password` | JFrog API token |

---

### **3. Import Pipeline**

```
Harness → Pipelines → Create Pipeline → Import from YAML
Select: app-build-verify.yaml
Click: Save
```

**✅ Pipeline + Trigger both imported automatically!**

---

### **4. Configure Webhook**

In GitHub Repo → Settings → Webhooks:

```
Payload URL: [Copy from Harness trigger]
Content type: application/json
Events: ☑ Pushes, ☑ Pull requests
Active: ☑
```

---

## 🧪 Test Your Pipeline

```bash
# Test 1: Push
git checkout -b feature/test
git push origin feature/test
# ✅ Pipeline auto-starts

# Test 2: Manual run with different branch
Harness → Run Pipeline → Enter branch name
# ✅ Override default branch
```

---

## 📦 How DevOps Scripts Work

**Pipeline auto-clones two repositories:**

```
1. Your App Code (main repo)
   ↓
2. DevOps Scripts (from DevOps/devops-automation)
   ↓
Both available in: /harness/workspace/
   ↓
Scripts used: lint.py, secret_scan.py, coverity.py, blackduck.py
```

**No manual downloads needed!** Scripts are automatically cloned and ready to use.

---

## 🐛 Common Issues

### ❌ Pipeline not triggering
```
✓ Check webhook is configured in GitHub
✓ Verify webhook shows successful deliveries
✓ Ensure trigger is enabled in Harness
```

### ❌ Pipeline waits for input
```
Problem: Using build: <+input>
Solution: Change to build: <+input>.default(<+trigger.branch>)
```

### ❌ Dependencies fail
```
✓ Verify JFrog credentials in Harness Secrets
✓ Check Maven settings.xml configuration
```

### ❌ Scripts not found
```
Error: python3: can't open file 'devops-automation/scripts/lint.py'

✓ Verify github_devops_connector has correct token
✓ Check token has access to DevOps/devops-automation repo
```

---

## ❓ FAQ

**Q: Why `.default()` in build configuration?**  
**A:** Supports both automatic (90%) and manual (10%) execution. Best practice for production CI/CD.

**Q: Can I test a different branch manually?**  
**A:** Yes! Harness → Run Pipeline → Enter custom branch name → Run

**Q: What branch does automatic trigger use?**  
**A:** 
- Push → Uses pushed branch
- PR → Uses source branch  
- Merge → Uses target branch

**Q: How do I know which event triggered the pipeline?**  
**A:** Check variable `triggerType` = `<+trigger.event>` (shows: push, pull_request, etc.)

---

## ✅ Best Practices

```yaml
✓ Use: <+input>.default(<+trigger.branch>)  # Automatic + Manual flexibility
✓ Store all credentials in Harness Secrets   # Never commit secrets
✓ Use additionalRepositories for scripts     # Centralized DevOps scripts
✓ Single auto-trigger (not 3 separate)       # Simpler maintenance
✓ Set up email notifications                 # Monitor pipeline health
```

---

## 🎯 Pipeline Flow Diagram

```
Developer Pushes Code
    ↓
GitHub Webhook Triggered
    ↓
Harness Pipeline Starts
    ↓
┌─────────────────────────┐
│  Clone Repositories     │ ← App code + DevOps scripts
├─────────────────────────┤
│  1. Lint               │ ← Code quality
│  2. Secret Scan        │ ← Security
│  3. Dependencies       │ ← Maven + JFrog
│  4. Build              │ ← Create JAR
│  5. Unit Tests         │ ← JUnit
│  6. Coverity SAST      │ ← Vulnerability scan
│  7. Black Duck SCA     │ ← Component scan
└─────────────────────────┘
    ↓
✅ JAR Ready to Deploy
```

---

## 📊 Pipeline Variables

| Variable | Value | Usage |
|----------|-------|-------|
| `triggerType` | `<+trigger.event>` | Event type (push/pr/merge) |
| `<+trigger.branch>` | Branch name | Auto-detected from GitHub |
| `<+project.identifier>` | Project ID | Project context |
| `<+org.identifier>` | Org ID | Organization context |

---

## 🎓 Key Takeaways

1. **Fully Automated** - Push code, pipeline runs automatically
2. **7 Quality Gates** - Code quality + Security + Testing
3. **Smart Configuration** - Auto-execution with manual override option
4. **Centralized Scripts** - DevOps scripts auto-cloned from shared repo
5. **Zero Manual Steps** - Webhook → Build → Test → Scan 


