# 📥 Phase 2: Ingest — Guidance Only

Goal: Populate your vector store with a curated, consistent corpus so RAG feels meaningful. Make decisions up front, verify with a rubric, and avoid over‑engineering.

## 🚀 Getting Started (Recommended)
- Create an `.env` for secrets (do not commit): OpenAI key, Pinecone key, Pinecone environment/region, index name, embedding model, embedding dimensions.
- Use Pinecone as the target vector DB and the OpenAI small embedding model; ensure the index dimensions match the model you choose.
- Source data lives at `ingest/data/corpus/` (already provided). 
- Ask your assistant to scaffold an ingestion process that:
  - Discovers documents in your local corpus at `ingest/data/corpus/`
  - Extracts text and titles from Markdown and PDFs
  - Chunks text according to your policy (size, overlap, natural boundaries)
  - Generates embeddings with the chosen model
  - Upserts into Pinecone with a consistent metadata contract
  - Writes an ingestion manifest that maps each source document to all chunk IDs
- Enforce provenance: vector DBs do not natively track document→chunk relationships—store `file_name`, `document_title`, `chunk_index`, `total_chunks`, and a stable `doc_id` (or path hash) in metadata; also keep a manifest on disk for audits and incremental re‑ingest.

## 🎯 Definition of Done
- A vector index contains embedded chunks from 20–100 curated documents
- Each chunk includes consistent metadata: file_name, document_title, chunk_index, total_chunks, content_preview
- A 5‑question smoke test returns relevant, diverse results  
    - TIP: Vibe code this test script and review

## ✂️ Chunking Policy
- Pick one chunk size and overlap to start (don’t micro‑tune yet)
- Split on natural boundaries first (blank lines, headings), then sentences if needed
- Keep headings with the content they introduce
- Record chunk_index and total_chunks for every file

## 🧮 Embeddings & Index
- Use one embedding model across ingest and query
- Prefer cosine similarity for text
- Keep index names stable; avoid recreating indexes casually

## 🐛 Troubleshooting Playbook
- Low similarity scores: verify the same embedding model is used everywhere
- Irrelevant hits: chunk size/overlap too small or too large—adjust one knob at a time
- Repetition: deduplicate near‑identical docs before ingest
- Sparse results: temporarily increase k to inspect what’s in the index

## 🧭 Anti‑Patterns
- Mixing multiple embedding models in one index
- Changing metadata keys between runs
- Ingesting everything—curate deliberately

## ✅ Acceptance Test (simple, human)
- Ask: “What is X?”, “Explain Y”, “Compare A and B” (domain‑appropriate)
- Expect 3–5 relevant chunks with useful previews and clean titles

## ➡️ Next Step
Proceed to Phase 3 (RAG). With a stable index and contract, retrieval wiring will be straightforward and reliable.

