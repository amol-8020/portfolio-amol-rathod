# 📚 Complete Production Deployment Reference

**Purpose:** Understand the complete architecture and how each piece connects.

---

## Architecture Comparison

### BEFORE (Local Development)

```
┌────────────────────────────────────────────────────────────┐
│                    YOUR COMPUTER                           │
├────────────────────────────────────────────────────────────┤
│                                                             │
│  ┌──────────────────┐         ┌──────────────────┐         │
│  │   React App      │         │  Node.js Server  │         │
│  │ localhost:5173   │◄────────►│ localhost:4000   │         │
│  │                  │  HTTP    │                  │         │
│  │                  │  Requests│                  │         │
│  └──────────────────┘         └──────────────────┘         │
│         ▲                              ▲                    │
│         │                              │                    │
│    You View              Data stored in JSON files          │
│    In Browser            (data/projects.json)              │
│                                                             │
└────────────────────────────────────────────────────────────┘
```

**Problem:** Everything stops when you close your computer!

---

### AFTER (Production Deployment)

```
┌─────────────────────────────────────────────────────────────────────────┐
│                         THE INTERNET (CLOUD)                            │
└─────────────────────────────────────────────────────────────────────────┘

    ┌──────────────────────────┐              ┌──────────────────────────┐
    │   VERCEL (Frontend)       │              │   RENDER (Backend)       │
    │                          │              │                          │
    │  amolrathod-portfolio.   │  HTTP API    │  portfolio-backend  .    │
    │     vercel.app           │◄────────────►│     onrender.com         │
    │                          │   Requests   │                          │
    │ - React App              │   /api/*     │ - Node.js Server         │
    │ - HTML, CSS, JS          │              │ - Express API            │
    │ - Auto-deployed on       │              │ - JSON Data Files        │
    │   every GitHub push      │              │ - Auto-deployed on       │
    │                          │              │   every GitHub push      │
    └──────────────────────────┘              └──────────────────────────┘
           ▲
           │
           │
    ┌──────────────┐
    │  Your Laptop │
    │              │
    │ Opens:       │
    │ browser and  │
    │ visits URL   │
    │              │
    └──────────────┘

Data Flow:
1. Browser Request:  "Get projects"
2. Vercel Frontend:  "Let me ask backend..."
3. Render Backend:   "Here are projects from database!"
4. Vercel Frontend:  "Got it! Showing to user..."
5. User Sees:        Projects displayed!

Available 24/7, always running, no laptop needed! ✅
```

---

## File Organization

```
AmolRathod-Portfolio/
│
├── client/                    # FRONTEND (React)
│   ├── src/
│   │   ├── components/
│   │   ├── pages/
│   │   └── lib/
│   │       └── api.ts        # ← Uses VITE_API_BASE env variable
│   │
│   ├── .env.development       # ← http://localhost:4000
│   ├── .env.production        # ← https://your-backend.onrender.com
│   ├── package.json
│   └── vite.config.ts         # ← Proxy config for local dev
│
├── server/                    # BACKEND (Node.js/Express)
│   ├── index.js               # ← Main server file
│   ├── package.json
│   ├── .gitignore             # ← Don't upload node_modules!
│   ├── data/
│   │   └── projects.json      # ← Your data
│   └── uploads/               # ← Image uploads
│
├── DEPLOYMENT_GUIDE.md        # ← Detailed step-by-step
├── QUICK_DEPLOYMENT.md        # ← Quick version
├── DEPLOYMENT_CHECKLIST.md    # ← Verification checklist
├── TROUBLESHOOTING.md         # ← If something breaks
│
├── vercel.json                # ← Frontend deployment config
├── package.json               # ← Root project
└── .gitignore                 # ← Don't upload sensitive files
```

---

## Environment Variables Explained

### What are Environment Variables?

Settings that change between local and production without changing code.

**Example:** API URL
- **Local:** `http://localhost:4000` (your computer)
- **Production:** `https://portfolio-backend-xyz.onrender.com` (Render)

Same code, different URL! ✅

### Where They're Used

#### Frontend (`client/.env.production`)

```env
# This URL changes based on where backend is deployed
VITE_API_BASE=https://portfolio-backend-xyz.onrender.com

# Dashboard PIN stays same
VITE_DASHBOARD_PIN=8821
```

