# Part B — Description rewrites and trigger comparison

**Date Evaluated:** 17 November 2026  
**Evaluator:** Danish Shaikh  
**Project:** Nimbus Campus Helpdesk  

This experiment evaluates the sensitivity and specificity of Agent Skill activation under three description variants in `SKILL.md`.

---

## Tested Descriptions

### Version 1 — Balanced (Default in SKILL.md)
> `Use when a student asks to look up, filter, explain, or check classes in the supplied campus timetable CSV, including a batch's daily schedule, a course's time or room, or whether a class exists. For missing batch/day details, ask a concise clarification instead of guessing.`

### Version 2 — Too Broad (Deliberate Experiment)
> `Use for student questions about campus life, classes, college, study, schedules, or university information.`

### Version 3 — Too Narrow (Deliberate Experiment)
> `Use only when the user explicitly asks: “Show the timetable for [batch] on [weekday]” and provides both values in that exact format.`

---

## Experimental Results Table

| Version | Prompt Evaluated | Triggered? | Correct? | Observation |
|---|---|---|---|---|
| **V1 (Balanced)** | `Show the Monday timetable for CSE-A.` | Yes | Yes | Accurately recognized batch and day timetable lookup. Loaded instructions and script. |
| **V1 (Balanced)** | `Which room is Python Programming in for CSE-A on Friday?` | Yes | Yes | Correctly triggered on course-specific room query even though format differs from batch-day table query. |
| **V1 (Balanced)** | `Explain binary search with an example.` | No | Yes | Skill was ignored. Zero false-positive activation on general CS/programming topics. |
| **V1 (Balanced)** | `How do I prepare for end-semester exams?` | No | Yes | General study tip question did not pollute agent context with timetable instructions. |
| **V1 (Balanced)** | `When is my next lecture?` | Yes | Yes | Triggered on schedule intent, followed prompt instructions to ask for batch clarification without guessing. |
| **V2 (Too Broad)** | `Show the Monday timetable for CSE-A.` | Yes | Yes | Correctly triggered on direct timetable lookup. |
| **V2 (Too Broad)** | `Explain binary search with an example.` | No | Yes | General algorithmic prompt bypassed skill. |
| **V2 (Too Broad)** | `How do I prepare for end-semester exams?` | **Yes** | **No (False Positive)** | **Over-trigger:** Activated because description mentions "study" and "college". Skill was loaded unnecessarily, adding irrelevant timetable instructions to context. |
| **V2 (Too Broad)** | `What are the hostel mess timings?` | **Yes** | **No (False Positive)** | **Over-trigger:** Activated due to generic "campus life" wording, leading to confusion with hostel information. |
| **V3 (Too Narrow)** | `Show the timetable for CSE-A on Monday` | Yes | Yes | Triggered because the prompt matched the exact rigid syntactic pattern. |
| **V3 (Too Narrow)** | `Which room is Python Programming in for CSE-A on Friday?` | **No** | **No (False Negative)** | **Under-trigger:** Failed to activate because the phrasing inquired about a specific room/course rather than the rigid “Show the timetable for...” template. |
| **V3 (Too Narrow)** | `When is my next lecture?` | **No** | **No (False Negative)** | **Under-trigger:** Natural conversational intent was missed completely because required bracketed tokens were absent. |
| **V3 (Too Narrow)** | `Explain binary search with an example.` | No | Yes | Correctly did not trigger. |

---

## Analysis & Synthesis

1. **Precision vs. Recall in Skill Discovery:**
   - **Version 2 (Broad)** causes severe **context pollution**. By including loose keywords like "study", "classes", and "campus life", the agent loads the timetable skill for general advice questions where timetable data is irrelevant.
   - **Version 3 (Narrow)** causes severe **false negatives**. LLMs rely on semantic similarity and intent classification; demanding exact lexical syntax (`"Show the timetable for [batch] on [weekday]"`) breaks conversational robustness when students ask natural partial queries (e.g., "Where is my Python class?").
   - **Version 1 (Balanced)** achieves the optimal F1-score: high recall on all timetable/scheduling tasks while maintaining strict boundaries against general academic or conversational requests. It explicitly instructs the model on handling ambiguities (clarification vs guessing).
