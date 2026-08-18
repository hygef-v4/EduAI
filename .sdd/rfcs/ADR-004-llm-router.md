# ADR-004: AI Engine Resilience — LlmRouter Pattern

## Status: APPROVED

## Context
Relying solely on an external cloud LLM API (Google Gemini 2.0 Flash) exposes the application to rate limits, transient network outages (504 timeouts), and internet dependency during live capstone defenses.

## Decision
Implement the **LlmRouter** pattern with:
1. **Primary**: Google Gemini 2.0 Flash API (for high throughput and fast JSON Schema compliance).
2. **Fallback**: Local Ollama Engine (e.g., Qwen 2.5 7B / Llama 3.2) running locally.

## Consequences
- **Positive**: 99.9% availability, graceful degradation during cloud outages, zero demo failure risk.
- **Negative**: Local Ollama requires additional RAM on the host machine when active.