**Used in:** `client/src/lib/api.ts`
```typescript
const baseURL = import.meta.env.VITE_API_BASE?.replace(/\/$/, '') ?? ''
// In production: baseURL = 'https://portfolio-backend-xyz.onrender.com'
// In local dev: baseURL = 'http://localhost:4000'
```

#### Backend (`server/` environment on Render)

```env
NODE_ENV=production
# Automatically set by Render, tells server it's in production
# Used for error handling, logging, and CORS configuration
```

### How Vercel Loads Environment Variables

1. You create `.env.production` in `client/` folder
2. During build, Vercel substitutes `import.meta.env.VITE_API_BASE` with actual value
3. Final JavaScript has real URL baked in
4. Frontend always knows where backend is!

---

## Deployment Process Flow

### Step 1: You Make Changes (5 min)

```
Your Computer:
  1. Edit files (ProjectsSection.tsx, etc.)
  2. Test locally (npm run dev)
  3. git add .
  4. git commit -m "Fix something"
  5. git push
```

### Step 2: GitHub Receives Code (instant)

```
GitHub.com:
  ✅ Receives your push
  ✅ Stores code
  ✅ Notifies Vercel and Render (webhooks)
```

### Step 3: Vercel Auto-Deploys (2-3 min)

```
Vercel:
  1. Detects GitHub push
  2. Pulls latest code from GitHub
  3. Runs: npm install (install dependencies)
  4. Runs: npm run build (build React app)
  5. Builds .env.production:
     VITE_API_BASE=https://...
  6. Uploads built files to CDN
  7. Domain points to new version
  ✅ New version LIVE!
```

### Step 4: Render Auto-Deploys (2-3 min)

```
Render:
  1. Detects GitHub push
  2. Pulls latest code from GitHub
  3. Runs: npm install (in server/ directory)
  4. Runs: npm start (start server)
  5. Sets environment variables:
     NODE_ENV=production
  6. Server restarts
  ✅ New backend version LIVE!
```

### Step 5: User Benefits

```
Any user visiting your site gets:
  ✅ Latest frontend code
  ✅ Latest backend code
  ✅ Most recent projects.json
  ✅ All via HTTPS (secure)
  ✅ Works 24/7 automatically!
```

---

## API Communication

### How Frontend & Backend Talk

#### Request Example: Getting Projects

**Frontend (React) wants data:**
```javascript
// In ProjectsSection.tsx
api.get<Project[]>('/api/projects')
  .then((r) => setItems(r.data))
  .catch(() => console.log('Failed!'))
```

**What Actually Happens:**

```
Step 1: Construct Full URL
  - baseURL = 'https://portfolio-backend-xyz.onrender.com'
  - endpoint = '/api/projects'
  - Full URL = 'https://portfolio-backend-xyz.onrender.com/api/projects'

Step 2: Send HTTPS Request
  GET https://portfolio-backend-xyz.onrender.com/api/projects

Step 3: Network Travel (internet)
  Browser → Vercel → Internet → Render Server

Step 4: Backend Receives Request
  // server/index.js line ~150
  app.get('/api/projects', (_req, res) => {
    const projects = readProjects()  // Read from JSON file
    res.json(projects)               // Send back as JSON
  })

Step 5: Backend Responds
  Response: [
    { id: "1", title: "CampusVoice", ... },
    { id: "2", title: "Portfolio", ... }
  ]

Step 6: Network Travel (back)
  Render Server → Internet → Vercel → Browser

Step 7: Frontend Receives & Displays
  setItems(r.data)        // Save projects to state
  // React re-renders with new data
  // User sees projects on screen!
```

**Time taken:** Usually 200-500ms ✅

---

## CORS Explained

### What is CORS?

**CORS** = Cross-Origin Resource Sharing

Browser security feature that says:
- ✅ Requests from same domain = allowed
- ❌ Requests from different domain = blocked (by default)

### Why Do We Need It?

**Local:** Browser → Backend on same port (localhost)
- Requests allowed ✅

**Production:** Vercel (vercel.app) → Render (onrender.com)
- Different domains!
- Blocked by default ❌
- Need to explicitly allow ✅

### How We Allow It

