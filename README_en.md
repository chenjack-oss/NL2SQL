# NL2SQL Data Query System

> Natural-language data querying service for data warehouses (Project showcase · Source code withheld due to enterprise data model confidentiality)

English | [简体中文](README.md)

## Overview

NL2SQL lets business users query a data warehouse in plain natural language — no SQL required. Built on LangGraph with a **12-node workflow** covering the full pipeline: *intent understanding → schema recall → SQL generation → validation & correction → result presentation*.

## Architecture

```
User question → Intent recognition → Table/Column recall (3-way parallel) → SQL generation
                     ↓                                        ↓
        Clarification follow-up (low confidence)      SQL syntax/column validation
                                                             ↓
                                                  Execute ──failure──→ LLM correction loop
                                                             ↓
                                              Result visualization + natural-language summary
```

## Key Techniques

- **12-node LangGraph workflow**: intent recognition, multi-turn clarification, SQL generation, execution, and correction are separate nodes with explicit state management between them.
- **Three-way parallel schema recall**: table-name recall / column recall / historical similar-question recall, improving schema localization accuracy.
- **SQL validation–correction loop**: syntax check + column-existence check; failures automatically regenerate SQL with the error message attached, significantly reducing hallucination.
- **SSE streaming output**: progress is pushed node by node, making generation observable in real time.

## Metrics & Results

Evaluated on a labeled dataset of **127 real business queries** (42 single-table / 85 multi-table JOIN):

| Metric | Result |
|---|---|
| SQL executability rate | **89%** (113 / 127) |
| SQL execution success rate | **86%** (109 / 127) |
| Routine reporting | Manual turnaround → **minute-level self-service query** |

- SQL statements that fail the `EXPLAIN` pre-check automatically enter the LLM correction loop; corrected statements re-enter the statistics.
- Multi-table JOIN cases (85 queries) were the main source of errors, continuously optimized by resolving JOIN relations via global `column_id` deduplication.
- Production uses a single correction round to avoid write-database risks; the code reserves room for extending to 3 correction rounds.

## Tech Stack

LangGraph · LangChain · DashScope (Qwen) · MySQL / Data Warehouse · FastAPI · SSE

## Notes

This repository is maintained as a project showcase page. For technical details or discussion, feel free to reach out: [Issues](https://github.com/chenjack-oss/NL2SQL/issues) / [e4823610812023@163.com](mailto:e4823610812023@163.com)
