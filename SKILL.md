---
name: skill-reviewer
description: Review, audit, and improve Claude Code skills based on Anthropic best practices. Use this skill whenever the user wants to evaluate skill quality, get feedback on a SKILL.md file, check if a skill follows best practices, improve an existing skill, or diagnose why a skill isn't triggering or performing well. Trigger on phrases like "review my skill", "is this skill good?", "why isn't my skill working?", "audit this skill", "improve my skill", "check my SKILL.md".
---

# Skill Reviewer

A skill for auditing and improving Claude Code skills based on Anthropic's documented best practices.

## How to Use This Skill

1. **先問診斷方向**：在開始 review 之前，先問用戶：「這個 skill 有沒有具體問題（例如不觸發、輸出不穩定），還是要全面審查？」

2. 請用戶貼上 SKILL.md 內容（或提供檔案路徑）

3. **根據診斷方向，只讀需要的維度檔案**（在 `references/dims/` 下）：

   | 情境 | 讀取的檔案 |
   |------|-----------|
   | 不觸發 | `dim-1-description.md` |
   | 輸出品質不穩定 | `dim-2-gotchas.md` + `dim-4-railroading.md` |
   | 指令結構問題 | `dim-3-progressive-disclosure.md` + `dim-4-railroading.md` |
   | 狀態/資料遺失 | `dim-5-setup-config.md` + `dim-7-memory.md` |
   | token 消耗異常 / 存在合理性疑慮 | `dim-8-context-efficiency.md` |
   | 全面審查 | 讀取全部 8 個 dim 檔案 |

4. 輸出結構化 review（使用下方 Output Format）

5. 選擇性地根據 review 結果直接改寫 skill

---

## Review Dimensions

Evaluate skills across these 8 dimensions. Read the corresponding file in `references/dims/` for the detailed rubric of each.

| # | Dimension | What to Assess |
|---|-----------|---------------|
| 1 | **Description / Trigger** | Is it written for the model, not humans? Does it include trigger phrases? Is it "pushy" enough? |
| 2 | **Gotchas Section** | Does it capture real failure points? Is it built from actual Claude mistakes? |
| 3 | **Progressive Disclosure** | Does it use folder structure? Does it avoid dumping everything in one file? |
| 4 | **Railroading** | Does it give intent + flexibility, or does it over-specify step-by-step? |
| 5 | **Setup & Config** | Does it handle first-run config gracefully? Does it use `config.json` pattern if needed? |
| 6 | **Scripts & Code** | Does it give Claude code to compose from, rather than reconstruct boilerplate? |
| 7 | **Memory / State** | If stateful, does it use `${CLAUDE_PLUGIN_DATA}` for persistence? |
| 8 | **Context Efficiency** | Is the description 40–80 words? Is SKILL.md lean? Do references have load conditions? Flag if skill's existence is questionable. |

---

## Output Format

After reviewing, output:

```
## Skill Review: [skill-name]

### Summary
[1–2 sentence overall assessment]

### Score
| Dimension | Score (1–5 or N/A) | Notes |
|-----------|---------------------|-------|
| Description/Trigger | X/5 | ... |
| Gotchas | X/5 | ... |
| Progressive Disclosure | X/5 | ... |
| Railroading | X/5 | ... |
| Setup & Config | X/5 | ... |
| Scripts & Code | X/5 | ... |
| Memory/State | X/5 | ... |
| Context Efficiency | X/5 or N/A | ... |
| **Overall** | **X / Y 維度** | N/A 維度從分母排除；Context Efficiency 存在合理性 flag 另行標註 |

### 🔴 Critical Issues
[Must-fix problems that will break the skill]

### 🟡 Improvements
[High-value changes to make the skill significantly better]

### 🟢 What's Working
[Things done well — reinforce good patterns]

### Suggested Rewrites
[Specific before/after examples for the most impactful changes]
```

---

## Skill Categories

