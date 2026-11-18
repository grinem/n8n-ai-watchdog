# n8n AI & Webhook Silent-Failure Watchdog (Zero-Trust MIT)

Catch silent workflow crashes, empty AI Agent responses (`[]`), `429` Shopify/OpenAI rate-limit drops, and OAuth token deadlocks in n8n **before your clients do** — with **100% local secret scrubbing** and **zero external dependencies**.

> 🔍 Need to scan an existing n8n workflow export before shipping?
> Run our **[Free Client-Side Workflow Audit (0 bytes leave your browser)](https://hooktriage.com/#triage)** or browse **[100+ Real Production Post-Mortems](https://hooktriage.com/)**.

---

## 🛡️ Zero-Trust Security Audit (Inspect Before You Import)

Developers shouldn't blindly import random workflow JSON files. Here is everything inside [`n8n-silent-failure-watchdog.json`](./n8n-silent-failure-watchdog.json):

| Node | Type | What It Does | Network Calls |
| :--- | :--- | :--- | :--- |
| `Error Trigger` | `n8n-nodes-base.errorTrigger` | Captures unhandled node & workflow errors across any linked workflow | **None (0)** |
| `Zero-Trust Local Scrubber & Classifier` | `n8n-nodes-base.code` (JS) | Masks API keys (`sk_live_`, `ghp_`, `re_`, `whsec_`, `AIza`, AWS `AKIA`, JWTs, `Bearer`, emails) locally inside n8n and classifies common causes (`429`, `401/403`, timeouts, JSON drift) | **None (0)** |
| `Format Diagnostic Alert` | `n8n-nodes-base.set` | Outputs clean `alert_title` + `alert_message` fields for your Slack / Telegram / Discord / Email node | **None (0)** |

- **No obfuscated code** (plain readable JS)
- **No hardcoded credentials or third-party tracking webhooks**
- **MIT Licensed** — audit, fork, and adapt freely

---

## ⚡ 30-Second Installation

### Option 1: Copy Raw JSON (No Email Gate)
1. Open [`n8n-silent-failure-watchdog.json`](https://raw.githubusercontent.com/grinem/n8n-ai-watchdog/main/n8n-silent-failure-watchdog.json).
2. Copy the JSON (`Ctrl+A`, `Ctrl+C`).
3. Open your n8n canvas and paste (`Ctrl+V`), or click **Workflow menu -> Import from URL / File**.
4. Connect your **Slack / Telegram / Email** node to the right of `Format Diagnostic Alert`.
5. In your critical production workflows: **Settings -> Error Workflow -> select `n8n Silent-Failure & AI Agent Watchdog`**.

### Option 2: Catching "Green" Silent AI Failures (`[]` or malformed JSON)
n8n's native `Error Trigger` only fires on unhandled exceptions — **not** when an HTTP request returns `200 OK` with empty items or an LLM outputs malformed JSON:
1. Add an `IF` node checking `{{ $json.items?.length > 0 && !!$json.expected_field }}`.
2. On the `false` branch, add an **Execute Sub-Workflow** node calling your Watchdog workflow (or throw a structured error) so silent failures trigger the exact same scrubbed Slack/Telegram alert.

---

## 🏗️ How It Works

```text
[Any Production Workflow Error / Silent-Failure Trigger]
        │
        ▼
[1. Error Trigger] ──► [2. Zero-Trust Local Scrubber (JS)]
                              │  • Strips API keys, JWTs, Bearer tokens & emails locally
                              │  • Classifies 429 rate-limits, 401/403 OAuth loops, timeouts
                              ▼
                       [3. Format Diagnostic Alert (Set)]
                              │
                              ▼
                       [Your Slack / Telegram / PagerDuty / Email]
```

---

## 📚 Related Resources

- **[n8n & AI Automation Incident Fixes Knowledge Base](https://hooktriage.com/)** — 100+ verified root-cause post-mortems across `n8n`, `Dify`, `Flowise`, `Activepieces`, `Make`, and webhooks.
- **[Client-Side n8n Workflow Pre-Check & Async Audit](https://hooktriage.com/#triage)** — Instantly detect missing timeouts, missing retries, and unprotected error paths in your JSON.

## 📄 License

[MIT](./LICENSE) — free for commercial, agency, and self-hosted deployments.
