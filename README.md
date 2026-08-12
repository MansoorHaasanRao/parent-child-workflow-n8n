# Parent & Child Workflow (n8n)

A demo of [n8n](https://n8n.io)'s **parent/child (sub-workflow) pattern**: a chat AI agent treats a separate workflow as a callable "tool" to fetch live weather data for any city.

## What it does

### Parent workflow (`parent-workflow.json`)
1. **When chat message received** — chat trigger for the user-facing conversation.
2. **AI Agent** (GPT-4.1) — a general assistant instructed to use the `weather` tool whenever the user asks about weather.
3. **weather** (Tool Workflow node) — calls the **child workflow** as a tool, passing the user's query.
4. **Simple Memory** — keeps short-term conversation context.

### Child workflow (`child-workflow.json`)
1. **When Executed by Another Workflow** — entry point invoked by the parent's `weather` tool call.
2. **Edit Fields** — extracts the `query` (city name) passed in.
3. **OpenWeatherMap** — fetches live weather data for that city.
4. **Message a model** (GPT-4.1) — converts the raw weather API response into a plain-text summary (temperature, conditions, humidity, etc.).
5. **Edit Fields1** — shapes the final text output returned back to the parent workflow.

## Architecture

```
Parent: Chat message → AI Agent (GPT-4.1) ── tool call ──▶ Child: city query → OpenWeatherMap → GPT-4.1 summary ──▶ back to AI Agent → reply
```

## Tech / Services used

| Component | Purpose |
|---|---|
| n8n | Workflow orchestration, parent/child sub-workflow tool calling |
| OpenAI GPT-4.1 | Chat agent reasoning + weather summary generation |
| OpenWeatherMap | Live weather data |

## Setup

1. Import both `parent-workflow.json` and `child-workflow.json` into the same n8n instance.
2. In the parent's **weather** node, re-select the child workflow (`workflowId`) once it exists in your instance, since IDs are instance-specific.
3. Configure credentials:
   - **OpenAI** — API key.
   - **OpenWeatherMap** — API key from [openweathermap.org](https://openweathermap.org/api).
4. Activate both workflows, then chat with the parent workflow and ask about the weather in any city.
