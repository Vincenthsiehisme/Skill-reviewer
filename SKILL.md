---
name: skill-reviewer
description: Review, audit, and improve Claude Code skills based on Anthropic best practices. Use this skill whenever the user wants to evaluate skill quality, get feedback on a SKILL.md file, check if a skill follows best practices, improve an existing skill, or diagnose why a skill isn't triggering or performing well. Trigger on phrases like "review my skill", "is this skill good?", "why isn't my skill working?", "audit this skill", "improve my skill", "check my SKILL.md".
---

# Skill Reviewer

A skill for auditing and improving Claude Code skills based on Anthropic's documented best practices.

## How to Use This Skill

1. **先問診斷方向**：在開始 review 之前，先問用戶：「這個 skill 有沒有具體問題（例如不觸發、輸出不穩定），還是要全面審查？」如果用戶已經明確說明問題或直接提供了 SKILL.md 內容，跳過問診直接開始。
2. 請用戶貼上 SKILL.md 內容（或提供檔案路徑）
3. **根據診斷方向，只讀需要的維度檔案**（在 `references/dims/` 下）：

   | 情境 | 讀取的檔案 |
   | --- | --- |
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

Evaluate skills across these 8 dimensions. Read the corresponding file in `references/dims/` for the detailed rubric of each.​​​​​​​​​​​​​​​​