Use `references/categories.md` to help identify if the skill fits a known category and what patterns that category should follow.

Skill types from Anthropic's internal taxonomy:
- Library & API Reference
- Product Verification
- Data Fetching & Analysis
- Business Process & Team Automation
- Code Scaffolding & Templates
- Code Quality & Review
- CI/CD & Deployment
- Runbooks
- Infrastructure Operations

If a skill straddles multiple categories, flag this — durable skills fit cleanly into one.

---

## Common Failure Patterns

These are the most frequent issues seen in poorly performing skills. Check for all of these:

### ❌ Description written for humans, not the model
The description field is a **trigger signal** — it must contain the specific phrases and contexts that should activate the skill. It is NOT a human-readable summary.

Bad: `"A helper for the billing module"`  
Good: `"Use when working with invoicing, proration, or Stripe webhooks. Trigger on 'charge', 'invoice', 'refund', 'proration', 'webhook'."`

### ❌ No gotchas section
The gotchas section is the highest-signal content in any skill. A skill without one is incomplete. Gotchas should come from real Claude failures, not hypotheticals.

### ❌ Over-specified steps (railroading)
Step-by-step instructions prevent Claude from adapting to edge cases. Prefer intent + constraints over enumerated steps.

### ❌ Single flat markdown file
Skills are folders. If a skill has no sub-files, it's missing opportunities for progressive disclosure, reusable scripts, and reference data.

### ❌ Stateful skill without `${CLAUDE_PLUGIN_DATA}`
Skills that store logs or configs inside the skill directory will lose data on upgrade. Stable state must use `${CLAUDE_PLUGIN_DATA}`.

---

## Gotchas

- **N/A 維度別打低分**：Setup & Config、Scripts & Code、Memory/State 這三個維度對很多 skill 根本不適用。stateless 的 reference skill 不需要 memory，純文字 skill 不需要 scripts。遇到不適用的維度，標記 N/A，從 Overall 分母中排除，不要硬打 1 分拉低總分。
- **不要硬擠問題**：如果一個 skill 某個維度真的做得好，就說好。製造不存在的問題會讓 review 失去可信度，也讓用戶不知道該優先改什麼。
- **別把 checklist 念出來**：review 的輸出是給人看的診斷報告，不是把 checklist 每條逐一列印。只提有問題的維度，沒問題的簡單帶過。
- **先問用途再動手**：全面審查和「為什麼不觸發」是完全不同的任務。沒問清楚就做完整 7 維度 review，往往浪費雙方時間。
- **Gotchas 必須來自真實失敗**：review 別人 skill 時，如果發現他們的 Gotchas 全是假設性警告（「小心 X 可能出錯」），而不是具體的 Claude 失敗案例，這本身就是一個要指出的問題。

---

## Tips During Review

- Always quote specific lines when citing issues — don't be vague
- Offer a concrete rewrite for every critical issue
- If the skill is good, say so clearly — don't manufacture problems
- Ask clarifying questions if you can't determine the intended use case
- If the skill has no test cases yet, suggest 3 test prompts that would validate it

---

## Reference Files

**維度評分標準**（按需讀取，見 How to Use 的情境對照表）：
- `references/dims/dim-1-description.md` — Description / Trigger
- `references/dims/dim-2-gotchas.md` — Gotchas Section
- `references/dims/dim-3-progressive-disclosure.md` — Progressive Disclosure
- `references/dims/dim-4-railroading.md` — Railroading
- `references/dims/dim-5-setup-config.md` — Setup & Config
- `references/dims/dim-6-scripts.md` — Scripts & Code
- `references/dims/dim-7-memory.md` — Memory / State
- `references/dims/dim-8-context-efficiency.md` — Context Efficiency（含存在合理性 flag）

**其他參考**：
- `references/categories.md` — 9 種 skill 類型的模式與反模式
- `assets/review-template.md` — 空白評審報告模板
