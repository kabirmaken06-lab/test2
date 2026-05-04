# CareerAI

CareerAI is a Vite + React career assistant with local login, plan limits, resume tools, ATS analysis, cover letters, interview prep, and a Node/Vercel API proxy for Claude.

## Local Setup

```powershell
npm install
Copy-Item .env.example .env
```

Open `.env` and set your real Anthropic key:

```env
ANTHROPIC_API_KEY=your_real_key_here
ANTHROPIC_MODEL=claude-sonnet-4-20250514
VITE_GOOGLE_CLIENT_ID=your_google_oauth_web_client_id_here
APP_URL=http://localhost:5173
RESEND_API_KEY=your_resend_api_key_here
REMINDER_FROM_EMAIL=CareerAI <onboarding@resend.dev>
```

Start the frontend and local API proxy:

```powershell
npm run dev
```

Open:

```text
http://localhost:5173
```

## Login and Plans

Login/signup is currently local demo auth stored in browser `localStorage`. Plans are also local and control daily AI action limits plus job tracker limits:

- Free: 5 AI actions/day, 10 jobs
- Pro: 100 AI actions/day, 75 jobs
- Elite: 500 AI actions/day, 300 jobs

For real production accounts and secure plan enforcement, connect a real auth provider plus a database, then validate usage on the server.

Google login uses Google Identity Services when `VITE_GOOGLE_CLIENT_ID` is configured. Without it, the app shows a demo Google login button for local testing.

## Plan Upgrades

Payment methods have been removed. Plan changes currently happen inside the app with a professional confirmation modal and are stored in browser `localStorage`.

Paid plans are monthly. The app stores a `planRenewsAt` date when Pro or Elite is activated, moves expired plans back to Free, and calls `/api/send-renewal-email` when a paid plan is close to expiry.

Renewal emails use Resend when `RESEND_API_KEY` is configured. Without it, the endpoint runs in mock mode for testing. True automatic emails while the user is offline require a scheduled backend job and a database.

## Deploy on Vercel

For a quick demo, you can deploy without any environment variables. The AI and renewal email endpoints will use safe mock mode.

For a real demo with live services, add only the keys you actually have in Vercel. Do not add the placeholder values from `.env.example`:

```text
ANTHROPIC_API_KEY
ANTHROPIC_MODEL
VITE_GOOGLE_CLIENT_ID
APP_URL
RESEND_API_KEY
REMINDER_FROM_EMAIL
```

Then deploy normally. Vercel will build `dist/` and use these API functions:

- `/api/claude`
- `/api/send-renewal-email`

The helper code lives in `lib/` so Vercel does not treat helper modules as public API routes.
