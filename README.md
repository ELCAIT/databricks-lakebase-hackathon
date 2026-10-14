<div align="center">

<img src="./logos/primary-lockup-full-color-rgb.svg#gh-light-mode-only" alt="Databricks" height="56"><img src="./logos/primary-lockup-full-color-white-rgb.svg#gh-dark-mode-only" alt="Databricks" height="56">
&nbsp;&nbsp;&nbsp;&nbsp;✕&nbsp;&nbsp;&nbsp;&nbsp;
<img src="./logos/ELCA_engineering_RVB_positive_Black.png#gh-light-mode-only" alt="ELCA Engineering" height="56"><img src="./logos/ELCA_engineering_logo_white.png#gh-dark-mode-only" alt="ELCA Engineering" height="56">

# 🚀 Hackathon Ramp-Up Guide
### Databricks Apps × Lakebase

**Build a full-stack, data-driven app on Databricks, fast.**

*Handed to you by your ELCA Solution Architect, Databricks Champion.
Grab us anytime during the hackathon, that's what we're here for.* 🙋

[The Stack](#-what-youre-building-with) · [🏎️ Fast Lane (Claude Code)](#️-track-a-fast-lane-you-have-claude-code-or-another-coding-agent) · [🧗 Step by Step](#-track-b-step-by-step-the-guided-sequence) · [Links](#-helpful-links)

</div>

---

## 🎯 What you're building with

| Building block | What it gives you |
|---|---|
| **Databricks Apps** | Serverless hosting for web apps (Streamlit, Dash, Gradio, Flask, Shiny, Node.js/React) inside your workspace. Auth, SSO and governance included, zero infra to manage |
| **Lakebase** | Fully managed **Postgres** on Databricks, the OLTP backend for your app. Autoscaling, scale-to-zero, instant Git-style **branching**, and native Unity Catalog sync to/from Delta |

---

## 🏎️ Track A: Fast lane (you have Claude Code or another coding agent)

If you're running **Claude Code**, Cursor, Codex or a similar coding agent, skip the manual steps entirely: Databricks DevHub ships **agent-ready templates** for exactly this stack:

### ➡️ [developers.databricks.com/templates](https://developers.databricks.com/templates)

**How it works:**
1. Open a template. For the hackathon, **[App with Lakebase](https://developers.databricks.com/templates/app-with-lakebase)** is your one-shot: it provisions the Lakebase Postgres project *and* scaffolds a full app with schema setup and CRUD API routes
2. Click **Copy prompt** and paste it into Claude Code
3. The agent builds everything end-to-end, asking you questions along the way (app name, workspace profile, …)
4. You're deployed. 🏁

> ⚠️ **One prerequisite still applies:** your local machine needs an authenticated Databricks CLI (Track B, Step 1). Run that template first. It's the strict prerequisite for every other one. Pro tip: also run **[Onboard Your Coding Agent](https://developers.databricks.com/templates/onboard-your-coding-agent)** so Claude Code gets the Databricks agent skills, DevHub Docs MCP server and an `AGENTS.md` for your repo.

---

## 🧗 Track B: Step by step, the guided sequence

No coding agent? No problem. Do these **in order**: each step is a prerequisite for the next one.

### Step 1: Set up your local dev environment

1. Install the **Databricks CLI `v1.0.0+`**: Homebrew or curl installer on macOS/Linux, `winget` on Windows (prefer WSL for the curl path)
2. Authenticate against the hackathon workspace: `databricks auth login --host https://<workspace-url>` (have the URL from your invite email ready)
3. Verify the handshake:
   ```bash
   databricks auth profiles        # your profile must show Valid: YES
   databricks current-user me      # smoke test, must return your identity
   ```

✅ **Done when:** the CLI is on `PATH`, your profile is valid, and `current-user me` returns who you are. Every later step depends on this.

### Step 2: Create your Lakebase instance

1. Pre-flight check that Lakebase is enabled for you: `databricks postgres list-projects` must succeed (an empty list is fine, while a `not enabled`/permission error means ping your workspace admin or an ELCA architect)
2. Provision a **Lakebase Postgres project**, via CLI, or in the UI: **app switcher → Lakebase Postgres → Autoscaling → New project**
3. You get a **production branch** with an active endpoint and a default database (scale-to-zero compute included)
4. 📝 **Collect and note the connection values**: host, endpoint path, database path, database name. Step 3 needs them.

✅ **Done when:** a managed Postgres cluster is running in the workspace and you have the connection values written down.

### Step 3: Set up the application

1. Scaffold an app wired to your database from Step 2:
   ```bash
   databricks apps init --name my-app --features lakebase \
     --set lakebase.postgres.project=projects/<project-id> \
     --set lakebase.postgres.branch=projects/<project-id>/branches/<branch-id> \
     --set lakebase.postgres.database=projects/<project-id>/branches/<branch-id>/databases/<database-id>
   ```
   All three `lakebase.postgres.*` fields must be set together, or `init` fails with `missing fields [postgres.project]`.

   ⚠️ Use the exact resource paths, not the names shown in the UI. Get them from `databricks postgres list-databases projects/<project-id>/branches/<branch-id>` and copy the `name` field verbatim. The database's resource id (e.g. `databricks-postgres`, hyphen) often differs from its actual Postgres name (`databricks_postgres`, underscore, what the UI and connection string show). Using the underscore form in the path fails with `Database ... does not exist in postgres branch ... (404 NOT_FOUND)`.
2. **Deploy first** (`databricks apps deploy`) so the schema is created under the *app's* managed identity, not your personal one (this is the #1 gotcha!)
3. Then iterate locally: `npm run dev`
4. The template gives you schema initialization and **full CRUD API routes** out of the box, so your data persistence layer is done

✅ **Done when:** the app has a live URL in the workspace and reads/writes to Lakebase.

### Step 4: Start vibing 🎧
```bash
# hack hack hack...
databricks apps deploy    # ship it, early and often
```
Build your idea on top: add tables, hook up Genie or Model Serving, style the frontend, and redeploy every time something works.

> 💡 **Branch your database like Git**: create an isolated Lakebase branch per feature/experiment, then throw it away after. Great for demo resets.

---

## 🔗 Helpful links

**Start here**
- 📘 [Databricks Apps: Get started](https://docs.databricks.com/aws/en/dev-tools/databricks-apps/get-started)
- 📘 [Lakebase: Get a Postgres database](https://docs.databricks.com/aws/en/oltp/projects/get-started)
- 📘 [Add Lakebase as an app resource](https://docs.databricks.com/aws/en/dev-tools/databricks-apps/lakebase)

**Templates & examples**
- 🧩 [DevHub template gallery](https://developers.databricks.com/templates): every template is a copy-ready prompt for Claude Code & friends
- 🧩 [Set Up Your Local Dev Environment](https://developers.databricks.com/templates/set-up-your-local-dev-environment) · [Create a Lakebase Instance](https://developers.databricks.com/templates/lakebase-create-instance) · [App with Lakebase](https://developers.databricks.com/templates/app-with-lakebase)
- 🧩 [Lakebase quickstart](https://developers.databricks.com/docs/lakebase/quickstart)
- 🧩 [github.com/databricks/app-templates](https://github.com/databricks/app-templates): incl. full-stack Node.js + Lakebase CRUD examples

**Reference**
- 📗 [Lakebase Postgres overview](https://docs.databricks.com/aws/en/oltp/projects/)
- 📗 [Databricks Apps on Azure](https://learn.microsoft.com/en-us/azure/databricks/dev-tools/databricks-apps/get-started)
- 📗 [Databricks Apps on AWS](https://docs.databricks.com/aws/en/dev-tools/databricks-apps/get-started)
- 📗 [Lakebase product page](https://www.databricks.com/product/lakebase)

---

## 🧭 Rules of thumb from your ELCA architects

1. **Deploy early, deploy often.** Get the "hello world" URL in the first minutes. Demos die on last-minute deploys.
2. **Deploy before running locally** when using Lakebase, so the schema belongs to the app's identity, not yours.
3. **Stuck ?? > minutes? Ask us.** That's literally why ELCA is here. 🙋

---

<div align="center">

<img src="./logos/ELCA_engineering_RVB_positive_Black.png#gh-light-mode-only" alt="ELCA Engineering" height="36"><img src="./logos/ELCA_engineering_logo_white.png#gh-dark-mode-only" alt="ELCA Engineering" height="36">

**ELCA** · We make it work! · [elca.ch](https://www.elca.ch)

*in partnership with*

<img src="./logos/primary-lockup-full-color-rgb.svg#gh-light-mode-only" alt="Databricks" height="32"><img src="./logos/primary-lockup-full-color-white-rgb.svg#gh-dark-mode-only" alt="Databricks" height="32">

**Databricks** · The Data + AI Company · [databricks.com](https://www.databricks.com)

*Happy hacking! 🧱*

</div>
