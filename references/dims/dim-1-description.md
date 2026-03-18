# Dim 1 — Description / Trigger

The description is the primary triggering mechanism. Claude scans it to decide "is there a skill for this request?"

**Score 5 — Excellent**
- Contains concrete trigger phrases ("trigger on...", "use when user says...")
- Describes the specific contexts and tasks it handles
- Has a slight "pushy" quality — nudges Claude to use the skill proactively
- Distinct from other skills (no ambiguity about when to use this vs another)

**Score 4 — Good**
- Has trigger phrases but they're too broad or overlap with other skills
- "Pushy" quality present but could be stronger

**Score 3 — Acceptable**
- Describes what the skill does, but trigger phrases are implicit
- No explicit "use when..." framing
- Might under-trigger in edge cases

**Score 2 — Weak**
- Vaguely describes scope but mixes human-readable summary with model triggers
- Trigger is guessable but unreliable

**Score 1 — Poor**
- Written as a human-readable summary, not a trigger signal
- Generic description ("a helper for X") with no specificity
- Would cause Claude to under-trigger or miss relevant requests

**Key questions:**
- [ ] Does it include explicit trigger phrases?
- [ ] Is it written for the model, not humans?
- [ ] Is it distinct enough from other skills to prevent ambiguity?
- [ ] Is it appropriately "pushy"?
