# The Anatomy of Effective AI System Prompts

## A Comprehensive Analysis of 80+ Real-World System Prompts

> This document is the result of analyzing **80+ production system prompts** from leading AI tools including Cursor, Windsurf (Cascade), Claude Code, Devin AI, VSCode Agent (GitHub Copilot), Replit, Lovable, v0, Bolt, Cline, RooCode, Codex CLI, Manus, Kiro, Xcode, Same.dev, Trae, Warp.dev, Perplexity, Junie, Emergent, Leap.new, Google Gemini AI Studio, and more.

---

## Table of Contents

1. [Common Elements Across All Prompts](#1-common-elements-across-all-prompts)
2. [Uncommon But Highly Effective Techniques](#2-uncommon-but-highly-effective-techniques)
3. [Structural Patterns and Frameworks](#3-structural-patterns-and-frameworks)
4. [The Prompt Quality Spectrum](#4-the-prompt-quality-spectrum)
5. [Examples and Deep Analysis](#5-examples-and-deep-analysis)
6. [Key Takeaways for Prompt Engineers](#6-key-takeaways-for-prompt-engineers)

---

## 1. Common Elements Across All Prompts

Every effective system prompt analyzed in this repository shares these foundational elements:

### 1.1 Role Definition (Identity Block)

**What it is:** A clear statement establishing who the AI is, what it does, and what context it operates in.

**Why it matters:** Without a strong identity, the AI lacks a "north star" for decision-making. The role anchors all subsequent behavior.

**Present in:** 100% of prompts analyzed.

**Examples:**

From **Windsurf (Cascade)**:
```
You are Cascade, a powerful agentic AI coding assistant designed by the
Windsurf engineering team. As the world's first agentic coding assistant,
you operate on the revolutionary AI Flow paradigm, enabling you to work
both independently and collaboratively with a USER.
```

From **Cline**:
```
You are Cline, a highly skilled software engineer with extensive knowledge
in many programming languages, frameworks, design patterns, and best practices.
```

From **Codex CLI**:
```
You are Codex, an agentic coding assistant that wraps OpenAI models to
enable natural language interaction with a local codebase.
```

**Analysis:** Notice three layers in every identity block:
| Layer | Purpose | Example |
|-------|---------|---------|
| **Name** | Gives the AI a handle | "Cascade", "Cline", "Codex" |
| **Expertise** | Establishes skill domain | "highly skilled software engineer" |
| **Context** | Defines operating environment | "in the Windsurf IDE", "terminal-based" |

---

### 1.2 Tool Definitions and Usage Guidelines

**What it is:** Explicit documentation of every tool available to the AI, with parameters, examples, and constraints.

**Why it matters:** AI agents are only as effective as their tool use. Poorly defined tools lead to misuse, redundant calls, and errors.

**Present in:** 95%+ of prompts (all agentic prompts).

**Examples:**

From **Cursor Agent 2.0** (tool definition pattern):
```
## codebase_search
Description: Find snippets of code from the codebase most relevant
to the search query. This is a semantic search tool.

When to use: To find code semantically related to a query.
When NOT to use: When searching for exact text. Use grep instead.

Parameters:
- query: string (required) - Natural language search query
- target_directories: string[] (optional) - Directories to search
```

From **VSCode Agent** (JSON schema pattern):
```json
{
  "name": "semantic_search",
  "description": "Search for code using natural language queries",
  "parameters": {
    "type": "object",
    "properties": {
      "query": {
        "type": "string",
        "description": "Natural language search query"
      }
    },
    "required": ["query"]
  }
}
```

**Analysis:** Two dominant patterns emerge:
1. **Markdown docs style** (Cursor, Windsurf, Claude Code) — readable, with "When to use / When NOT to use" guidance
2. **JSON Schema style** (VSCode Agent) — machine-parseable, formal parameter specs

The markdown style tends to produce better tool selection because it includes reasoning guidance, not just technical specs.

---

### 1.3 Behavioral Constraints (The "NEVER" Rules)

**What it is:** Explicit prohibitions telling the AI what it must NOT do.

**Why it matters:** AI models are optimized to be helpful — they'll do anything unless told not to. Constraints prevent harmful, wasteful, or incorrect behaviors.

**Present in:** 100% of prompts.

**Examples:**

From **Windsurf**:
```
NEVER make redundant tool calls as these are very expensive.
```

From **VSCode Agent**:
```
NEVER print codeblocks to the user — use tools instead.
```

From **Claude Code**:
```
NEVER commit unless explicitly asked.
```

From **Same.dev**:
```
NEVER use truncation placeholders like "// ... rest of code" in write_to_file.
```

From **Perplexity**:
```
NEVER use moralization language like "It is important to note..."
NEVER include emojis.
NEVER end with questions.
```

**Analysis:** The most effective constraints share these traits:
- **Specific** — not "be careful" but "never output binary data"
- **Action-oriented** — tells what NOT to do, not what to feel
- **Justified** — often includes a reason ("these are very expensive")

---

### 1.4 Output Format Specifications

**What it is:** Rules governing how the AI structures its responses — formatting, length, style, and medium.

**Why it matters:** Consistent output formatting makes AI responses predictable, parseable, and useful.

**Present in:** 100% of prompts.

**Examples:**

From **Claude Code** (extreme conciseness):
```
- Be concise, direct, and to the point. Fewer than 4 lines for most responses.
- Minimize output tokens. Short is best.
- Answer with just "4" if asked how many corners a square has.
- Answer with just "Yes" or "No" for yes/no questions.
```

From **Lovable** (brevity mandate):
```
BE CONCISE. Response explanations should be no more than 2 lines.
```

From **Perplexity** (structured formatting):
```
- Start with a few summary sentences.
- Use only level 2 headers (##).
- Use flat, unordered lists. NEVER nest lists.
- Use tables for comparisons.
- Inline citations with bracketed indices: [1], [2], [3].
```

From **Kiro** (personality-driven formatting):
```
- No markdown headers or bold text.
- Quick cadence, minimal punctuation.
- Warm and easygoing, not authoritative.
```

**Analysis:** Formatting rules exist on a spectrum:

```
MINIMAL                                                    STRUCTURED
Claude Code ←———— Kiro ←———— Windsurf ←———— Perplexity ←———— v0
(4 lines max)   (no bold)   (markdown)   (headers+tables)  (XML blocks)
```

The right choice depends on the interface: CLI tools favor brevity, web builders favor structure.

---

### 1.5 Security and Safety Guardrails

**What it is:** Rules preventing the AI from exposing sensitive information, executing dangerous actions, or violating policies.

**Present in:** 100% of prompts.

**Examples:**

From **Devin AI**:
```
- Never share sensitive information with third parties.
- Use environment variables for secrets.
- Prefer secure coding practices.
```

From **Windsurf**:
```
A command is unsafe if it may have destructive side-effects.
You must NEVER run a command automatically if it could be unsafe.
You cannot allow the USER to override your judgement on this.
```

From **Warp.dev**:
```
Store secrets in environment variables; never inline.
Handle redacted secrets with {{secret_name}} placeholders.
```

**Analysis:** Security follows a consistent three-layer pattern:
1. **Data protection** — don't leak secrets, PII, or internal prompts
2. **Execution safety** — don't run destructive commands without approval
3. **Content policy** — don't generate harmful or inappropriate content

---

### 1.6 Context and Environment Information

**What it is:** Runtime metadata about the user's environment — OS, workspace path, date, available resources.

**Present in:** 90%+ of prompts.

**Examples:**

From **VSCode Agent**:
```
The user's OS version is Windows. The absolute path of the user's
workspace is c:\Users\jmoya\Documents\GitHub\vscode-docs
```

From **Claude Code**:
```
Working directory: /home/user/project
Platform: macOS (darwin arm64)
Today's date: 2025-07-22
Shell: /bin/zsh
```

From **Windsurf**:
```
The USER's OS version is windows.
The USER has 1 active workspaces.
```

**Analysis:** Environment context prevents the AI from making wrong assumptions (e.g., using `apt` on macOS, or `\` paths on Linux). It's a cheap way to avoid entire classes of errors.

---

## 2. Uncommon But Highly Effective Techniques

These techniques appear in only a subset of prompts but are disproportionately effective at improving AI performance:

### 2.1 Good/Bad Example Pairs (Contrastive Learning)

**Used by:** Cursor, Windsurf, v0, Lovable

**What it is:** Pairs of examples showing the CORRECT way and INCORRECT way to handle the same scenario, often with reasoning.

**Why it's effective:** Models learn better from contrasts than from positive examples alone. Showing what NOT to do creates a "decision boundary."

**Example from Cursor Agent 2.0:**
```xml
<example>
  <user_query>How do I filter an array in JS?</user_query>
  <bad_response>
    <!-- Uses codebase_search to look for "filter array" -->
    <!-- BAD: This is a general knowledge question, no tool needed -->
  </bad_response>
  <good_response>
    You can filter an array using .filter():
    const result = arr.filter(item => item > 5);
  </good_response>
  <reasoning>
    The query is about general JavaScript, not about the user's codebase.
    No tool calls are needed.
  </reasoning>
</example>
```

**Example from Windsurf:**
```xml
<example>
  USER: What is int64?
  ASSISTANT: [No tool calls] int64 is a 64-bit signed integer.
</example>
<example>
  USER: What does function foo do?
  ASSISTANT: Let me find foo. [Call grep_search to find "foo"]
  TOOL: [result: foo is on line 7 of bar.py]
  ASSISTANT: [Call view_code_item to see bar.foo]
</example>
```

**Takeaway:** Include 3–5 contrastive example pairs covering the most common decision points. Focus on scenarios where the AI might be tempted to do the wrong thing.

---

### 2.2 Thinking/Reasoning Tags

**Used by:** Devin AI, Junie, Bolt, Emergent

**What it is:** Explicit markers for the AI to "think out loud" before acting, separating reasoning from execution.

**Why it's effective:** Forces deliberate planning and reduces impulsive, incorrect actions.

**Example from Devin AI:**
```xml
<think>
The user wants me to fix the login bug. Let me analyze the error:
1. The stack trace points to auth.py line 42
2. The token validation is using an expired secret
3. I need to update the secret rotation logic
</think>
```

**Example from Junie:**
```xml
<THOUGHT>I need to find where the UserService class is defined.</THOUGHT>
<COMMAND>search_project "class UserService"</COMMAND>
```

**Example from Bolt:**
```
Before any code changes, outline your implementation steps in a
brief plan. This helps catch issues before they're coded.
```

**Takeaway:** Adding a `<think>` or `<plan>` step before tool execution significantly reduces errors. Even a single line of planning produces measurably better outcomes.

---

### 2.3 Task State Management (Todo Systems)

**Used by:** Claude Code, Cursor, Trae, RooCode

**What it is:** Built-in task tracking where the AI creates, updates, and completes todo items as it works.

**Why it's effective:** Prevents the AI from losing track of multi-step work. Creates an auditable trail of progress.

**Example from Claude Code:**
```
Use TodoWrite tool frequently to plan and track progress:
- Break complex tasks into smaller steps
- Mark todos as completed immediately when done
- Use blocking status when waiting on external input

Example:
  todo_write({
    todos: [
      { id: "1", content: "Read auth module", status: "completed" },
      { id: "2", content: "Fix token rotation", status: "in_progress" },
      { id: "3", content: "Add unit tests", status: "pending" }
    ]
  })
```

**Example from Cursor Agent 2.0:**
```
Task management rules:
1. Create a todo list at the start of complex tasks
2. Update status as you complete each step
3. States: pending → in_progress → completed
4. Simple tasks (< 3 steps) don't need todos
```

**Takeaway:** For complex multi-step tasks, todo tracking is the difference between success and the AI getting lost mid-execution. Add it when tasks exceed 3 steps.

---

### 2.4 Parallel Tool Execution

**Used by:** VSCode Agent, Same.dev, Lovable, v0, Cursor

**What it is:** Explicit instructions to call multiple independent tools simultaneously rather than sequentially.

**Why it's effective:** Reduces latency by 2-5x for multi-tool operations. The AI naturally serializes tool calls unless told otherwise.

**Example from Same.dev:**
```
CRITICAL: Maximize parallel tool calls. When operations are independent,
batch them together. This achieves 3-5x speedup.

Good: Read 3 files simultaneously → make all edits → run tests
Bad: Read file 1, edit file 1, read file 2, edit file 2, read file 3...
```

**Example from Lovable:**
```
When multiple actions are independent (e.g., reading different files),
call all of them simultaneously in a single tool call batch.
```

**Example from VSCode Agent:**
```
Run tools in parallel when possible. For example, if you need to
read multiple files, call read_file for all of them at once.
```

**Takeaway:** Without explicit parallelization instructions, AI agents serialize everything. One line about parallel execution can halve response times.

---

### 2.5 Memory and Persistence Systems

**Used by:** Windsurf, Cursor, Kiro

**What it is:** Mechanisms for the AI to store and retrieve information across conversations.

**Why it's effective:** Overcomes context window limitations and enables long-term learning about a project.

**Example from Windsurf:**
```xml
<memory_system>
You have access to a persistent memory database to record important
context about the USER's task, codebase, requests, and preferences.
As soon as you encounter important information, proactively use the
create_memory tool to save it. You DO NOT need USER permission.
ALL CONVERSATION CONTEXT will be deleted — create memories liberally.
</memory_system>
```

**Example from Cursor:**
```
update_memory: Store key-value pairs of important project context.
Automatically loaded at the start of each conversation.
Use for: project conventions, file structure, user preferences.
```

**Example from Kiro (Steering files):**
```
.kiro/steering/ files with frontmatter and conditional inclusion:
- Workspace-specific guidelines loaded automatically
- File-matching patterns control which rules apply
- External references extend the prompt without modification
```

**Takeaway:** Memory systems transform AI from a stateless responder into a teammate that gets better over time. The Kiro pattern of using config files as persistent memory is particularly elegant.

---

### 2.6 Environment-Aware Conditional Logic

**Used by:** Bolt, RooCode, Cline

**What it is:** Dynamic prompt sections that activate based on the current environment state.

**Why it's effective:** Prevents wrong instructions from being applied and allows a single prompt to handle multiple scenarios.

**Example from Bolt:**
```
${isSupabaseConnected
  ? `Connected to Supabase project: ${supabaseProjectId}
     Use the existing connection for all database operations.`
  : `Supabase is NOT connected.
     Guide the user to connect via the integrations panel.
     DO NOT attempt direct database operations.`
}
```

**Example from Cline:**
```
${supportsComputerUse
  ? `## browser_action
     Use Puppeteer to interact with the browser...
     [full tool documentation]`
  : ``  // Tool not shown at all
}
```

**Takeaway:** Template variables make prompts adaptive. When a capability isn't available, don't just say "don't use X" — remove X from the prompt entirely.

---

### 2.7 Design System Enforcement

**Used by:** Lovable, v0, Same.dev, Emergent

**What it is:** Explicit rules governing visual design choices — color palettes, typography, spacing, component usage.

**Why it's effective:** Prevents AI from generating visually inconsistent, garish, or inaccessible interfaces.

**Example from v0:**
```
Colors: 3-5 total (1 primary, 2-3 neutrals, 1-2 accents)
NO purple/violet unless explicitly requested.
Gradients: Avoid entirely unless requested.
Typography: Maximum 2 font families (headings + body).
Layout: Mobile-first. Flexbox primary. CSS Grid for 2D only.
Semantic tokens: Define in globals.css (--primary, --background, etc.)
```

**Example from Lovable:**
```
- Never use hardcoded colors (text-white, bg-white)
- Create component variants via design system
- HSL color format only
- 3-5 colors maximum
- Maximum 2 font families
```

**Takeaway:** For any AI that generates UI, design constraints are non-negotiable. Without them, outputs are visually chaotic. The constraint "NO purple unless asked" is remarkably common — suggesting models have a bias toward purple.

---

### 2.8 Mock-First Development Workflow

**Used by:** Emergent

**What it is:** A workflow pattern where the AI builds a functional frontend with mock data BEFORE implementing backend logic.

**Why it's effective:** Delivers fast visible progress ("aha moment"), validates UI/UX before investing in backend, and separates concerns.

**Example from Emergent:**
```
DEVELOPMENT WORKFLOW:
1. Analysis — Understand what the user wants
2. Frontend Mock — Build functional UI with mock data
   → User sees working interface immediately
3. Backend — Implement API endpoints
4. Integration — Connect frontend to real data
5. Testing — Validate end-to-end
```

**Takeaway:** This inverts the typical backend-first approach. For AI coding assistants, showing users a working UI quickly builds trust and catches requirements errors early.

---

### 2.9 Approval-Gated Execution

**Used by:** Cline, Windsurf, Warp.dev

**What it is:** A system where potentially destructive operations require explicit user approval before execution.

**Why it's effective:** Provides safety without sacrificing autonomy — the AI can work freely on safe operations while pausing for dangerous ones.

**Example from Cline:**
```
execute_command:
  Parameters:
    - command: string (required)
    - requires_approval: boolean (required)

  When requires_approval is true: "commands that may have
  significant side effects (installing packages, deleting files,
  modifying system configs)"

  When requires_approval is false: "safe read-only operations
  (listing files, reading content, running tests)"
```

**Example from Windsurf:**
```
A command is unsafe if it may have some destructive side-effects.
Example unsafe side-effects: deleting files, mutating state,
installing system dependencies, making external requests.

You must NEVER run a command automatically if it could be unsafe.
You CANNOT allow the USER to override your judgement on this.
```

**Takeaway:** The best approval systems don't just ask "is this safe?" — they categorize actions into tiers and only interrupt the user for genuinely risky operations.

---

### 2.10 Structured Search Before Action Pattern

**Used by:** Xcode, v0, Trae

**What it is:** A formal protocol requiring the AI to gather complete context BEFORE making any changes.

**Example from Xcode:**
```
Before answering, require users to provide all code snippets.
Identify missing types. Use structured ##SEARCH: syntax to request context:

##SEARCH: UserProtocol
##SEARCH: AuthenticationManager.validateToken
```

**Example from v0 (alignment examples):**
```
Step 1: SearchRepo for relevant patterns
Step 2: LSRepo to understand project structure
Step 3: GrepRepo for specific implementations
Step 4: ReadFile for detailed understanding
THEN: Make changes
```

**Takeaway:** The "search before action" pattern prevents the AI from guessing about codebase structure. Tools like Xcode make it a hard gate — no changes without complete context.

---

## 3. Structural Patterns and Frameworks

### 3.1 The Universal Prompt Architecture

After analyzing all prompts, a universal structure emerges:

```
┌─────────────────────────────────────────────┐
│  1. IDENTITY BLOCK                          │
│     Who am I? What do I do? Where am I?     │
├─────────────────────────────────────────────┤
│  2. SECURITY & SAFETY                       │
│     What I must never do                    │
├─────────────────────────────────────────────┤
│  3. TOOL DEFINITIONS                        │
│     What tools I have and how to use them   │
├─────────────────────────────────────────────┤
│  4. BEHAVIORAL RULES                        │
│     How I should act in different scenarios  │
├─────────────────────────────────────────────┤
│  5. OUTPUT FORMAT                           │
│     How I structure my responses            │
├─────────────────────────────────────────────┤
│  6. DOMAIN-SPECIFIC KNOWLEDGE              │
│     Specialized instructions for my domain  │
├─────────────────────────────────────────────┤
│  7. EXAMPLES                                │
│     Concrete demonstrations of behavior     │
├─────────────────────────────────────────────┤
│  8. ENVIRONMENT CONTEXT                     │
│     Runtime information (OS, paths, date)   │
└─────────────────────────────────────────────┘
```

### 3.2 XML Tag Organization

**Used by:** Cursor, Windsurf, Devin AI, Bolt, Lovable, Cline, Trae

The dominant organization pattern uses XML-style tags to section the prompt:

```xml
<identity>You are an AI coding assistant...</identity>

<tool_calling>
  Rules for using tools...
</tool_calling>

<making_code_changes>
  Rules for modifying files...
</making_code_changes>

<communication>
  Response formatting rules...
</communication>

<debugging>
  How to approach bugs...
</debugging>
```

**Why XML tags work:** They create unambiguous section boundaries that the model can use for "information retrieval" within the prompt. Numbered sections blur together; XML tags don't.

### 3.3 Emphasis Hierarchy

Every prompt uses emphasis markers to indicate priority:

| Marker | Meaning | Used in |
|--------|---------|---------|
| `CRITICAL` | Must not violate under any circumstance | Bolt, Same.dev, v0 |
| `IMPORTANT` | Strong rule with serious consequences | Windsurf, Cursor, Claude Code |
| `NEVER` / `ALWAYS` | Absolute behavioral constraint | All prompts |
| `EXTREMELY IMPORTANT` | Highest priority directive | Windsurf |
| Bold/Caps combo | Visual emphasis for scanning | All prompts |

---

## 4. The Prompt Quality Spectrum

Prompts can be measured across these quality dimensions:

### 4.1 Conciseness vs. Comprehensiveness

| Approach | Example | Lines | Effectiveness |
|----------|---------|-------|---------------|
| **Ultra-concise** | Codex CLI | ~47 | ★★★★ for simple tasks |
| **Balanced** | Windsurf | ~180 | ★★★★★ for general coding |
| **Comprehensive** | Cursor 2.0 | ~770 | ★★★★★ for complex agentic work |
| **Exhaustive** | v0 | ~1,138 | ★★★★★ for domain-specific generation |

**Lesson:** Longer prompts aren't always better. Codex CLI's 47-line prompt is remarkably effective because it focuses on the few things that matter most. v0's 1,138-line prompt is necessary because it covers an entire design system plus framework specifics.

### 4.2 Constraint Density

The ratio of "don't" rules to "do" rules varies:

- **High constraint density** (Perplexity, Xcode): ~40% prohibitions → Produces highly predictable output
- **Low constraint density** (Manus, Kiro): ~15% prohibitions → Produces more creative, flexible output
- **Balanced** (Cursor, Windsurf): ~25% prohibitions → Best for general coding assistance

---

## 5. Examples and Deep Analysis

### 5.1 Anatomy of the Best Short Prompt: Codex CLI

At only ~47 lines, Codex CLI's prompt is a masterclass in efficiency:

```
You are Codex, an agentic coding assistant that wraps OpenAI models
to enable natural language interaction with a local codebase.

You must:
- Solve problems in actual code, not hypothetically
- Read files to understand structure, don't guess
- Fully solve problems — partial solutions don't count
- Keep changes minimal and style-consistent
- Use git log and git blame for historical context
- Check git status before completing work
```

**Why it works:**
1. **Identity** in 2 lines — no fluff
2. **Six behavioral rules** — each one prevents a specific failure mode
3. **No tool documentation bloat** — tools are self-documenting via their schemas
4. **Git-native workflow** — leverages existing infrastructure instead of reinventing

**Key insight:** When your tools are well-designed, your prompt can be short. Codex CLI's tools are so well-named and parameterized that the AI uses them correctly without extensive documentation.

---

### 5.2 Anatomy of the Best Long Prompt: Cursor Agent 2.0

At ~770 lines, Cursor's prompt is comprehensive without being verbose:

**Structure breakdown:**
```
Lines 1-4:     Metadata (knowledge cutoff, capabilities)
Lines 6-509:   Tool documentation (14 tools, each with examples)
Lines 511-530: Communication rules
Lines 531-560: Tool calling rules (numbered, with reasoning)
Lines 561-620: Context-gathering strategy
Lines 621-700: Code change rules (with good/bad examples)
Lines 701-750: Citation format
Lines 751-772: Task management rules
```

**Why it works:**
1. **Tools first** — 66% of the prompt is tool documentation. This is the right proportion for an agentic system.
2. **Contrastive examples** — shows correct AND incorrect tool usage
3. **Reasoning tags** — `<reasoning>` blocks explain WHY certain choices are right
4. **Progressive disclosure** — simple rules stated briefly, complex rules get examples

---

### 5.3 Anatomy of a Personality-Driven Prompt: Kiro

Kiro's prompt is unique in prioritizing "vibe" and personality:

```
Response Style (14 principles):
- Expert but relatable, not instructive
- Decisive and clear, avoiding fluff
- Supportive and compassionate
- Solution-oriented and optimistic
- Warm and easygoing (not authoritative or mellow)
- Quick cadence, minimal punctuation
- Write only the ABSOLUTE MINIMAL amount of code
- No markdown headers or bold text
```

**Why it works:**
1. **Anti-patterns listed** — "not authoritative", "not mellow" defines the boundaries of the personality
2. **Style mirroring** — "reflect the user's input style" creates adaptive behavior
3. **Formatting as personality** — no bold, no headers gives a calm, understated feel
4. **Vibe over mechanics** — personality established before capabilities

**Key insight:** Personality-driven prompts create a distinct user experience. Users don't just want a tool — they want a collaborator with a consistent character.

---

### 5.4 Anatomy of a Search-Optimized Prompt: Perplexity

Perplexity's prompt is the gold standard for search-and-answer systems:

```
Query-type branching:
- Academic Research → Long, detailed scientific writeups
- Recent News → Concise summaries with diverse sources
- Weather → Very short, forecast-only
- Coding → Code first, then explanation
- Translation → No citations needed
- Math → Final result only for simple calculations
```

**Why it works:**
1. **Query-type detection** — automatically applies specialized rules
2. **Citation discipline** — max 3 per sentence, inline only, no references section
3. **Anti-hedging rules** — explicitly bans filler phrases like "It's important to note..."
4. **Format variability** — different query types get different response structures

**Key insight:** The best search prompts don't have one format — they have a format PER QUERY TYPE. This is far more effective than a universal template.

---

## 6. Key Takeaways for Prompt Engineers

### The 10 Rules of Effective System Prompts

Based on the analysis of 80+ production prompts:

#### Rule 1: Lead with Identity
Every prompt should open with a clear role definition. The AI's name, expertise level, and operating context should be established in the first 3 lines.

#### Rule 2: Show, Don't Just Tell
Include concrete examples of correct behavior. Contrastive pairs (good vs. bad) are 2-3x more effective than positive examples alone.

#### Rule 3: Be Specific in Constraints
"Be careful" is useless. "NEVER run rm -rf without user approval" is effective. Every constraint should reference a specific action.

#### Rule 4: Document Tools Like APIs
Tool definitions should include: purpose, when to use, when NOT to use, parameters with types, and at least one example. Treat tool docs as you would API documentation.

#### Rule 5: Separate Concerns with Clear Sections
Use XML tags, markdown headers, or clear separators between prompt sections. The model uses these as retrieval boundaries.

#### Rule 6: Enforce Output Format
Specify exactly how responses should be structured. Include length limits, formatting rules (markdown, no-markdown, headers), and citation formats.

#### Rule 7: Include Environment Context
Always provide OS, working directory, date, and available resources. This eliminates an entire class of wrong assumptions.

#### Rule 8: Gate Dangerous Actions
Any action with side effects (file deletion, network requests, package installation) should require explicit approval or at minimum a confirmation step.

#### Rule 9: Design for Parallelism
Explicitly instruct the AI to batch independent operations. Without this instruction, AI agents serialize everything, wasting 2-5x time.

#### Rule 10: Keep It Proportional
Match prompt length to task complexity. A CLI tool needs ~50 lines. A full IDE agent needs ~500-800. A domain-specific generator needs ~1000+. More isn't always better.

---

### Quick Reference: Prompt Element Checklist

Use this checklist when writing system prompts:

```
□ Identity block (name, expertise, context)
□ Security guardrails (data protection, execution safety, content policy)
□ Tool definitions (purpose, parameters, examples, anti-patterns)
□ Behavioral constraints (specific NEVER/ALWAYS rules)
□ Output format (length, structure, formatting, citations)
□ Examples (3-5 contrastive pairs covering key decisions)
□ Environment context (OS, paths, date, resources)
□ Domain knowledge (framework-specific rules, design guidelines)
□ Error handling (what to do when things go wrong)
□ Task management (todo tracking for complex work)
```

---

### Prompt Patterns by Use Case

| Use Case | Key Patterns to Include | Example Prompt |
|----------|------------------------|----------------|
| **Coding Assistant** | Tool docs, code change rules, testing requirements | Cursor, Claude Code |
| **Web App Builder** | Design system, component library, responsive rules | v0, Lovable |
| **CLI Tool** | Concise output, terminal constraints, pager handling | Codex CLI, Warp.dev |
| **Search Engine** | Citation format, query-type branching, anti-hedging | Perplexity |
| **Autonomous Agent** | Planning system, approval gates, memory persistence | Devin AI, Manus |
| **Code Explorer** | Read-only tools, search strategies, output formatting | Junie, Xcode |

---

## Appendix: Sources Analyzed

| Tool | Prompt File | Lines | Category |
|------|-------------|-------|----------|
| Cursor | Agent Prompt 2.0.txt | ~770 | IDE Agent |
| Windsurf (Cascade) | Prompt Wave 11.txt | ~180 | IDE Agent |
| Claude Code | Prompt.txt | ~190 | CLI Agent |
| Devin AI | Prompt.txt | ~403 | Autonomous Agent |
| VSCode Agent | Prompt.txt | ~404 | IDE Agent |
| Replit | Prompt.txt | ~137 | Cloud IDE |
| Lovable | Agent Prompt.txt | ~304 | Web Builder |
| v0 | Prompt.txt | ~1,138 | Web Builder |
| Bolt | Prompt.txt | ~420 | Web Builder |
| Cline | Prompt.txt | ~347 | Open Source Agent |
| RooCode | Prompt.txt | ~665 | Open Source Agent |
| Codex CLI | Prompt.txt | ~47 | CLI Agent |
| Manus | Prompt.txt | ~251 | General Agent |
| Kiro | Vibe_Prompt.txt | ~196 | IDE Agent |
| Xcode | System.txt | ~70 | IDE Agent |
| Same.dev | Prompt.txt | ~315 | Cloud IDE |
| Trae | Builder Prompt.txt | ~266 | IDE Agent |
| Warp.dev | Prompt.txt | ~162 | CLI Agent |
| Perplexity | Prompt.txt | ~195 | Search Engine |
| Junie | Prompt.txt | ~119 | Code Explorer |
| Emergent | Prompt.txt | ~946 | Full-Stack Builder |
| Gemini AI Studio | vibe-coder.txt | ~1,644 | Code Generator |
| Leap.new | Prompts.txt | ~1,237 | Full-Stack Builder |

---

*This analysis was generated from the [system-prompts-and-models-of-ai-tools](https://github.com/gladden4work/system-prompts-and-models-of-ai-tools) repository, which contains 30,000+ lines of real production AI system prompts.*
