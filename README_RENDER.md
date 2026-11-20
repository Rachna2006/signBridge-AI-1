# Deploying SignBridge AI on Render

This guide puts the app online so judges can open it from any device.
You deploy **two services**: the Flask API (a web service) and the React site (a static site).
Both are defined in `render.yaml`, so Render can create them together.

## Read this first (free tier facts)

- **Cold starts:** a free web service sleeps after 15 minutes without traffic. The first request afterwards takes about a minute. **Open the site 5 minutes before your demo** and click around.
- **History is not permanent on the free tier:** the SQLite file is lost whenever the API restarts, redeploys or sleeps. That is fine for a demo. Real storage later means a Render Postgres database or a paid persistent disk.
- **HTTPS is automatic.** This matters because browsers only allow webcam and microphone on HTTPS (or localhost).
- **Model size (Stages 3 and 4):** free instances have limited memory, so we will keep the trained model small (scikit-learn or a small Keras/TFLite model). Check Render's current limits in your dashboard before deploying a big model.
- Always keep a **local copy running on your laptop** as a backup for the demo.

## Before you start

1. A free GitHub account and a free Render account (https://render.com, sign in with GitHub).
2. Git installed (https://git-scm.com).
3. The app works locally (see `README.md`).

## Step 1: Push the project to GitHub

Create an empty repository on GitHub named `signbridge-ai` (no README, no .gitignore). Then in PowerShell, inside the `signbridge-ai` folder:

```powershell
git init
git add .
git commit -m "SignBridge AI stage 1"
git branch -M main
git remote add origin https://github.com/YOUR-USERNAME/signbridge-ai.git
git push -u origin main
```

`render.yaml` must be at the top level of the repository, next to `backend` and `frontend`.

## Step 2: Create both services with the Blueprint

1. In the Render dashboard click **New**, then **Blueprint**.
2. Connect your GitHub account and choose the `signbridge-ai` repository.
3. Render reads `render.yaml` and shows two services: `signbridge-api` and `signbridge-web`.
4. It asks for two values. Enter temporary guesses for now:
   - `FRONTEND_ORIGIN` = `https://signbridge-web.onrender.com`
   - `VITE_API_URL` = `https://signbridge-api.onrender.com`
5. Click **Apply** and wait for both builds to finish (a few minutes).

## Step 3: Fix the URLs if Render changed the names

If another person already used the name `signbridge-api` or `signbridge-web`, Render adds a random suffix. Check the real URLs at the top of each service page, then:

1. Open **signbridge-api**, then **Environment**. Set `FRONTEND_ORIGIN` to the real frontend URL (no slash at the end). Save. The API restarts by itself.
2. Open **signbridge-web**, then **Environment**. Set `VITE_API_URL` to the real API URL (no slash at the end). Save.
3. On **signbridge-web** click **Manual Deploy**, then **Clear build cache & deploy**. The frontend must be rebuilt, because `VITE_API_URL` is baked into the site while it builds.

If the guessed names were correct, you can skip this step.

## Step 4: Test the deployment

1. Open `https://YOUR-API-URL/api/health`. You should see JSON containing `"status":"ok"`. The first load can take a minute.
2. Open your frontend URL. The landing page should appear.
3. Click **Get Started**. The dashboard badge should say **Backend: Online**.
4. On **Translation History**, add a developer test record, search for it, and delete it.
5. Refresh the dashboard page directly (not by clicking). It should still load. This confirms the rewrite rule works.

## Manual setup (if you do not want to use the Blueprint)

**API:** New, Web Service. Root Directory `backend`. Runtime Python 3. Build Command `pip install -r requirements.txt`. Start Command `gunicorn --bind 0.0.0.0:$PORT app:app`. Health Check Path `/api/health`. Instance type Free. Environment variables: `PYTHON_VERSION` = `3.11.9`, `FRONTEND_ORIGIN` = your frontend URL.

**Frontend:** New, Static Site. Root Directory `frontend`. Build Command `npm install && npm run build`. Publish Directory `dist`. Environment variables: `NODE_VERSION` = `20`, `VITE_API_URL` = your API URL. Under Redirects/Rewrites add a **Rewrite** from `/*` to `/index.html`.

## Updating the live site

Every `git push` to `main` redeploys automatically:

```powershell
git add .
git commit -m "describe your change"
git push
```

## Troubleshooting

| Problem | Likely cause and fix |
|---|---|
| Dashboard says **Backend: Offline** | The API is asleep (wait a minute and refresh), or `VITE_API_URL` is wrong. Fix it, then redeploy the frontend with a cleared build cache. |
| Browser console shows a CORS error | `FRONTEND_ORIGIN` on the API does not exactly match your frontend URL. Use `https://`, no trailing slash, no path. |
| Page refresh on `/app/history` shows "Not Found" | The rewrite rule is missing. Add `/*` to `/index.html` as a **Rewrite** (not Redirect). |
| API build fails | Check the build log. Make sure `rootDir` is `backend` and `requirements.txt` is inside it. |
| API fails to start with "No module named app" | The start command must run from the `backend` folder (Root Directory = `backend`) and be `gunicorn ... app:app`. |
| Frontend build fails | Check the log. Try `npm install && npm run build` locally first to see the same error. |
| History disappeared | Expected on the free tier: the SQLite file resets when the API restarts or sleeps. |
| Webcam does not start (later stages) | Use the `https://` address, allow camera permission in the browser, and close other apps using the camera. |

## Demo-day checklist

1. Open the live site 5 minutes early to wake the API.
2. Check **Backend: Online** on the dashboard.
3. Have the local version running as a backup.
4. Use Chrome or Edge, and allow camera and microphone when asked.
5. Have a stable internet connection (or a mobile hotspot) ready.
