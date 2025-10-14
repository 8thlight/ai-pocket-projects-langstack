# 🔬 Phase 4: Research Workflows

Goal: Turn complex questions into rigorous multi‑step research with predictable outputs, while preserving speed where possible.

## 🎯 Definition of Done
- Planner consistently produces a brief, useful plan (subtopics + questions).
- Gatherer alternates KB + web, then explicitly declares readiness.
- Report Builder emits a well‑structured markdown report with inline citations and a references section.
- Loops are bounded; failures are surfaced clearly.
- Evaluations in place for routing prompt - 3 minimum
- Context compression: keep last ~10 turns with a running summary; per‑source summaries within a token budget
  - Keep last N turns verbatim (N≈8–12); fold older turns into a running summary
  - On overflow, update the summary and drop the oldest verbatim turn

## 🧱 Roles & Responsibilities
- Planner
  - Extract subtopics and key questions.
  - Identify which ones require web vs. KB.
  - Produce a minimal, actionable plan (not an essay).
- Gatherer (loop)
  - Follow the plan; collect KB first, then web.
  - Summarize findings briefly; call out gaps.
  - When coverage is sufficient, write: “RESEARCH COMPLETE — ready to build report”.
- Report Builder
  - Title, sections, inline citations ([KB‑X], [WEB‑X]), and a references section.
  - Synthesis over aggregation; avoid bullet dumps.

## 🔁 Loop Guardrails
- Soft cap on gatherer iterations (e.g., 3–5) with a short justification if exceeded.
- Prefer breadth over depth after the second pass (reduce duplicative searches).
- Fail fast if no sources are found after the first pass (signal to user).

## 🧠 Context Compression & Token Budgets
Keep the research context within model limits without losing traceability.

- Token management: avoid sending full history. Keep the last ~10 turns and roll older turns into a running summary that is included with the recent context.
- Large result sets: summarize retrieved content as per‑source notes before synthesis to prevent oversized contexts.

## 🧪 Routing Evaluation & Prompt Tuning
Routing is tricky—plan to iterate. I strongly advise you add a lightweight eval loop to tune the router:
- Start by vibing one for review - it really helps get there quickly
- Tune the prompt using concrete indicators (multi‑section asks, comparisons, “comprehensive/report/deep dive”, etc.).
- Return structured JSON from the router (e.g., decision, confidence, rationale, indicators) to reduce ambiguity and improve parsing.
- Treat ambiguous requests as simple by default unless strong research indicators are present.
- Re‑run evals after each prompt change

## 📌 Quality Bar for Reports
- Structure: Introduction, Core Concepts, Current State, Challenges, Future Trends, Applications, Conclusion, References.
- Citations: Inline for claims; references at end split by KB vs. Web.
- Substance: Explanatory paragraphs, not lists of quotes.
- Length: Aim for ~800+ words when asked for "comprehensive".

## ✅ Acceptance Tests
- It correcly produces a research report when asked or a simple chat experince. 
- The UI clearly indicates the type of action taken and sources are properly cited in the response. 

