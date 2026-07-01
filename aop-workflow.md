# Sparrowhawk Automated Onboarding Pipeline - Master Blueprint

## Tools Used:

- **Retool:** The core automation engine, webhooks, and logic branching.
- **Retool Database (PostgreSQL):** To track state, decouple 1-to-many client entities, and calculate parallel setups.
- **Discord / CRM:** The front-end trigger for Sales to initiate the pipeline.
- **Fillout Forms:** Client data ingestion and questionnaire.
- **Google Calendar:** Kickoff and training call scheduling.
- **Gmail (SMTP):** Contextual, automated email dispatch.
- **Google Docs API:** To automatically generate meeting agendas and gap lists.

## Build Rules:

- Proceed step-by-step to ensure logic is flawless before moving to the next block.
- Build exclusively in Retool Workflows.
- All logic must be strictly tested in a simulated/unit-test environment before going live.

---

## 🟢 Phase 1: Live Architecture (Steps 1–6)

### Step 1: Webhook Ingestion & Database Routing

The automation catches a webhook payload (triggered via Discord or CRM) containing:

1. Client Name
2. Company / Legal Entity Name
3. (Optional) Additional Company / Legal Entity Name
4. Client Email Address
5. MSA Signed Status (Y/N)

_Architecture Note:_ If an additional company is present, the database dynamically splits the payload into two entirely isolated rows to track them in parallel without cross-contamination.

### Step 2: The Kickoff Gate

Once the database rows are created, the system sends the initial Welcome/Kickoff email.

_Architecture Note:_ The MSA verification logic gate is fully built but is currently bypassed/frozen (`true ||`) to reduce initial onboarding friction. It can be toggled on instantly when required.

### Step 3: The Daily Evaluation Engine

A scheduled Cron job wakes up daily and runs a SQL query (utilizing a `COUNT(*) OVER` window function) to evaluate the database. It checks every open client row to see if the Fillout form has been submitted and/or if a 45-minute kickoff call has been booked.

### Step 4: Path 2 — Booked Call, Missing Form

If the system detects the kickoff call is scheduled but the form is missing, it isolates that row and routes it down Path 2. It sends a contextual prep reminder asking the client to fill out the form prior to the call.
_(Includes dynamic injection of the specific company name and a parallel-setup disclaimer if the client has multiple entities)._

### Step 5: Path 3 — Form Submitted, Missing Call

If the system detects the form is submitted but no call is scheduled, it routes the row down Path 3. It sends an email thanking them for the form and prompting them to book a time on the calendar.
_(Includes dynamic company name injection and parallel-setup disclaimer)._

### Step 6: Path 1 — The Stale Check (Missing Both)

If neither action has been taken, the system evaluates the timestamp. Using Modulo math, it ensures a general check-in reminder to complete both actions is sent **strictly every 48 hours** to prevent daily client spam/fatigue.

---

## 🗺️ Phase 2: V2 Roadmap & Build Plan (Steps 7–12)

### Step 7: The Bouncer & Confirmation

Once both the form is submitted and the kickoff call is scheduled, the daily engine stops sending reminders. The system should automatically send a final "Thank You / Confirmation" email outlining what to expect next. _(Template needed)._

### Step 8: Automated Gap Analysis (The Diff Engine)

Upon receipt of the Fillout form webhook, the system must cross-reference the client's answers against the Sparrowhawk Master Checklist. It will automatically categorize the data into "Completed" vs. "Gaps," push this data to the Google Docs API, and generate a formatted Kickoff Agenda for the CS team to review prior to the call.

### Step 9: Post-Kickoff Recap

After the onboarding call is completed, push a recap email containing the call notes to the client. This email must include a "pre-build confirmation" that pulls in specific validated data from the call. _(Template ready)._

### Step 10: "You Are Live" & Training Scheduling

Triggered by a manual action (e.g., via Discord) confirming the backend workspace and channels are fully built. The automation will send an email informing the client their account is live, providing login details, and prompting them to schedule their mandatory Team Training Call. _(Template ready)._

### Step 11: Weekly Adoption Check-in

Initiate an automated weekly check-in cadence via email during the first 30 days to clear blockers and confirm initial value. _(Template ready)._

### Step 12: The 30-Day Milestone

At the exact 30-day mark, trigger a specific email asking the client to review their success criteria against the initial targets and prompting them to schedule a 30-day review call. _(Template ready)._
