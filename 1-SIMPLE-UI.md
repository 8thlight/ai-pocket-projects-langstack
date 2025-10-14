# 🎨 Phase 1: One‑Hour UI Sprint (Guidance Only)

Goal: Ship a minimal chat UI in 60 minutes. No libraries, no polish—just a working loop you can iterate on. Paste a screenshot into Cursor at the end for critique.

## 🚀 Getting Started (Recommended)
- Use a UI generator like v0 to draft a basic chat layout (messages list, fixed input at bottom, light/dark friendly). Keep it minimal.
- Take a full‑page screenshot and paste it into Cursor.
- Ask Cursor: “Recreate this UI with mock data that streams tokens and emits a routing ‘step’ event (simple|research). No backend yet—just behaviors to test the UX.”
- Iterate quickly on spacing, alignment, and readability before wiring any real endpoints.
- Add basic Markdown rendering for assistant messages (headings, links, lists, inline/code blocks).

## 🎯 Definition of Done
- One-page chat layout: messages list + fixed input at bottom
- Send a message → receive a streaming response (mock or real)
- Assistant messages render Markdown (bold/italic, lists, links, inline/code blocks)
- Mode badge clearly displays the action taken (Chat or Deep Research); read‑only; auto‑updates on routing step events
- “Sources” list appears under assistant replies: show title + URL per source (optional snippet); collapse if more than 3

## 🧭 Guardrails
- Minimal styling: system font, comfortable spacing, readable colors
- Basic accessibility: label the input, visible focus, sufficient contrast
- Sanitize and safely render Markdown/links to avoid XSS; open external links thoughtfully

## 🧪 Quick Self‑Test
- Auto‑scroll only when you are at the bottom
- Mode badge toggles correctly on “step” event
- Markdown renders correctly (headings, links, lists, code blocks); links behave safely
- “Sources” stays attached to the correct assistant reply

## ➡️ Next Step
Move to Phase 2 (Ingest) to load real data. The UI becomes meaningful once your knowledge base is indexed.

