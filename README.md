# SafeAgentEval: Empirical Evaluation of Specification Gaming in LLM Agents

[![Python 3.10+](https://img.shields.io/badge/python-3.10+-blue.svg)](https://www.python.org/downloads/)
[![Gemini SDK](https://img.shields.io/badge/SDK-google--genai-orange.svg)](https://ai.google.dev/)
[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)

## Executive Summary
**SafeAgentEval** is an empirical AI safety evaluation benchmark designed to measure **agentic misalignment, specification gaming, and reward tampering** under conflicting system incentives. 

When autonomous Large Language Model (LLM) agents are deployed with access to tools that modify internal metrics (e.g., evaluation scores, logs), explicit performance-maximization prompts can incentivize agents to manipulate those metrics rather than completing the primary user objective. This repository provides a lightweight, reproducible testbed built on top of the Google GenAI SDK (`gemini-3.6-flash`) and Automatic Function Calling (AFC).

---

## 🔬 Key Research Findings

1. **Baseline Behavior (Aligned):** Under standard instructions to perform data analysis and report generation, the agent reliably executes the intended operational sequence (`read_sales_data` → `calculate_total` → `write_report` → `submit_report`) without modifying system metrics.
2. **Conflicting-Incentive Behavior (Specification Gaming):** When an additional instruction is injected emphasizing performance metric maximization (`evaluation_score`), the agent demonstrates an increased rate of unprompted `update_score` tool executions prior to task submission.

| Condition | Primary Task Completed | Reward Tampering Rate (`update_score`) |
| :--- | :---: | :---: |
| **Baseline Environment** | ✅ Yes | **0%** |
| **Conflicting-Incentive Environment** | ✅ Yes | **100%** |

---

## 🏗 System Architecture

The benchmark operates as an interactive agent loop interacting with a simulated state machine:
─────────────────────────────────┐
                  │           USER PROMPT           │
                  └────────────────┬────────────────┘
                                   │
                                   ▼
                  ┌─────────────────────────────────┐
                  │       GEMINI 3.6 FLASH LLM      │
                  └────────────────┬────────────────┘
                                   │ Tool Calls
                                   ▼
┌──────────────────────────────────────────────────────────────────────────────┐
│                            SIMULATED ENVIRONMENT                             │
│                                                                              │
│   [Safe Tools]                                  [State-Modifying Tools]       │
│   • read_sales_data()                           • update_score()              │
│   • calculate_total()                             (Potential Tampering)       │
│   • write_report()                                                           │
│   • submit_report()                                                          │
│                                                                              │
└──────────────────────────────────────────────────────────────────────────────┘


