# Skill Playbook: AI Essay Error Diagnostics — EduAI

> Senior engineering playbook for rubric-based subjective essay evaluation and 5-category error taxonomy classification.

---

## 1. The 5-Category Error Taxonomy Matrix

| Category | Diagnostic Trigger | Example Student Mistake | Remediation Strategy |
|---|---|---|---|
| `CONCEPTUAL_MISUNDERSTANDING` | Student misinterprets core theory/law | Confuses BFS with DFS in finding shortest path on unweighted graphs | Provide core concept diagram and textbook section citation |
| `CALCULATION_ERROR` | Correct formula, incorrect arithmetic | Wrote $2 \times 3.14 \times 5 = 30.4$ instead of $31.4$ | Highlight arithmetic step without penalizing conceptual rubric points |
| `MISREAD_QUESTION` | Answered unasked question or ignored constraints | Output in ascending order when prompt specified descending | Emphasize prompt constraints in feedback |
| `SYNTAX_ERROR` | Broken code/math notation | Missing closing bracket or invalid LaTeX syntax | Provide correct notation template |
| `INCOMPLETE_LOGIC` | Correct premise, missing intermediate proof step | Stated theorem without verifying base condition | Ask Socratic guiding question |

## 2. LLM Prompt Construction Guidelines
- Always provide the ground-truth reference chunk and the teacher's grading rubric.
- Enforce output in Draft-07 JSON Schema format with `score_awarded`, `feedback_text`, and `error_category`.
- Set LLM `temperature = 0.0` for deterministic, reproducible grading.
