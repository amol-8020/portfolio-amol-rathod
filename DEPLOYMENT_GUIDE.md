# 📦 Complete Deployment Guide for Amol's Portfolio

**Current Problem:** Vercel deployment works, but API calls fail because backend is on localhost.

**Solution:** Deploy backend to cloud, update frontend API URL, configure environment variables.

---

## 📋 Overview of What We'll Do

```
LOCAL (Current):
┌─────────────────────────────────────────┐
│  Frontend (React) - http://localhost:5173│
│  Backend (Node.js) - http://localhost:4000│
│  Database - XAMPP MySQL                  │
└─────────────────────────────────────────┘

PRODUCTION (After Fix):
┌────────────────────────────────────────────────────────────┐
│  Frontend (React) - your-portfolio.vercel.app (Vercel)     │
│  Backend (Node.js) - your-api.onrender.com (Render.com)   │
│  Database - File-based (JSON) or MongoDB                   │
└────────────────────────────────────────────────────────────┘
```

---

## ✅ STEP 1: Deploy Backend to Render.com (FREE)

### Why Render?
- ✅ Free tier available
- ✅ Easy one-click deployment
- ✅ Auto-restart on crash
- ✅ Custom domain included
- ✅ No credit card needed initially

### Steps:

#### 1.1 Prepare Your Backend Repository

```bash
# Your backend code is already in server/ folder
# Make sure server/.gitignore exists and has:
node_modules/
.env
uploads/
data/
.DS_Store
```

**Create `server/.gitignore` if missing:**
```
node_modules/
.env
uploads/
data/
.DS_Store
*.log
```

#### 1.2 Push to GitHub

```bash
# Initialize git in your portfolio folder (if not done)
git init
git add .
git commit -m "Add portfolio project"
git branch -M main
git remote add origin https://github.com/YOUR_USERNAME/AmolRathod-Portfolio.git
git push -u origin main
```

**Login to GitHub:** https://github.com

#### 1.3 Deploy Backend to Render

1. Go to **https://render.com**
2. Click **"Sign up"** (use GitHub to sign up - recommended)
3. After login, click **"+ New"** → **"Web Service"**
4. Choose **"Build and deploy from a Git repository"**
5. Click **"Connect account"** to link GitHub
6. Search and select `AmolRathod-Portfolio` repository
7. Fill in the form:

```
Name:                portfolio-backend
Environment:         Node
Root Directory:      server
Build Command:       npm install
Start Command:       npm start
```

8. Scroll to **Environment** section
9. Add environment variable:
   - **Key:** `NODE_ENV`
   - **Value:** `production`

10. Click **"Create Web Service"**
11. **Wait 3-5 minutes** for deployment
12. Copy the URL shown (e.g., `https://portfolio-backend-xyz.onrender.com`)
13. ✅ Your backend is now live!

---

## ✅ STEP 2: Update Frontend for Production API

### 2.1 Create `.env.production` File

Create a file in the `client/` folder:

**File:** `client/.env.production`

```env
VITE_API_BASE=https://portfolio-backend-xyz.onrender.com
VITE_DASHBOARD_PIN=8821
```

⚠️ **Replace `portfolio-backend-xyz.onrender.com` with YOUR actual Render URL from Step 1.12**

### 2.2 Create `.env.development` File

**File:** `client/.env.development`

```env
VITE_API_BASE=http://localhost:4000
VITE_DASHBOARD_PIN=8821
```

### 2.3 Update Vercel Environment Variables

1. Go to **Vercel Dashboard:** https://vercel.com/dashboard
2. Click on your **portfolio project**
3. Go to **Settings** → **Environment Variables**
4. Add new variable:
   - **Name:** `VITE_API_BASE`
   - **Value:** `https://portfolio-backend-xyz.onrender.com`
   - **Select:** Production, Preview, Development
5. Click **Save**
6. Redeploy project (click **Deployments** → **Redeploy** on latest commit)

---

## ✅ STEP 3: Fix CORS (If Needed)

Your backend already has CORS enabled, but let's verify it's correct.

**File:** `server/index.js` (around line 70-72)

Current code:
```javascript
app.use(cors())
```

This allows **ALL** origins. For production, make it safer:

```javascript
const allowedOrigins = [
  'https://amolrathod-portfolio.vercel.app',  // Replace with YOUR Vercel URL
  'http://localhost:5173',
  'http://localhost:3000',
]

app.use(cors({
  origin: (origin, callback) => {
    if (!origin || allowedOrigins.includes(origin)) {
      callback(null, true)
    } else {
      callback(new Error('CORS not allowed'))
    }
  },
  credentials: true,
}))
```

