# Prompt Optimizer Studio

[![License: MIT](https://img.shields.io/badge/License-MIT-blue.svg)](https://opensource.org/licenses/MIT)

Browser-based editor for building structured LLM prompts using tagged blocks (role, task, context, constraints, code).  
The tool assembles the blocks into a final prompt and supports several research-verified formatting techniques that improve model behavior.

→ **Live demo**: https://yucheng-wei.github.io/Prompt-Optimizer-Studio/

---

## Core idea

Instead of writing one long prompt, you create small labeled blocks that are later wrapped in consistent XML-style tags (`<role>`, `<task>`, `<context>`, etc.).  
This structure makes it easier to reorder parts, apply presets, translate/optimize individual sections, and activate different reasoning scaffolds with toggles.

## Main features

- **Block editor** — add, reorder, delete blocks of different types  
  - `<role>` — system persona / instructions  
  - `<task>` — main instruction  
  - `<context>` — documents, data, examples  
  - `<constraints>` — rules, format requirements, guardrails  
  - `<code_context>` — source code snippets  
  - plain text fallback

- **One-click formatting strategies** (toggles)
  - **Prompt Repetition** — repeats every `<task>` block at the end → improves instruction following in non-reasoning settings without changing output length or latency  
  - **Chain-of-Thought variants** — appends different final instructions  
    - Classical: "Let's think step-by-step."  
    - Agentic: strict Plan → Check → Execute loop  
    - Chain-of-Table: table-first reasoning (data-heavy tasks)  
    - **Society of Thought**: simulates multi-perspective debate (detailed below)  
  - **Compact whitespace** — collapses multiple blank lines for cleaner token usage

- **AI helpers** (using your SiliconFlow API key)
  - Translate block to English (Hunyuan-MT-7B or similar)  
  - Rewrite block for clarity & structure (DeepSeek-R1-Distill or Qwen2.5-Coder)  
  - Auto-generate role from a task description

- Built-in presets  
  - Role examples: Coding Specialist, Academic Research Assistant, Senior Technical Writer, Society of Thought starter  
  - Constraint examples: strict grounding, no yapping, academic citation style

- Final output pane — live preview, copy, download as .txt or .md  
- Token & cost estimation (adjustable $/1M token rate)

Everything runs in the browser. No data leaves your machine except the API calls you explicitly trigger.

## Research techniques & measured effects

The tool exposes three techniques backed by recent papers. You can turn them on/off independently to compare results.

1. **Prompt Repetition**  
   Paper: Leviathan et al. (2025) — [arXiv:2512.14982](https://arxiv.org/abs/2512.14982)  
   Effect: Repeating the full input prompt (without reasoning / CoT) improves accuracy on many models (Gemini, GPT-4o-mini, Claude 3 Haiku/Sonnet, DeepSeek-V3) across benchmarks (ARC, MMLU-Pro, GSM8K, MATH, custom pattern tasks).  
   Win rate: 47/70 model-benchmark pairs, 0 losses (statistically significant in many cases).  
   Cost: zero extra generation tokens, zero measured latency increase in most setups.

2. **Chain-of-Thought (classic version)**  
   Paper: Kojima et al. (2022) — [arXiv:2205.11916](https://arxiv.org/abs/2205.11916)  
   Effect: Adding "Let's think step-by-step" (or variants) at the end dramatically lifts zero-shot / few-shot reasoning performance on arithmetic, commonsense, and symbolic tasks. Remains one of the simplest and most reliable baselines.

3. **Society of Thought** (main differentiator)  
   Paper: Kim et al. (2026) — [arXiv:2601.10825](https://arxiv.org/abs/2601.10825)  
   Core claim: Leading reasoning models (DeepSeek-R1, QwQ-32B, o-series style) achieve higher performance **not** just from longer thinking, but from implicitly simulating multi-agent debate — diverse internal perspectives with different personality traits (Big Five), expertise, and conflict/resolution patterns.  
   Measured effects:  
   - Reasoning models show significantly higher perspective/personality diversity than instruction-tuned baselines.  
   - Mechanistic interpretability reveals conversational dynamics (questioning, perspective shift, disagreement, reconciliation).  
   - RL experiments: rewarding only final accuracy causes base models to spontaneously increase conversational behaviors.  
   - Fine-tuning with explicit conversation scaffolding accelerates reasoning gains compared to monologue-style CoT training.  
   Tool implementation: fixed 3-persona debate scaffold (Meticulous Verifier, Creative Strategist, Skeptical Auditor) inside `<think>` tags, no fixed turn order, disagreement encouraged, consensus required before final answer.

## Tech stack

- Vue 3 (Composition API)  
- Tailwind CSS  
- Font Awesome  
- localStorage only (presets & API key)

## Contributing

Ideas / PRs welcome — especially additional paper-verified scaffolds (Tree-of-Thoughts, self-consistency, etc.) or better presets.

## License

MIT © 2026 Yu-Cheng Wei
