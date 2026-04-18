# CFPB Complaint Resolution Agent

An agentic AI system that ingests live complaints from the CFPB Consumer Complaint Database and produces a full resolution package (classification, compliance check, team routing, and a regulator-ready customer response) in roughly 15 seconds.

Built for the UMD Agentic AI Challenge 2026.

## The Problem

A national fintech company offering credit cards, loans, and digital banking faces a surge in customer complaints across multiple channels, including the public CFPB Consumer Complaint Database. Complaints are inconsistently categorized, manually routed, and often delayed, which increases regulatory risk and customer churn.

The goal of this project is to build an agentic AI system that automatically classifies complaints, identifies root causes, assigns the right internal teams, and generates a complete resolution plan, including remediation steps, regulatory-compliant customer responses, and preventive recommendations.

## How It Works

Six specialized agents work together in three stages.

**Stage 1 — Parallel analysis.** Four agents run concurrently against the raw complaint narrative:

- **Classifier** categorizes the complaint by product and severity on a 1 to 5 scale.
- **Compliance** flags regulatory risk against 10 key consumer financial regulations, including FCRA, FDCPA, TILA, RESPA, FCBA, ECOA, EFTA, Regulation CC, Regulation E, and UDAAP.
- **Root Cause** identifies the underlying operational, process, or policy failure that produced the complaint.
- **Sentiment** reads the consumer's emotional state, urgency level, and flags vulnerable-consumer indicators such as financial hardship, health issues, or threatened legal action.

**Stage 2 — Routing.** Once Stage 1 completes, the Router agent consumes all four outputs and assigns the complaint to the right internal team (for example, Billing Disputes, Mortgage Servicing, Fraud Investigation, or Executive Escalation) with a priority level of P1 through P4 and a corresponding SLA in hours.

**Stage 3 — Resolution.** The Resolution agent runs last with full context from all prior agents. It drafts remediation steps, a complete customer response letter, financial remediation recommendations, and preventive suggestions to address the root cause.

Every agent returns structured JSON with a fixed schema, so every decision is machine-parseable and auditable, which is critical for regulator-facing explainability.

## End-to-End Flow

1. The user either pastes a complaint narrative, loads one of three built-in sample complaints, or clicks **Fetch Live Complaint** to pull a real one from the CFPB database.
2. Clicking **Run Pipeline** kicks off the 6-agent workflow. The UI streams NDJSON events from the backend so each agent card animates and updates in real time as it finishes, rather than waiting for a black-box 15-second response.
3. When the pipeline completes, the user sees the full resolution package: product classification, severity, regulatory risk level, assigned team with SLA, a summary of the resolution approach, step-by-step remediation actions, and a draft customer response letter.
4. If the case meets any high-risk criteria (severity 4 or higher, HIGH or CRITICAL regulatory risk, or a legal-review flag from the Router), the system automatically blocks auto-send and displays a **Human Review Required** banner. A compliance officer must click **Mark Review Cleared · Unlock Send** before the email delivery controls become available.
5. Once cleared, the user enters a recipient email address and clicks **Send**. A branded HTML version of the resolution letter is delivered via Gmail SMTP in under 2 seconds.
6. Every completed run is logged to the **Processed This Session** feed so reviewers can see the history of cases handled during an evaluation session.

## Tech Stack

| Layer | Technology |
|-------|-----------|
| Frontend | Next.js 15, React 19, Tailwind v4, Motion for animations |
| AI | OpenAI GPT-4o-mini in JSON mode (temperature 0.2 to 0.3) |
| Data Source | CFPB Consumer Complaint Database API |
| Email Delivery | Gmail SMTP via nodemailer |
| No-code Workflow | n8n (parallel implementation of the same agent logic) |
| Deployment | Vercel |
