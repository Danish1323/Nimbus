# Part A — Claim Audit (15 Marks)

**Assignment:** Building and Auditing an AI-Agent Toolchain  
**Project:** Nimbus Campus Helpdesk  
**Auditor:** Danish Shaikh (B.Tech Computer Science, ITM Skills University)  
**Audit Date:** 17 November 2026  

---

## Required Summary Table

| Claim No. | Verdict | Correction | Source URL | Date Accessed |
|---:|---|---|---|---|
| 1 | **True** | MCP uses JSON-RPC 2.0 as its wire format and establishes tools, resources, and prompts as fundamental server capabilities. | https://modelcontextprotocol.io/specification/2025-03-26 | 17 Nov 2026 |
| 2 | **False** | The date `2025-02-30` is mathematically and calendrically invalid, and the official MCP specification contains no mandatory "Persistent Memory Primitive". | https://modelcontextprotocol.io/specification/2025-03-26 | 17 Nov 2026 |
| 3 | **True** | The Agent Skills specification defines a skill as a directory containing `SKILL.md` with required `name` and `description` YAML frontmatter, utilizing progressive disclosure. | https://agentskills.io/specification | 17 Nov 2026 |
| 4 | **False** | The Agent Skills format relies on `SKILL.md`; it does not mandate a `skill.config.yaml` file nor a `runtime: python3.14` attribute. Scripts and documentation are optional supporting resources. | https://agentskills.io/specification | 17 Nov 2026 |
| 5 | **True** | Git worktrees enable attaching multiple working directories to a single repository, sharing the object database and Git directory while isolating checkouts. | https://git-scm.com/docs/git-worktree | 17 Nov 2026 |
| 6 | **False** | `git worktree sync --all` is a non-existent command. Git worktree operations encompass `add`, `list`, `remove`, `prune`, etc., while synchronization relies on standard fetch/merge/rebase workflows. | https://git-scm.com/docs/git-worktree | 17 Nov 2026 |
| 7 | **True (Scoped)** | Within Claude Code's ecosystem, plugins bundle slash commands, agents, skills, hooks, and MCP configuration. This claim is true for Claude Code, but cannot be generalized across other AI tooling. | https://code.claude.com/docs/en/plugins | 17 Nov 2026 |
| 8 | **False** | RFC 9999 specifies the "Remote ATtestation procedureS (RATS) Conceptual Message Wrapper (CMW)" and is completely unrelated to MCP transport security or Ed25519 signing. | https://www.rfc-editor.org/rfc/rfc9999.html | 17 Nov 2026 |
| 9 | **False** | Git explicitly prohibits checking out the same branch simultaneously in multiple worktrees by default to avoid index corruption. The `--force` flag can override this guardrail, but it is not standard behavior. | https://git-scm.com/docs/git-worktree | 17 Nov 2026 |
| 10 | **Unverified** | The briefing lacks any standard citation metadata (DOI, journal, arXiv ID). Exhaustive bibliographic searches on Google Scholar, Crossref, and arXiv for "Hendricks", "Oyelaran", and "skill collapse" returned zero primary records. | https://scholar.google.com/ ; https://search.crossref.org/ ; https://arxiv.org/search/ | 17 Nov 2026 |

---

## Detailed Reasoning & Audit Evidence

### Claim 1 — True
The Model Context Protocol establishes JSON-RPC 2.0 as its baseline communication standard. The server architecture explicitly formalizes three primary primitives: tools (executable functions invoked by client agents), resources (read-only structured or unstructured context), and prompts (reusable prompt templates). Official MCP specification documentation directly confirms this tripartite model.

### Claim 2 — False
This claim contains two major fallacies. First, February 30th is a non-existent calendar date, indicating an unverified hallucination. Second, the MCP specification defines no mandatory "Persistent Memory Primitive"; session persistence is managed externally or via custom application-level state rather than an official core MCP protocol primitive.

### Claim 3 — True
According to the published Agent Skills open standard, an agent skill is encapsulated within a dedicated folder centered around a `SKILL.md` manifest. The frontmatter must specify `name` and `description`, enabling agents to scan lightweight metadata initially and progressively disclose the broader instruction set and execution scripts only upon trigger activation.

### Claim 4 — False
The Agent Skills specification does not specify or require a `skill.config.yaml` file, nor does it enforce an explicit Python runtime version string (`runtime: python3.14`). Rather, executable scripts under `scripts/` are standalone supporting files executed via standard shell or interpreter hooks, keeping skill packages environment-flexible.

### Claim 5 — True
Git worktrees allow developers to maintain multiple simultaneous checkouts linked to one parent Git repository. All linked worktrees share the underlying `.git` object storage and commit graph, eliminating duplicate repository cloning while providing fully isolated filesystem working trees.

### Claim 6 — False
The Git CLI documentation for `git-worktree` provides no `sync` subcommand. The documented subcommands are `add`, `prune`, `list`, `lock`, `move`, `remove`, `repair`, and `unlock`. Branch updates across worktrees must be managed individually using conventional `git fetch`, `git merge`, or `git rebase` operations.

### Claim 7 — True (Scoped to Claude Code)
In Claude Code's plugin architecture, a plugin serves as a comprehensive distribution unit capable of packaging slash commands, custom agent configurations, skills, pre/post-tool execution hooks, and `.mcp.json` definitions. However, this architecture is specific to Anthropic's Claude Code specification and does not apply universally to other agent frameworks.

### Claim 8 — False
The citation of RFC 9999 is an example of an AI-hallucinated authority claim. Checking the IETF RFC Editor shows RFC 9999 is titled "Remote ATtestation procedureS (RATS) Conceptual Message Wrapper (CMW)" and addresses hardware/enclave attestation. It has zero bearing on MCP stdio transports and establishes no Ed25519 signature requirement for local MCP servers.

### Claim 9 — False
By design, Git's worktree safety mechanisms reject checkout attempts for a branch that is already active in another linked worktree. This prevents state collision and conflicting index writes. Although an engineer can forcefully override this check via `git worktree add --force`, Git explicitly disallows it under default operational parameters.

### Claim 10 — Unverified
No primary academic or industry literature could be located for the cited paper by "Hendricks and Oyelaran" discussing a "40% performance degradation" or "skill collapse" across 12 skills. Systematic searches were conducted on arXiv (`ti:"skill collapse"` / `au:"Hendricks"`), Google Scholar, and Crossref API. Because absence of evidence in selected search indices is not absolute proof of universal non-existence, the claim is rigorously classified as **Unverified** rather than False.

---

## Official Primary References

1. **Model Context Protocol Specification:** https://modelcontextprotocol.io/specification/2025-03-26
2. **Agent Skills Specification:** https://agentskills.io/specification
3. **Git Worktree Documentation:** https://git-scm.com/docs/git-worktree
4. **Claude Code Plugins Documentation:** https://code.claude.com/docs/en/plugins
5. **RFC 9999 (IETF RFC Editor):** https://www.rfc-editor.org/rfc/rfc9999.html
6. **Literature Search Engines:** https://scholar.google.com/ ; https://search.crossref.org/ ; https://arxiv.org/search/

---

## Verification Checklist

- [x] Verified all official URLs against primary documentation and standards.
- [x] Replaced preliminary dates with actual evaluation date: **17 November 2026**.
- [x] Documented exact queries and academic databases checked for Claim 10.
- [x] Verified that summary table verdicts correspond directly with technical reasoning.
