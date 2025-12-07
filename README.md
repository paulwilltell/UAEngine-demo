# UAEngine — Adaptive Interaction Intelligence for AI

> "No more generic answers. UAEngine analyzes how people write, then shapes how AI responds."

---

## What is UAEngine?

UAEngine is a lightweight interaction-intelligence layer that sits **between the user and your model**.

It doesn't change model weights.  
It doesn't do emotion diagnosis.  
Instead, it analyzes **how** a user writes and turns that into **structured signals** your AI can act on:

- Pacing and rhythm (fast / slow / overloaded)
- Punctuation patterns (`???`, `!!!`, trailing off, etc.)
- Message density and complexity (cognitive load)
- Likely follow-up intent
- Preferred response structure (code-first, checklist, narrative, tldr, etc.)

You plug UAEngine into your stack, and it tells your AI:

> "Right now this user needs: slow, step-by-step, gentle tone, shallow depth"  
> or  
> "This user wants: fast, high-level, expert mode, skip the basics"

---

## Live Demo & Dashboard

- **Product Overview:** https://paulwilltell.github.io/UAEngine-demo/  
- **Live Dashboard Demo:** https://paulwilltell.github.io/UAEngine-demo/dashboard.html  

The hosted demo shows:

- Real-time signal extraction from sample messages  
- Cognitive load scores over time  
- Prediction accuracy for likely next questions  
- Adaptive response formats chosen based on signals

> Note: The current dashboard uses simulated data to showcase the engine's capabilities.

---

## Core Signal Engines

UAEngine currently exposes **5 main analysis modules**, which you can combine or use independently.

### 1. MEP — Micro-Expression Parser

**What it does**

Turns punctuation and formatting into usable context:

- Distinguishes `???` (confusion) from `!!!` (intensity)  
- Detects heavy caps usage, repetition, trailing ellipses  
- Flags likely frustration vs enthusiasm based on patterns — *purely from text*

**Example output**

```json
{
  "IsFrustrated": true,
  "CapsRatio": 0.76,
  "QuestionCluster": 3,
  "Markers": ["multi_question", "caps", "triple_question"]
}
```

### 2. CLE — Cognitive Load Estimator

**What it does**

Estimates how mentally loaded the user is based on:

- Sentence length and density
- Number of ideas per message
- Nested questions and branching
- Time between turns (when available)

**Example output**

```json
{
  "Score": 0.92,
  "Level": "overloaded",
  "RecommendedAction": "simplify_now"
}
```

### 3. TSD — Temporal Signal Decay

**What it does**

Tracks how signals evolve across a conversation:

- Early curiosity → later frustration
- Confidence → uncertainty
- Idle → heavy engagement

Useful for:

- Detecting when you need to reset, recap, or slow down
- Identifying "drift" in tone and comprehension

### 4. PIB — Predictive Intent Branching

**What it does**

Predicts likely next user moves as ranked options:

```json
{
  "Predictions": [
    { "Intent": "show_error", "Probability": 0.92 },
    { "Intent": "ask_for_code_example", "Probability": 0.85 },
    { "Intent": "simplify_explanation", "Probability": 0.48 }
  ]
}
```

You can use this to:

- Pre-load context
- Proactively ask "Do you want A or B?"
- Design UI that's one click ahead of the user

### 5. ARM — Adaptive Response Morphology

**What it does**

Chooses the shape of the response:

- `code_first` — show code, then explanation
- `tldr_expand` — short answer first, then deeper layers
- `socratic` — guided questions for learners
- `checklist` — step-by-step tasks
- `narrative` — story / conceptual explanation
- `bullet_storm` — rapid-fire bullet responses

ARM doesn't generate the content itself.  
It tells your model: "Format the answer like this."

---

## Example Flow

User says:

> "OK slow down and do one step at a time, don't jump."

UAEngine returns something like:

```json
{
  "PacingOverride": "slow",
  "DepthLevel": 1,
  "InstructionMode": "step_by_step",
  "PreferredMorphology": "checklist"
}
```

Your app builds a system prompt such as:

> "Explain this as a slow, step-by-step checklist, minimal jargon, depth level 1."

Model responds in that structure automatically.

---

## Integration Sketch

⚠️ API and SDK surface is evolving. This is a conceptual example.

```javascript
// 1. Send user message to UAEngine
const signals = await fetch("https://api.uaengine.dev/analyze", {
  method: "POST",
  headers: { "Content-Type": "application/json", "Authorization": "Bearer YOUR_KEY" },
  body: JSON.stringify({
    message: userMessage,
    conversationId: sessionId
  })
}).then(r => r.json());

// 2. Build system prompt from signals
const systemPrompt = buildAdaptivePrompt(signals, {
  baseRole: "helpful assistant",
  domain: "programming"
});

// 3. Call your LLM
const completion = await openai.chat.completions.create({
  model: "gpt-4.1",
  messages: [
    { role: "system", content: systemPrompt },
    { role: "user", content: userMessage }
  ]
});
```

---

## Use Cases

- **Developer Tools** — detect when a dev is stuck vs just exploring
- **Customer Support** — prioritize frustrated or overloaded tickets
- **EdTech** — adapt explanation depth and pacing per learner
- **AI Agents** — choose actions based on cognitive signals, not just text content
- **General UX** — make AI feel less like a static bot and more like an adaptive partner

---

## Project Status

- ✅ UX + concept design complete
- ✅ Signal taxonomy and heuristics designed
- ✅ Live demo & dashboard prototypes online
- 🚧 API & SDK hardening in progress
- 🚧 Deeper language-model-driven signal refinement

**Roadmap:**

- Add structured JSON schema for all signal types
- Provide TypeScript + Python SDKs
- Offer local-only mode for privacy-focused deployments
- Benchmark effect on user satisfaction / task completion

---

## 📧 Contact

**Paul Timchuk**  
Email: paul@uaengine.ai

---

© 2025 UAEngine. All rights reserved.
