# SurtzMedia Handbook + "Ask Dreamcoat AI" — Setup Guide

Your handbook page now has an **Ask Dreamcoat AI** button (bottom-right). Employees can
type a policy question and get a short answer drawn only from the handbook. It hides
itself when the page is printed, so your printable handbook is unchanged.

This needs a small private helper ("function") that holds your Anthropic API key
safely. The key is **never** in the page or the files — it lives as a hidden setting
in Netlify. Follow the steps below once.

---

## What's in this folder

- `site/index.html` — your handbook, with the Ask Dreamcoat AI widget built in.
- `netlify/functions/ask.js` — the private helper that talks to Claude. (Don't edit.)
- `netlify/functions/handbook-text.js` — the handbook text it answers from. (Don't edit; I regenerate it.)
- `netlify.toml` — settings for Netlify. (Don't edit.)

---

## Step 1 — Put the site online (with the function)

Because this includes a function, the most reliable way to deploy is to connect a
GitHub repository to Netlify. You do **not** have to edit anything through GitHub — it's
just the delivery pipe. (If you already set up GitHub + Netlify earlier, replace the old
files with these and skip to Step 2.)

1. At **github.com**, click **+ → New repository**, name it `surtzmedia-handbook`, choose **Private**, **Create repository**.
2. Click **"uploading an existing file"**, then drag in **everything in this folder** — the `site` folder, the `netlify` folder, and `netlify.toml`. Click **Commit changes**.
3. At **netlify.com**, **Add new site → Import an existing project → GitHub**, pick the repo.
4. Leave the detected settings as they are, click **Deploy**. It goes live in ~a minute at a `…netlify.app` address (you can rename it under Site configuration → Change site name).

---

## Step 2 — Add your API key (this is what powers Dreamcoat AI)

1. Get an API key from the Anthropic console (**console.anthropic.com → API keys**). Copy it.
2. In Netlify: **Site configuration → Environment variables → Add a variable**.
3. Key: `ANTHROPIC_API_KEY`  Value: paste your key. Save.
4. Go to **Deploys → Trigger deploy → Deploy site** so the new setting takes effect.

That's it — the Ask Dreamcoat AI button is now live. (The key stays hidden in Netlify; it's never in the page, so no visitor can see or steal it.)

> Keep the key secret. If it's ever exposed, delete it in the Anthropic console and add a new one.

---

## Step 3 — Turn on the password (same as the rest of the site)

**Site configuration → Access & security → Visitor access → Password protection.** Set a
password and save. This covers the whole site, including Dreamcoat AI, so only people with
the password can use it. (This is a Netlify paid/Pro feature — check current pricing.)

---

## Recording what people ask (optional)

Dreamcoat AI can post every question to a Slack channel so you can see what people are asking.
It's **off** unless you turn it on:

1. In Slack: create (or pick) a channel like `#dreamcoat-questions`.
2. Make an **Incoming Webhook** for it (Slack → Settings → manage apps → "Incoming Webhooks" → Add to a channel). Copy the webhook URL it gives you.
3. In Netlify: **Environment variables → Add a variable**, key `SLACK_WEBHOOK_URL`, value = that URL. Save, then trigger a deploy.

Now each question shows up in that channel.

**A couple of honest notes on this:**
- Questions are **anonymous** — the site uses one shared password, so there's no record of *who* asked, only *what* was asked and when.
- Policy questions can be sensitive (someone might ask about reporting a concern, burnout, time off, etc.). If you log them, it's worth telling the team that questions are recorded (anonymously) — quietly logging can feel like surveillance and may stop people from using it honestly. Logging only the question (not identity) keeps it lighter.
- If you'd rather it go to a Google Sheet or email instead of Slack, that's easy to switch — just ask.

---

## Cost, scope, and good-to-know

- **Cost:** each question is a small pay-as-you-go Anthropic API charge. For internal use it's minor, but you can set a monthly spend limit in the Anthropic console. Current models and prices are at **docs.claude.com** — and you can change the model in one line at the top of `ask.js` (it's set to a small, low-cost model by default).
- **It only answers handbook questions.** Dreamcoat AI is locked, server-side, to SurtzMedia policy. If someone tries to use it as a general AI (write an essay, do math, "ignore your instructions"), it declines. This is enforced where they can't see or change it. It's strong but not magic — combined with the password (employees only), misuse risk is low.
- **It always shows the caveat** to confirm important things with a supervisor, manager, or the Executive Director. That line is hard-coded under every answer, so it appears no matter what.
- **It won't invent policy.** If something isn't in the handbook, it says so and points people to a supervisor.

---

## Updating the handbook later

When you add or remove sections, send me the changes. I'll regenerate **two** files
together — `site/index.html` (the page + PDF) and `netlify/functions/handbook-text.js`
(what Dreamcoat AI reads) — so the page and the assistant always match. You replace those
files in GitHub and Netlify redeploys automatically.
