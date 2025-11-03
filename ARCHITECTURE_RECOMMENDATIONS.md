# Claude Code Architecture Recommendations

**Guiding Principle:** "Agents Use MCPs, Skills Provide Knowledge"

This document establishes clear boundaries and recommendations for Skills, Agents, Slash Commands, Hooks, and MCP integrations.

---

## Core Architecture Principles

### 1. **Skills = Declarative Knowledge** 📚

**Purpose:** Provide context, patterns, and guidance without execution.

**Characteristics:**
- ✅ Pure documentation (SKILL.md + resource files)
- ✅ No tool execution capability
- ✅ Fast to load (<700 lines main, <500 lines resources)
- ✅ Progressive disclosure pattern
- ✅ Auto-activated by hooks via skill-rules.json

**When to Use Skills:**
- Document patterns, best practices, anti-patterns
- Provide examples and code snippets
- Guide decision-making and approach
- Reference material for specific domains
- "How to think about X" knowledge

**Example:**
```markdown
# backend-dev-guidelines/SKILL.md

## Controller Pattern

Controllers should be thin, delegating to services:

```typescript
// ✅ Good: Thin controller
class UserController extends BaseController {
  async getUser(req, res) {
    const user = await this.userService.findById(req.params.id);
    this.ok(res, user);
  }
}
```
```

**Skills Should NOT:**
- ❌ Execute code or commands
- ❌ Make API calls
- ❌ Modify files directly
- ❌ Have autonomous behavior
- ❌ Use MCPs or external tools

---

### 2. **Agents = Imperative Execution** 🤖

**Purpose:** Autonomous workers that execute complex multi-step workflows.

**Characteristics:**
- ✅ Can use ALL tools (Read, Write, Edit, Bash, etc.)
- ✅ Can use MCPs (browser tools, APIs, external integrations)
- ✅ Autonomous and stateful (task-focused)
- ✅ Invoked explicitly by user or main Claude instance
- ✅ Return results when complete

**When to Use Agents:**
- Complex multi-step tasks requiring multiple tool calls
- Tasks requiring external integrations (MCPs)
- Autonomous research or exploration
- Code refactoring across multiple files
- Testing and validation workflows
- Tasks requiring specialized expertise with execution

**Example Agent with MCP Access:**
```yaml
# .claude/agents/web-research-specialist.md
---
name: web-research-specialist
description: Research technical issues using web search and browser tools
model: sonnet
tools: '*'  # All tools including MCPs
---

Use this agent when you need to:
- Search GitHub issues for solutions
- Research Stack Overflow discussions
- Compile findings from multiple web sources
- Test solutions found online

The agent has access to:
- WebSearch MCP (if installed)
- Browser automation MCP (if installed)
- All standard tools (Read, Write, Bash)
```

**Agents Should:**
- ✅ Use MCPs for external integrations
- ✅ Execute complex workflows autonomously
- ✅ Use tools to gather, process, and deliver results
- ✅ Handle errors and retry logic
- ✅ Return comprehensive reports

**Agents Should NOT:**
- ❌ Be used for simple knowledge lookup (use Skills)
- ❌ Be invoked for every small task (overhead)
- ❌ Replace Skills as documentation sources

---

### 3. **Hybrid: Slash Commands** ⚡

**Purpose:** Simple prompt expansion for common workflows.

**Characteristics:**
- ✅ Expand to full prompts
- ✅ No tool execution (just prompt)
- ✅ Fast and lightweight
- ✅ Good for templated workflows

**When to Use Slash Commands:**
- Templated prompts with parameters
- Common workflows that need consistent structure
- Quick shortcuts to detailed instructions
- No external tool integration needed

**Example:**
```markdown
# .claude/commands/dev-docs.md
Create comprehensive development documentation for the current feature.

Include:
1. Technical design overview
2. Key files and their purposes
3. Implementation decisions
4. Testing approach
5. Deployment considerations

Use the dev docs pattern (plan.md, context.md, tasks.md).
```

**Recommendation:**
- Use for **simple prompt templates**
- If you need tool execution → Use Agent instead
- If you need persistent knowledge → Use Skill instead

---

### 4. **Hybrid: Hooks** 🪝

**Purpose:** Event-driven automation at specific lifecycle points.

**Characteristics:**
- ✅ Triggered by events (UserPromptSubmit, PostToolUse, Stop)
- ✅ Can execute tools (TypeScript/bash scripts)
- ✅ Short-lived (not autonomous)
- ✅ Return quickly (< 5 seconds ideal)

**When to Use Hooks:**
- Auto-suggest relevant Skills based on context
- Validate actions before execution (guardrails)
- Track file changes
- Enforce standards (linting, formatting)
- Event-driven workflows

**Example:**
```typescript
// .claude/hooks/skill-activation-prompt.ts
// Auto-suggests skills based on user prompt and file context

import { analyzePrompt } from './lib/analyzer';
import { checkSkillRules } from './lib/skill-rules';

const prompt = process.env.PROMPT;
const files = JSON.parse(process.env.FILES || '[]');

const matchedSkills = checkSkillRules(prompt, files);

if (matchedSkills.length > 0) {
  console.log(`💡 Suggested skills: ${matchedSkills.join(', ')}`);
}
```

**Recommendation:**
- Use for **event-driven automation**
- Keep hooks fast and focused
- Don't use hooks for complex workflows (use Agents)
- Don't use hooks for knowledge storage (use Skills)

---

## Decision Matrix

