# ✅ DEPLOYMENT CHECKLIST

Print this and check off items as you complete them.

---

## Phase 1: Preparation

- [ ] Code pushed to GitHub repository
- [ ] `server/.gitignore` created with node_modules
- [ ] `client/.env.production` created with placeholder URL
- [ ] `client/.env.development` created with localhost:4000
- [ ] Reviewed DEPLOYMENT_GUIDE.md
- [ ] Render.com account created (via GitHub)

---

## Phase 2: Backend Deployment

### On Render.com

- [ ] Created new Web Service
- [ ] Selected GitHub repository: AmolRathod-Portfolio
- [ ] Set Root Directory: `server`
- [ ] Set Build Command: `npm install`
- [ ] Set Start Command: `npm start`
- [ ] Added Environment Variable:
  - [ ] KEY: `NODE_ENV`
  - [ ] VALUE: `production`
- [ ] Clicked "Create Web Service"
- [ ] Waited for deployment to complete (green checkmark)
- [ ] Backend URL visible: `https://portfolio-backend-_____.onrender.com`

### Test Backend

- [ ] Opened: `https://portfolio-backend-_____.onrender.com/api/health`
- [ ] Response shows: `{"ok": true, ...}`
- [ ] Opened: `https://portfolio-backend-_____.onrender.com/api/projects`
- [ ] Response shows: JSON array with 2 projects

**Backend URL to use:** ___________________________________

---

## Phase 3: Frontend Configuration

### Update Environment Files

- [ ] Opened `client/.env.production`
- [ ] Replaced URL with actual Render backend URL
- [ ] **Removed trailing slash** from URL
- [ ] Saved file
- [ ] Opened `client/.env.development`
- [ ] Verified localhost:4000 is set

### Update Render CORS (Optional but Recommended)

- [ ] Opened `server/index.js`
- [ ] Updated `allowedOrigins` array with Vercel URL:
  ```
  'https://amolrathod-portfolio.vercel.app'
  ```
- [ ] Saved file

### Push Changes to GitHub

- [ ] Opened terminal in project folder
- [ ] Ran: `git add .`
- [ ] Ran: `git commit -m "Configure production API"`
- [ ] Ran: `git push`
- [ ] Verified changes on GitHub.com

---

## Phase 4: Vercel Configuration

### Add Environment Variables

- [ ] Logged into https://vercel.com/dashboard
- [ ] Selected project: AmolRathod-Portfolio
- [ ] Went to: Settings → Environment Variables
- [ ] Added new variable:
  - [ ] NAME: `VITE_API_BASE`
  - [ ] VALUE: `https://portfolio-backend-_____.onrender.com`
  - [ ] Select: Production, Preview, Development
  - [ ] Clicked SAVE
- [ ] Deploy automatically triggered OR manually redeployed

### Monitor Deployment

- [ ] Went to: Deployments
- [ ] Latest deployment shows green checkmark (Ready)
- [ ] Did NOT show "Build Failed"

---

## Phase 5: Verification

### Test Backend

- [ ] Visited: `https://portfolio-backend-_____.onrender.com/api/health`
- [ ] Status: 200 OK
- [ ] Response JSON looks correct

### Test Frontend

- [ ] Visited: `https://amolrathod-portfolio.vercel.app`
- [ ] Page loads properly
- [ ] No infinite loading spinner

### Test Projects Section

- [ ] Scrolled to Projects section
- [ ] Shows 2 projects (or more if API has more)
- [ ] Project images visible
- [ ] Project titles correct

### Test Stats Section

- [ ] Scrolled to Stats section
- [ ] Shows "2+ Projects" (not "0+ Projects")
- [ ] Shows "5+ Skills"
- [ ] Shows "3+ Focus areas"

### Test Animations

- [ ] Hover on project cards → smooth animation
- [ ] Animations work without stuttering
- [ ] Skills bars animate on scroll

### Debug Check (F12 DevTools)

- [ ] Opened: DevTools (F12)
- [ ] Console tab: NO red error messages
- [ ] Network tab:
  - [ ] `/api/projects` call shows 200 status
  - [ ] Response is JSON array, not error
  - [ ] No CORS errors

---

## Phase 6: Troubleshooting (If Needed)

### If Projects Don't Load

- [ ] Opened: `https://portfolio-backend-_____.onrender.com/api/projects`
- [ ] Verified response is JSON
- [ ] Checked Render logs for errors
- [ ] Verified `VITE_API_BASE` in Vercel matches backend URL
- [ ] Checked for trailing slash in URL (should NOT have one)

### If CORS Error in Console

- [ ] Updated `server/index.js` allowedOrigins
- [ ] Pushed changes to GitHub
- [ ] Waited for Render to auto-redeploy (1-2 min)
- [ ] Refreshed Vercel site (Ctrl+Shift+R to hard refresh)

### If Vercel Build Failed

- [ ] Checked Vercel Deployments → Logs
- [ ] Verified `VITE_API_BASE` env variable is set
- [ ] Deployed locally: `cd client && npm run build`
- [ ] Fixed any build errors shown

### If Render Deployment Failed

- [ ] Checked Render Deployments → Logs
- [ ] Verified `server/package.json` exists
- [ ] Tested locally: `cd server && npm install && npm start`
- [ ] Fixed any errors shown

---

## Optional Improvements

- [ ] Added custom domain to Vercel
- [ ] Added custom domain to Render
- [ ] Upgraded Render to Starter plan ($7/month) to avoid cold starts
- [ ] Set up monitoring/alerts on Render
- [ ] Planned database migration (MongoDB, PostgreSQL)
- [ ] Added analytics to Vercel
- [ ] Backed up projects.json data

---

## Success! 🎉

If all checkboxes above are checked, your portfolio is successfully deployed!

### Your Live URLs:
- **Frontend:** https://amolrathod-portfolio.vercel.app
- **Backend:** https://portfolio-backend-_____.onrender.com
- **API Endpoint:** https://portfolio-backend-_____.onrender.com/api/projects

### Keep These For Reference:
- Render Backend URL: _______________________________
- Vercel Project URL: _______________________________
- Render Dashboard: https://dashboard.render.com
- Vercel Dashboard: https://vercel.com/dashboard

---

## Post-Launch (Next 24 Hours)

- [ ] Monitor Render logs for errors
- [ ] Monitor Vercel dashboard for issues
- [ ] Test projects section once more
- [ ] Share portfolio with friends/family
- [ ] Update GitHub README with deployment info
- [ ] Keep `.env.production` safe (don't share)

---

## Notes

Write any issues encountered here:

_________________________________________________________________________

_________________________________________________________________________

_________________________________________________________________________

---

**Document Version:** 1.0
**Date Completed:** _____ / _____ / _____
**Completed By:** _____________________________
