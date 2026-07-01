# 🦅 Sparrowhawk Automated Onboarding Pipeline

A fully decoupled, self-healing automated pipeline built in Retool to manage multi-entity client onboardings, prevent communication fatigue, and eliminate manual data entry.

## 🛠 Tech Stack & Integrations
* **Core Engine:** Retool Workflows & Retool Database (PostgreSQL)
* **Ingestion:** Webhooks (Discord / CRM / Fillout Forms)
* **Comms:** Gmail (SMTP HTML Templates)
* **Scheduling:** Google Calendar
* **Documentation:** Google Docs API

---

## 🏗 Architecture Overview (V1 Live)

The V1 architecture is currently in production and consists of two primary Retool Workflows operating independently to ensure scale and fault tolerance.

### 1. The Kickoff Engine (Ingestion Webhook)
A webhook catcher designed to safely ingest incoming Go-To-Market (GTM) handoffs.
* **1-to-Many Entity Splitting:** If a payload contains multiple legal entities for a single client, the engine dynamically forks the data and creates isolated database rows to track each entity independently.
* **MSA Validation Bypass:** The logic gate checking for executed MSAs is built but currently set to `true ||` to reduce initial friction. 

### 2. The Daily Checker (Cron & Logic Loop)
A daily scheduled workflow that acts as the "brain" of the pipeline. It reads the database, recalculates math, and routes communications.
* **Self-Resolving States:** Uses a `COUNT(*) OVER` SQL window function to natively recalculate how many open entities belong to a single client email, passing that integer to the logic loop.
* **Unit-Tested Routing Gates:** Evaluates every entity in complete isolation to prevent cross-contamination.
  * **Path 1 (Stale Check):** Uses Modulo math (`% 2`) to restrict general follow-up emails to a strict 48-hour cadence, eliminating spam.
  * **Path 2 (Missing Form):** Identifies booked calls missing a form; pushes a localized, entity-specific prep email.
  * **Path 3 (Missing Call):** Identifies submitted forms missing a call; pushes a localized scheduling link.
* **Dynamic Conditional Rendering:** Inline JavaScript ternary operators automatically inject a "parallel setup" disclaimer into HTML emails *only* if the SQL query detects the client is actively onboarding 2+ entities.

---

## 🗺️ V2 Roadmap (In Development)

* **Zero-Touch Gap Analysis:** Transitioning to real-time Fillout webhooks. 
* **The "Diff Engine":** A planned Retool JS function that will cross-reference a client's Fillout payload against our Master Checklist CSV to automatically categorize data into `Completed Validations` and `Flagged Gaps`.
* **Automated Agenda Generation:** Pushing Diff Engine arrays to the Google Docs API to auto-generate standardized Kickoff Agendas for the CS team prior to the call.

---

## 🧰 Maintenance & Quick Actions

### Turning the MSA Gate Back On
If Legal/Compliance requires the MSA check to be strictly enforced before sending the Kickoff email:
1. Open the **Onboarding Kickoff** workflow.
2. Open the `branch1` block.
3. Change the IF condition from `{{ true || startTrigger.data.msa_signed === 'Y' }}` to `{{ startTrigger.data.msa_signed === 'Y' }}`.
4. Deploy the workflow.

### Updating Email Copy
All email templates are housed directly inside the SMTP blocks within the workflows.
* **Welcome Email:** Located in `Onboarding Kickoff`.
* **Reminder Emails (Paths 1, 2, 3):** Located inside `function1` within the `Daily Onboarding Checker` workflow. 
* *Note:* Do not alter the `{{ params.x }}` bracketed variables within the HTML unless you are also updating the loop parameter mapping on the main canvas.
