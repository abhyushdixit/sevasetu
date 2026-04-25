# SevaGrid AI — The Intelligence Heart of SevaSetu

## Overview

SevaSetu currently has a polished UI with static/simulated AI behavior. The goal is to embed a real AI engine powered by the **Google Gemini API** that acts as the living brain of the entire platform — processing voice inputs, generating crisis analysis, recommending resource allocation, and providing an interactive AI Command Center.

The AI will be called **SevaGrid AI** and will be deeply integrated across all four existing tabs plus a dedicated new AI Command Center tab.

---

## Proposed Architecture

**SevaGrid AI Engine (gemini-2.0-flash)**

│

├── **Voice-to-Task Module** → Real transcription + Gemini task generation

├── **Crisis Intelligence Module** → AI analysis of alert patterns

├── **Resource Optimizer** → AI-driven allocation recommendations

├── **SevaGrid Command Center** → Free-form AI chatbot for crisis coordinators

└── **Predictive Risk Engine** → AI risk scoring for live zones




> All calls go directly to the Gemini REST API (`generativelanguage.googleapis.com`) using a user-provided API key — no backend server needed, runs 100% in the browser.

---

## Open Questions

### **IMPORTANT**

- **API Key:** The AI features will prompt the user to enter their key on first load via a secure key modal. The key is stored in `sessionStorage` only (never persisted to disk).

### **NOTE**

- **Gemini Model:** Will use `gemini-2.0-flash` (fast, cheap, good for real-time crisis analysis). Upgrade to `gemini-1.5-pro` if you need longer context windows for complex reports.

---

## Proposed Changes

### **New: `sevaai.js` — SevaGrid AI Engine Core**

A dedicated AI engine module with the following capabilities:

#### **GeminiClient class**
- Manages API key, model selection, and raw fetch calls to Gemini REST API.
- Handles streaming responses for real-time feel.
- Error handling with graceful fallback to demo mode.

#### **SevaGridAI singleton**
Methods exposed:
- `analyzeVoice(transcript)` → Returns structured task list JSON.
- `analyzeCrisis(alerts)` → Returns crisis intelligence report.
- `optimizeResources(resources, crisisZones)` → Allocation recommendations.
- `chat(message, context)` → General AI command assistant.
- `predictRisk(zone)` → Risk score + reasoning for a crisis zone.

---

### **Modified: `index.html`**

- **API Key Modal:** Full-screen onboarding modal to enter Gemini API key (with "Demo Mode" fallback).
- **New nav item:** 🧠 SevaGrid AI tab with AI badge.
- **Voice-to-Task tab:** Replace simulated delay with real Gemini streaming call; show live token streaming.
- **AI status indicator:** Top bar shows AI connection status (Connected / Demo Mode).

---

### **New Tab: SevaGrid AI Command Center (`tab-ai`)**

A stunning, full-featured AI interface:

- 🧠 **AI Command Center:** Chat interface with the AI crisis coordinator.
  - Pre-seeded system prompt giving Gemini the role of a crisis coordinator.
  - Context-aware: fed live alert data + resource inventory on every call.
  - Markdown rendering of AI responses.
  - Suggested quick-action prompts ("Assess flood risk", "Optimize water distribution").

- 📊 **Crisis Intelligence Panel:** Auto-generated AI summary of the current situation.
  - Refreshes every 60s.
  - Shows: overall severity score, top 3 priority actions, predicted escalation zones.

- 🎯 **Resource Optimizer:** Click to get AI-powered allocation recommendations.
  - Analyzes current `RESOURCES` vs active `ALERTS`.

- ⚡ **Risk Predictor:** Per-zone AI risk scoring.
  - Select any crisis zone → AI provides 0-100 risk score + detailed reasoning.

---

### **Modified: `app.js`**

- `simulateVoice()` → calls `SevaGridAI.analyzeVoice()` with real Gemini response.
- Hook into `DOMContentLoaded` to initialize AI engine and update nav.

---

### **Modified: `style.css`**

- AI Command Center styles (chat bubbles, typing indicators, markdown prose).
- API key modal styles.
- AI tab styles with premium glassmorphism and gradient accents.
- Streaming text animation (typewriter cursor).

---

## Verification Plan

### **Automated**

1. Open `index.html` in browser via Live Server.
2. Enter a real Gemini API key in the modal.
3. Test all 5 AI functions:
   - Voice-to-Task with real Gemini response.
   - AI chat with crisis context.
   - Crisis intelligence report generation.
   - Resource optimizer.
   - Risk predictor per zone.

### **Demo Mode**

- Without API key → static fallback responses so the UI is never broken.
- Hackathon judges can interact without needing a key.

---

## Implementation Order

1. **`sevaai.js`** — AI engine core (`GeminiClient` + `SevaGridAI`).
2. **`index.html`** — API key modal + new AI tab HTML.
3. **`style.css`** — AI tab + modal + chat styles.
4. **`app.js`** — Wire real AI calls into existing Voice-to-Task + new tab init.