---

## ✅ STEP 4: Test Everything

### Test 1: Check Backend is Working
1. Go to `https://portfolio-backend-xyz.onrender.com/api/health`
2. You should see: `{"ok":true}`

### Test 2: Check Projects API
1. Go to `https://portfolio-backend-xyz.onrender.com/api/projects`
2. You should see your projects list in JSON format

### Test 3: Check Frontend Works
1. Open your Vercel URL: `https://your-portfolio.vercel.app`
2. Projects section should show 2+ projects
3. Stats should show project count correctly
4. Open browser **DevTools** (F12) → **Console** to check for errors

### Test 4: Check Network Calls
1. Open DevTools → **Network** tab
2. Refresh page
3. Look for `/api/projects` call
4. Should show **200 status** (success)
5. Should have response JSON with projects

---

## ✅ STEP 5: Production Checklist

Before considering deployment complete, verify:

- [ ] Backend deployed on Render/Railway
- [ ] Backend URL added to Vercel environment variables
- [ ] Frontend `.env.production` created with correct API URL
- [ ] Vercel redeployed after environment variable change
- [ ] `/api/health` endpoint returns `{"ok":true}`
- [ ] `/api/projects` returns project list
- [ ] Vercel site shows projects (not "0 projects")
- [ ] Browser console has no CORS errors
- [ ] Network tab shows API calls with 200 status

---

## 📚 Troubleshooting

### Problem: "Unable to load projects" on Vercel

**Check 1:** Is backend URL in Vercel env vars?
```bash
# Don't include trailing slash
✅ https://portfolio-backend-xyz.onrender.com
❌ https://portfolio-backend-xyz.onrender.com/
```

**Check 2:** Is Render backend actually online?
```
Visit: https://portfolio-backend-xyz.onrender.com/api/health
Should show: {"ok":true}
```

**Check 3:** Check Vercel logs
- Go to Vercel Dashboard → Your Project → **Deployments**
- Click latest deployment → **Logs**
- Look for any error messages

### Problem: CORS Error in Browser

**Solution:** Update `server/index.js` with your Vercel domain:
```javascript
const allowedOrigins = [
  'https://your-actual-vercel-url.vercel.app',
  'http://localhost:5173',
]

app.use(cors({
  origin: (origin, callback) => {
    if (!origin || allowedOrigins.includes(origin)) {
      callback(null, true)
    } else {
      callback(new Error('CORS not allowed'))
    }
  },
}))
```

Then push to GitHub and Render will auto-redeploy.

### Problem: Render Free Tier Sleeping

Free tier on Render spins down after 15 minutes of inactivity.
- **First call takes 30 seconds** to spin up
- **Subsequent calls are instant**
- Upgrade to **Starter** ($7/month) to keep always running

**For now:** Use free tier, just know first load might be slow.

---

## 🚀 Alternative Backend Hosting Options

If Render doesn't work, try these:

### Option 1: Railway.app
1. Go to https://railway.app
2. Sign up with GitHub
3. Create new project → Deploy GitHub repo
4. Select `server` directory
5. Add Node.js service
6. Free tier: $5 credit/month

### Option 2: Heroku (Free)
1. Go to https://www.heroku.com
2. Create account
3. Deploy from GitHub
4. ✅ Free, but older platform

### Option 3: Vercel Backend (Serverless Functions)
- Deploy backend as Vercel Functions
- Same platform as frontend
- More expensive for always-running service

---

## 📝 Summary of Changes Made

### Files to Create/Update:

1. **`server/.gitignore`** - Exclude node_modules from git
2. **`client/.env.production`** - Production API URL
3. **`client/.env.development`** - Local API URL (optional)
4. **`server/index.js`** - Update CORS to be more restrictive (optional)

### Environment Variables:

| Service | Variable | Value |
|---------|----------|-------|
| Vercel | `VITE_API_BASE` | `https://your-backend.onrender.com` |
| Render | `NODE_ENV` | `production` |

---

## ✨ You're Done!

Your portfolio will now:
- ✅ Load from Vercel (frontend)
- ✅ Fetch data from production backend
- ✅ Display projects correctly
- ✅ Show accurate stats
- ✅ Support contact form (if backend receives it)

**Questions?** Check the Network tab in DevTools to debug API calls.

---

**Next Steps After This:**
1. Monitor Render backend for any errors
2. Consider upgrading Render to Starter plan ($7/month) to avoid cold starts
3. Eventually add a proper database (MongoDB, PostgreSQL)
4. Add authentication for admin dashboard
