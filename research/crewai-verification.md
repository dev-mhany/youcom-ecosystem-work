# CrewAI x You.com Integration — Verification

## 1. Verdict

**Type B (with Type C documentation wrapper). The original claim is misleading and should not be used as stated.**

CrewAI did **not** add a first-class You.com tool. CrewAI 1.14.4 (Apr 30, 2026) shipped two **documentation pages** that describe how to point CrewAI's pre-existing, generic `MCPServerHTTP` / `MCPServerAdapter` classes at You.com's remote MCP server (`https://api.you.com/mcp`). No You.com-specific Python class, package, or import path was added to either `crewAIInc/crewAI` or `crewAIInc/crewAI-tools`. The actual integration work was done on the You.com side (publishing the MCP server); CrewAI's contribution was a recipe in the docs.

The version-specific framing in the claim is also wrong: 1.14.3 contains **no You.com mention at all**, and 1.14.4a1 lists You.com only under "Documentation," not "Features."

---

## 2. Evidence

### 2a. Release notes — primary source

**1.14.3** (published 2026-04-24, [release link](https://github.com/crewAIInc/crewAI/releases/tag/1.14.3)):
The full changelog mentions e2b, Daytona, Bedrock V4, Azure DefaultAzureCredential, checkpoint lifecycle events, and one MCP-related item — **a generic perf optimization**: *"Optimize MCP SDK and event types to reduce cold start by ~29%"*. **You.com is not mentioned anywhere.**

**1.14.4a1** (published 2026-04-29, [release link](https://github.com/crewAIInc/crewAI/releases/tag/1.14.4a1)):
You.com appears in exactly one line, under the **Documentation** section (not Features):
> *"Add You.com MCP tools for search, research, and content extraction."*

**1.14.4** (published 2026-04-30, [release link](https://github.com/crewAIInc/crewAI/releases/tag/1.14.4)):
Re-lists the same line under "Features" alongside Tavily Research. The diff (`1.14.3...1.14.4`, 87 files) confirms the only You.com-related changes are two MDX files: `docs/en/tools/search-research/youai-search.mdx` and `docs/en/tools/web-scraping/youai-contents.mdx`. **No `.py` source files referencing You.com were added.**

### 2b. Code search — `crewAIInc/crewAI-tools`

The full recursive tree of `crewAI-tools/main` contains **no You.com tool directory**. The 75+ tool subdirectories include `tavily_search_tool`, `serper_dev_tool`, `brave_search_tool`, `exa_tools`, `linkup`, etc., but **no `you_*` or `ydc_*` directory**. The `youtube_channel_search_tool` and `youtube_video_search_tool` are unrelated (YouTube). No `YouSearchTool` class exists in either repo.

### 2c. CrewAI's own docs explicitly use generic MCP plumbing

From `docs/en/tools/search-research/youai-search.mdx` shipped in 1.14.4 (raw source: [1.14.4/docs/.../youai-search.mdx](https://raw.githubusercontent.com/crewAIInc/crewAI/1.14.4/docs/en/tools/search-research/youai-search.mdx)):

> *"You.com provides a remote MCP server at `https://api.you.com/mcp`..."*

The Python example imports the **generic** classes, not a You.com-specific class:
```python
from crewai.mcp import MCPServerHTTP
mcps=[MCPServerHTTP(url="https://api.you.com/mcp?profile=free", streamable=True)]
```

The companion `youai-contents.mdx` even documents that CrewAI's MCP layer is **incompatible** with You.com's `you-contents` schema and ships ~50 lines of user-side schema-patching workaround (`_patch_tool_schema`) — clear evidence this is not a curated first-class integration.

### 2d. You.com docs

`https://you.com/docs/integrations/crewai` exists (`https://docs.you.com/integrations/crewai` 301-redirects to it). The page states the integration *"uses crewAI's built-in MCP support"* and instructs users to install plain `mcp` or `crewai-tools[mcp]` and connect to `https://api.you.com/mcp`. (The standalone `https://you.com/docs/developer-resources/mcp-server` page returns 404 at the moment.)

### 2e. You.com MCP server exists independently

The npm package [`@youdotcom-oss/mcp`](https://www.npmjs.com/package/@youdotcom-oss/mcp) is published ("You.com API Model Context Protocol Server") and the remote endpoint `https://api.you.com/mcp` is live with `you-search`, `you-research`, and `you-contents` tools. This existed and worked **before** CrewAI's 1.14.4 docs page; any MCP-capable client (Claude Desktop, generic MCP, CrewAI ≥ the version that added MCP support, etc.) can connect.

---

## 3. What to say on the pitch call

Three options depending on how aggressive Gitonga wants to be. **Option A is the only one that's strictly accurate** — recommend it.

**Option A — accurate, defensible (RECOMMENDED):**
> *"CrewAI ships You.com integration documentation in their April 2026 1.14.4 release — agents connect to our remote MCP server at api.you.com/mcp using CrewAI's MCP support. Any CrewAI user gets you-search, you-research, and you-contents out of the box."*

**Option B — slightly stronger, still defensible:**
> *"You.com is one of the named MCP integrations in CrewAI's 1.14.4 release notes, alongside Tavily. The CrewAI docs show our search, research, and content APIs as a recommended stack for their agents."*

**Option C — DO NOT USE:**
> ~~"CrewAI integrated You.com via MCP in v1.14.3 / v1.14.4a1."~~ This is wrong on three counts: (1) 1.14.3 has no You.com content, (2) 1.14.4a1 lists it under Documentation only, (3) "CrewAI integrated You.com" overstates direction — CrewAI added a docs recipe pointing at our MCP server, which is fundamentally a You.com-built integration consumed via CrewAI's generic MCP plumbing.

**If asked "did CrewAI write code for this?":** Answer honestly — *"They added documentation and listed us in their release notes; the integration code lives on our side as the MCP server. CrewAI's existing MCP client connects to it."* That's still a strong story (CrewAI is endorsing You.com to their users) without any false claim about CrewAI engineering effort.

---

## 4. Sources

- CrewAI 1.14.3 release: https://github.com/crewAIInc/crewAI/releases/tag/1.14.3
- CrewAI 1.14.4a1 release: https://github.com/crewAIInc/crewAI/releases/tag/1.14.4a1
- CrewAI 1.14.4 release: https://github.com/crewAIInc/crewAI/releases/tag/1.14.4
- Diff 1.14.3...1.14.4 (GitHub API): https://api.github.com/repos/crewAIInc/crewAI/compare/1.14.3...1.14.4
- youai-search docs (1.14.4): https://raw.githubusercontent.com/crewAIInc/crewAI/1.14.4/docs/en/tools/search-research/youai-search.mdx
- youai-contents docs (1.14.4): https://raw.githubusercontent.com/crewAIInc/crewAI/1.14.4/docs/en/tools/web-scraping/youai-contents.mdx
- crewAIInc org repos: https://api.github.com/orgs/crewAIInc/repos
- crewAI-tools tools directory listing: https://api.github.com/repos/crewAIInc/crewAI-tools/contents/crewai_tools/tools
- You.com CrewAI integration page: https://you.com/docs/integrations/crewai (301 from https://docs.you.com/integrations/crewai)
- You.com MCP npm package: https://www.npmjs.com/package/@youdotcom-oss/mcp / https://registry.npmjs.org/@youdotcom-oss/mcp
- You.com MCP endpoint: https://api.you.com/mcp
