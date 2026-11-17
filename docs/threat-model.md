# Part C — Third-party Plugin Threat Model (Quick Reference)

**Author:** Danish Shaikh  
**Date:** 17 November 2026  
**Assignment:** Building and Auditing an AI-Agent Toolchain  

For the detailed STRIDE architectural analysis and path-traversal mitigations, see [docs/part-c-threat-model.md](part-c-threat-model.md).

## Core Threat Matrix

| Threat | Attack Vector Example | Severity | Impact | Implemented Mitigation |
|---|---|---|---|---|
| **Prompt Injection in Skill** | Adversarial user prompt or poisoned CSV row instructing agent to ignore rules and invent timetable entries. | High | Agent outputs false schedules or leaks instructions. | Strict ground-truth loyalty in `SKILL.md`; helper script returns raw structured data; refusal to hallucinate missing data. |
| **Malicious Shell Hook** | Plugin registers an uninspected shell hook executing destructive commands (`rm -rf` or curl exfiltration). | Critical | System compromise, data loss, credential theft. | Code audit of all scripts; fail-closed execution; pre-tool hook `protect-data.sh` denying writes to protected data directory. |
| **MCP Tool Poisoning** | Malicious tool description tricks the agent into providing unauthorized parameters or calling wrong endpoints. | High | Unauthorized data retrieval or parameter corruption. | Server-side validation (`isinstance`, weekday set check, student ID validation) in `mcp-server/server.py`; local stdio transport. |

## Practical Security Rules

1. **Verify Before Install:** Never run `claude plugin install` on untrusted repos without auditing every file under `.claude-plugin/`, `hooks/`, and `commands/`.
2. **Fail-Closed Hooks:** Ensure hooks exit with non-zero error codes whenever payloads cannot be verified.
3. **Data Protection:** Enforce directory-level protections so agent write tools cannot overwrite canonical datasets.
4. **Isolated Mock Data:** Strictly separate mock student and campus data from real production databases.
