# 🔧 Troubleshooting Production Issues

## Quick Diagnostic Checklist

Use this when your portfolio isn't working on Vercel.

---

## Issue 1: "Unable to load projects" on Vercel

### Step 1: Check if backend is running
```
Open in browser:
https://your-backend-url.onrender.com/api/health

You should see:
{
  "ok": true,
  "environment": "production",
  "timestamp": "2024-04-14T...",
  "version": "1.0.0"
}

If not:
- Check Render dashboard for errors
- Click your service → View Logs
- Look for crash messages
```

### Step 2: Check API endpoint works
```
Open in browser:
https://your-backend-url.onrender.com/api/projects

You should see JSON like:
[
  {
    "id": "abc123",
    "title": "Project Name",
    ...
  }
]
```

### Step 3: Check Vercel env variables
1. Go to Vercel dashboard
2. Click your project
3. Settings → Environment Variables
4. Look for `VITE_API_BASE`
5. Value should be: `https://your-backend-url.onrender.com` (NO trailing slash)

### Step 4: Check Vercel deployment
1. Vercel dashboard → Deployments
2. Click latest deployment
3. Logs tab
4. Look for any errors or warnings
5. If needed, click "Redeploy" button

---

## Issue 2: CORS Error in Browser Console

### Error message might look like:
```
Access to XMLHttpRequest at 'https://...' 
from origin 'https://your-vercel-url.vercel.app' 
has been blocked by CORS policy
```

### Fix:

**Update `server/index.js` with YOUR Vercel URL:**

```javascript
const allowedOrigins = [
  'http://localhost:5173',
  'http://localhost:3000',
  'https://YOUR-ACTUAL-VERCEL-URL.vercel.app',  // ← UPDATE THIS
  'https://portfolio-backend.onrender.com',
]
```

**Then push to GitHub:**
```bash
git add server/index.js
git commit -m "Fix CORS for production"
git push
```

**Then Render will automatically redeploy** (takes 1-2 min).

---

## Issue 3: 500 Error on `/api/projects`

### Check Render Logs:
1. Go to https://dashboard.render.com
2. Click your backend service
3. Click "Logs" – see what's failing
4. Common issues:
   - File permissions
   - Out of disk space
   - JSON corruption

### Reset data:
If data files are corrupted:
```
1. Delete data/ folder from Render
2. Render will recreate it
3. Or add default projects back in code
```

---

## Issue 4: Stats shows "0+ Projects"

### Check StatsSection.tsx:
The stats section should use fallback of 2 projects (added in previous fixes).

If showing 0:
1. Check browser console for errors
2. Check if `/api/projects` API call failed
3. If fallback didn't work, verify ProjectsSection.tsx has fallback data

### Verify frontend has fallback:
Look for this in `ProjectsSection.tsx`:
```javascript
const FALLBACK_PROJECTS: Project[] = [
  { id: 'campusvoice-001', ... },
  { id: 'portfolio-002', ... },
]
```

---

## Issue 5: Render Deployment Failed

### Common reasons:

**Build command failed:**
- Check that `server/` directory has `package.json`
- Check that `npm install` works locally: `cd server && npm install`

**Start command failed:**
- Verify `npm start` works locally: `cd server && npm start`
- Check for PORT environment variable issues

**Out of memory:**
- Delete unused files
- Check for large dependencies

### Check Render Build Logs:
1. Render dashboard → Your service
2. Deployments tab
3. Click failed deployment
4. View full logs

---

## Issue 6: "Port is required" or PORT errors

### Solution:
Render automatically sets `PORT` environment variable.

If you added custom PORT env var, **remove it**:
1. Render dashboard → Your service
2. Settings → Environment
3. Delete any manual `PORT` variable
4. Render uses `PORT` automatically

---

## Issue 7: Vercel Build Failed

### Common issues:

**Environment variable not set:**
- Go to Vercel Settings → Environment Variables
- Add `VITE_API_BASE` with your backend URL

**TypeScript error:**
- Check client build locally: `cd client && npm run build`
- Fix any errors shown

**Missing dependencies:**
- Ensure all `package.json` files are correct
- Run `npm install` in both `client/` and `server/` folders

### Check Vercel Build Logs:
1. Vercel dashboard → Deployments
2. Click failed deployment
3. Logs tab shows the error

---

## Quick Debug Commands

### Test backend health (in terminal):
```bash
curl https://your-backend-url.onrender.com/api/health
# Should return: {"ok":true,"environment":"production",...}
```

### Test projects API:
```bash
curl https://your-backend-url.onrender.com/api/projects
# Should return JSON array of projects
```

### Test locally before deploying:
```bash
cd server
npm install
npm start
# Should show: 🚀 Portfolio Backend Server Started
```

---

## Browser DevTools Debug

### Check Network tab (F12):
1. Open Vercel site
2. Press F12 → Network tab
3. Refresh page
4. Look for `/api/projects` call
5. Click it → Response tab
6. See what JSON is returned

### Check Console tab (F12):
1. Press F12 → Console tab
2. Look for red error messages
3. Check CORS, 404, 500 errors
4. Network failures

### Check Application tab (F12):
1. Press F12 → Application tab
2. Local Storage → Check `VITE_API_BASE` value
3. Verify it matches environment variable

---

## Still Having Issues?

### Create issue on GitHub with:
1. Exact error message (from browser console)
2. Screenshot of DevTools Network tab
3. Screenshot of Render logs
4. Screenshot of Vercel env variables
5. Your Vercel URL
6. Your Render backend URL

---

## Success Indicators ✅

When everything works:
- [ ] Browser shows no red errors in console
- [ ] Network tab shows `/api/projects` with 200 status
- [ ] `/api/projects` returns JSON array
- [ ] Projects section displays on Vercel site
- [ ] Stats show correct number of projects (2+)
- [ ] No CORS errors in console