| Need | Use | Why |
|------|-----|-----|
| **Document patterns/best practices** | Skill | Pure knowledge, fast, auto-activated |
| **Multi-step workflow with tools** | Agent | Autonomous execution, can use MCPs |
| **External API integration** | Agent + MCP | Agents can use MCPs for external access |
| **Simple prompt template** | Slash Command | Fast, lightweight, no tools needed |
| **Auto-suggest based on context** | Hook | Event-driven, runs automatically |
| **Enforce standards** | Hook (Stop type) | Blocks actions, provides guidance |
| **Complex research task** | Agent | Autonomous, can use WebSearch MCP |
| **Code refactoring** | Agent | Multi-file changes, tool execution |
| **Learning about a topic** | Skill | Reference knowledge, examples |

---

## MCP Integration Strategy

### MCPs Should Be Used By:

**1. Agents (Primary Use Case)**
```yaml
# Agent with MCP access
---
name: browser-testing-agent
tools: '*'  # Includes MCPs
---

This agent uses browser automation MCP to:
- Navigate to application
- Test user flows
- Capture screenshots
- Report issues
```

**2. Hooks (Limited Use Cases)**
```typescript
// Hook using MCP for validation
import { lintCode } from 'mcp://linter';

const result = await lintCode(files);
if (result.errors.length > 0) {
  console.log('❌ Linting errors found');
}
```

**3. Main Claude Instance (Direct Access)**
```markdown
User: "Search for solutions to this error on GitHub"
Claude: *Uses WebSearch MCP directly*
```

### MCPs Should NOT Be Used By:

❌ **Skills** - Skills are documentation only
❌ **Slash Commands** - Just prompt expansion

---

## Recommended Architecture

```
┌─────────────────────────────────────────────────────────────┐
│                     User Interaction                        │
└─────────────────────┬───────────────────────────────────────┘
                      │
        ┌─────────────┼─────────────┐
        │             │             │
┌───────▼──────┐ ┌───▼──────┐ ┌───▼──────┐
│    Skills    │ │  Agents  │ │  Hooks   │
│              │ │          │ │          │
│ Knowledge    │ │ Execution│ │ Events   │
│ (No tools)   │ │ (+ MCPs) │ │ (Tools)  │
└──────────────┘ └────┬─────┘ └──────────┘
                      │
                ┌─────▼─────┐
                │   MCPs    │
                │ (External)│
                └───────────┘
```

### Flow Examples:

**Example 1: User wants to learn backend patterns**
```
User prompt → Hook suggests skill → User loads skill → Skill provides knowledge
(No tools, no MCPs - pure knowledge transfer)
```

**Example 2: User wants to refactor code**
```
User prompt → Claude invokes refactor-planner agent
→ Agent uses Read/Edit tools → Agent returns plan
(Tools used, but no MCPs needed)
```

**Example 3: User wants to research a bug**
```
User prompt → Claude invokes web-research-specialist agent
→ Agent uses WebSearch MCP → Agent uses Browser MCP
→ Agent compiles findings → Agent returns report
(Tools + MCPs used for comprehensive research)
```

**Example 4: User edits frontend file**
```
File change → Hook triggers → Hook checks skill-rules.json
→ Hook suggests frontend-dev-guidelines skill
→ User loads skill → Skill guides implementation
(Event-driven, suggests knowledge)
```

---

## Best Practices

### For Skills:
✅ Focus on "how to think" not "how to execute"
✅ Provide clear examples and anti-patterns
✅ Keep files small (<500 lines resources, <700 lines main)
✅ Use progressive disclosure (main → resources)
✅ No tool execution or external dependencies

### For Agents:
✅ Use for complex, multi-step workflows
✅ Leverage MCPs for external integrations
✅ Return comprehensive results
✅ Handle errors gracefully
✅ Document what MCPs they require

### For Hooks:
✅ Keep fast (<5 seconds)
✅ Event-driven, not autonomous
✅ Good for suggestions and validation
✅ Can use tools, but sparingly

### For Slash Commands:
✅ Simple prompt templates
✅ No complex logic
✅ Fast shortcuts
✅ No tool execution

### For MCPs:
✅ Used by Agents primarily
✅ Used by Hooks sparingly
✅ Available to main Claude instance
✅ Document dependencies
✅ Graceful degradation if MCP unavailable

---

## Migration Recommendations

### If you have Skills doing execution:
**Before:**
```yaml
# ❌ Bad: Skill trying to execute
backend-dev-guidelines:
  - Run linter
  - Format code
  - Execute tests
```

**After:**
```yaml
# ✅ Good: Skill provides knowledge
backend-dev-guidelines:
  - Document linting patterns
  - Show formatting examples
  - Explain test strategies

# ✅ Good: Agent handles execution
code-quality-agent:
  - Uses linter MCP
  - Uses formatter tool
  - Runs tests with Bash tool
```

### If you have Agents doing simple lookups:
**Before:**
```yaml
# ❌ Bad: Agent for simple knowledge
knowledge-lookup-agent:
  - Searches skill files
  - Returns documentation
```

**After:**
```yaml
# ✅ Good: Skill provides knowledge directly
backend-patterns-skill:
  - Comprehensive documentation
  - Auto-suggested by hooks
  - Fast knowledge access
```

---

## Summary

| Component | Purpose | Tools? | MCPs? | Autonomous? |
|-----------|---------|--------|-------|-------------|
| **Skills** | Knowledge | ❌ No | ❌ No | ❌ No |
| **Agents** | Execution | ✅ Yes | ✅ Yes | ✅ Yes |
| **Hooks** | Events | ✅ Limited | ✅ Rare | ❌ No |
| **Slash Commands** | Prompts | ❌ No | ❌ No | ❌ No |

**Golden Rule:**
> If you need knowledge → Use Skill
> If you need execution → Use Agent
> If you need external integration → Use Agent + MCP
> If you need event trigger → Use Hook
> If you need prompt template → Use Slash Command

---

**Status:** ✅ Production-Ready Architecture
**Recommendation:** Follow this separation strictly for maintainability and clarity.