**In `server/index.js`:**

```javascript
const allowedOrigins = [
  'https://amolrathod-portfolio.vercel.app',  ← Allow this domain
  'http://localhost:5173',                    ← Allow local dev
]

app.use(cors({
  origin: (origin, callback) => {
    if (allowedOrigins.includes(origin)) {
      callback(null, true)  ← Yes, Allow!
    } else {
      callback(new Error('CORS not allowed'))  ← No, Block!
    }
  },
}))
```

### Error Message

If CORS fails, browser console shows:
```
Access to XMLHttpRequest at 'https://portfolio-backend-xyz.onrender.com/api/projects'
from origin 'https://amolrathod-portfolio.vercel.app'
has been blocked by CORS policy
```

**Fix:** Add your Vercel URL to `allowedOrigins` ✅

---

## Troubleshooting Decision Tree

```
Is projects section empty?
│
├─→ Check browser console (F12)
│   │
│   ├─→ See CORS error?
│   │   └─→ Go to TROUBLESHOOTING.md → Issue 2
│   │
│   ├─→ See network error?
│   │   └─→ Go to TROUBLESHOOTING.md → Issue 1
│   │
│   └─→ No errors in console?
│       └─→ Check Network tab
│           │
│           ├─→ /api/projects call?
│           │   │
│           │   ├─→ Status 200?
│           │   │   └─→ Check Response tab, should have JSON
│           │   │
│           │   └─→ Status 500?
│           │       └─→ Backend error, check Render logs
│           │
│           └─→ No /api/projects call?
│               └─→ VITE_API_BASE not set correctly
│                   Check .env.production
```

---

## Quick Reference

### URLs to Bookmark

| Service | URL | Purpose |
|---------|-----|---------|
| Vercel Backend | https://vercel.com/dashboard | Deploy frontend |
| Render | https://dashboard.render.com | Deploy backend |
| GitHub | https://github.com | Code repository |
| Frontend Live | https://amolrathod-portfolio.vercel.app | Users see this |
| Backend API | https://portfolio-backend-xyz.onrender.com | Backend live |
| API Health | https://portfolio-backend-xyz.onrender.com/api/health | Check backend |
| API Projects | https://portfolio-backend-xyz.onrender.com/api/projects | Get projects |

### Common Commands

#### Push Changes to Production
```bash
git add .
git commit -m "Description of changes"
git push
# Vercel and Render automatically deploy!
```

#### Test Backend Locally
```bash
cd server
npm install      # First time only
npm start        # Starts on localhost:4000
```

#### Test Frontend Locally
```bash
cd client
npm install      # First time only
npm run dev      # Starts on localhost:5173
```

#### Check Render Logs
```
1. https://dashboard.render.com
2. Click your service
3. Logs tab
```

#### Check Vercel Logs
```
1. https://vercel.com/dashboard
2. Click your project
3. Deployments → Latest → Logs
```

---

## Production Checklist

### Before Deployment
- [ ] All features work locally
- [ ] No console errors on local site
- [ ] API calls work locally
- [ ] Projects display correctly
- [ ] All animations smooth

### After Deployment
- [ ] Backend deployed on Render
- [ ] Frontend redeployed on Vercel with new env vars
- [ ] `VITE_API_BASE` points to correct Render URL
- [ ] No trailing slash in API URL
- [ ] `/api/health` returns 200
- [ ] `/api/projects` returns JSON
- [ ] Frontend displays projects
- [ ] No CORS errors in console
- [ ] Network calls show 200 status

---

## Support & Resources

### Documentation
- [Vercel Docs](https://vercel.com/docs)
- [Render Docs](https://render.com/docs)
- [Express.js Guide](https://expressjs.com/)
- [React Docs](https://react.dev)

### This Project
- `DEPLOYMENT_GUIDE.md` - Full step-by-step
- `QUICK_DEPLOYMENT.md` - Quick version
- `TROUBLESHOOTING.md` - Problems & fixes
- `DEPLOYMENT_CHECKLIST.md` - Verification checklist

### Questions?
1. Check TROUBLESHOOTING.md first
2. Check browser DevTools (F12)
3. Check Render/Vercel logs
4. GitHub Issues section

---

**Good luck with your deployment! 🚀**
