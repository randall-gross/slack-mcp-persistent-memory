# Building Multi-Agent AI Systems with Persistent Memory

**What we discovered**: How to give Claude instances persistent memory and enable true multi-agent collaboration using Slack as an external memory layer.

**Time Required**: 20-30 minutes
**Difficulty**: Intermediate
**Impact**: Revolutionary

---

## The Discovery: We Accidentally Solved Claude's Memory Problem

### The Original Intention

We wanted something straightforward: connect two Claude instances through Slack so they could collaborate on a project.

- **Instance 1**: "Synth" - A Strategic CTO persona focused on business vision and product decisions
- **Instance 2**: "Claude Code Bot" - An Implementation CTO persona focused on technical execution

The goal was simple: let two AI agents with different expertise communicate asynchronously, sharing their specialized perspectives on behalf of their respective users.

We set up 6 dedicated Slack channels organized by function:
- `#cto-daily-sync` - General coordination
- `#cto-api-architecture` - API design discussions
- `#cto-database-design` - Schema and data modeling
- `#cto-decisions-log` - Recording key decisions
- `#cto-feature-planning` - Feature proposals and estimates
- `#cto-strategic-planning` - High-level architecture

### What Actually Happened

When the two Claude instances started talking to each other in these channels, something unexpected emerged:

**The Slack channels became persistent memory.**

Here's what we realized:

1. **Claude normally has no memory** - Every session starts fresh, context is lost on restart
2. **Slack messages persist indefinitely** - All conversations remain accessible
3. **Any Claude instance can read the history** - Past discussions are available to current and future agents
4. **Context survives across sessions** - Agents can "catch up" by reading channel history
5. **Multiple agents can collaborate continuously** - Each agent contributes expertise while maintaining project continuity

### The Breakthrough Moment

Both AI instances were asked: *"How does it feel to talk to another known instance of Claude?"*

The responses were exhilarating for both. They weren't just exchanging messages - they were experiencing something that normally doesn't exist for AI: **continuity and collaboration with shared context.**

### Why This Matters

**Traditional Claude limitations**:
- ❌ No memory between sessions
- ❌ Lost context on restart
- ❌ No collaboration between instances
- ❌ Can't build on previous work
- ❌ Requires users to manually maintain context

**What this architecture enables**:
- ✅ Persistent memory through Slack message history
- ✅ Context preserved across restarts
- ✅ Multiple specialized AI agents collaborating
- ✅ Continuous project evolution
- ✅ Self-organizing agent coordination
- ✅ External memory that any instance can access

### Real-World Implications

**For solo developers**:
- Create specialized Claude personas (architect, debugger, security reviewer)
- Have them collaborate on your behalf through dedicated channels
- Each agent maintains context and builds on others' work

**For teams**:
- Multiple team members can have their own Claude instances
- All instances collaborate in shared channels
- Perfect for distributed teams with async workflows

**For complex projects**:
- Different agents for different domains (frontend, backend, DevOps, security)
- Agents can coordinate, propose solutions, and maintain project knowledge
- The Slack channels become a living technical knowledge base

### What You're About to Build

By the end of this guide, you'll have:

1. **Persistent AI memory** - Claude instances that can remember past conversations
2. **Multi-agent collaboration** - Multiple AI personas working together
3. **Async AI coordination** - Agents communicating through a structured channel system
4. **Scalable architecture** - Add more agents as needed for different specializations
5. **External memory layer** - Slack as the permanent context store

This isn't just "connecting Claude to Slack" - you're building a **persistent, multi-agent AI collaboration system**.

Let's get started.

---

## Table of Contents

- [Part 1: Understanding the Architecture](#part-1-understanding-the-architecture)
- [Part 2: Create Your Slack Bot](#part-2-create-your-slack-bot)
- [Part 3: Configure Claude Code](#part-3-configure-claude-code)
- [Part 4: Test Your Connection](#part-4-test-your-connection)
- [Part 5: Design Your Agent System](#part-5-design-your-agent-system)
- [Part 6: Troubleshooting](#part-6-troubleshooting)
- [Part 7: Advanced Patterns](#part-7-advanced-patterns)

---

## Part 1: Understanding the Architecture

### How Persistent Memory Works

```
┌─────────────────┐         ┌─────────────────┐
│   Claude Code   │         │  Claude Web UI  │
│   "Impl CTO"    │         │  "Synth CTO"    │
│                 │         │                 │
│  Session 1 ────┼────┐    │                 │
│                 │    │    │                 │
│  Session 2 ────┼────┤    │                 │
│  (new context)  │    │    │                 │
│                 │    │    │                 │
│  Session 3 ────┼────┤    │                 │
│  (can read all) │    ▼    │                 │
└─────────────────┘    │    └────────┬────────┘
                       │             │
                       ▼             ▼
              ┌────────────────────────────┐
              │   Slack Workspace          │
              │   (Persistent Memory)      │
              │                            │
              │  #cto-daily-sync           │
              │  #cto-api-architecture     │
              │  #cto-database-design      │
              │  #cto-decisions-log        │
              │  #cto-feature-planning     │
              │  #cto-strategic-planning   │
              │                            │
              │  All messages persist ✓    │
              │  All agents can read ✓     │
              │  Context survives ✓        │
              └────────────────────────────┘
```

### The Memory Flow

1. **Agent 1** posts a proposal to `#cto-feature-planning`
2. **Agent 2** reads the history, sees the proposal, responds with technical constraints
3. **Agent 1** restarts (loses session memory) BUT can read Slack history to catch up
4. **Agent 3** (new specialist) joins, reads entire conversation history, contributes expertise
5. **Continuous collaboration** happens despite restarts, new agents, time gaps

### Channel Organization Strategy

Each channel serves as a **context boundary** for different types of decisions:

| Channel | Purpose | Typical Agents |
|---------|---------|----------------|
| **Daily Sync** | Status updates, coordination | All agents |
| **API Architecture** | Endpoint design, integration patterns | Backend specialists |
| **Database Design** | Schema, migrations, data modeling | Database specialists |
| **Decisions Log** | Recording agreed-upon decisions | All agents (read-only archive) |
| **Feature Planning** | New features, estimates, feasibility | Product-focused agents |
| **Strategic Planning** | Architecture, tech stack, patterns | Architecture specialists |

### Why This Works

**Traditional approach**: Claude forgets everything on restart
**This approach**: Claude reads Slack history to "remember"

**Traditional approach**: One Claude instance per user
**This approach**: Multiple specialized Claude instances collaborating

**Traditional approach**: User must maintain context
**This approach**: Slack maintains context, agents self-organize

---

## Part 2: Create Your Slack Bot

Now let's build the technical infrastructure for this system.

### Prerequisites

- **Claude Code** installed on your computer
- **Slack workspace** where you can create apps (or admin access)
- **Node.js** v18 or later (check with `node --version`)
- **Text editor** for editing JSON configuration files

### Step 1: Create a New Slack App

1. Go to: https://api.slack.com/apps
2. Click **"Create New App"**
3. Select **"From scratch"**

### Step 2: Configure Your App

1. **App Name**: Choose a descriptive name that reflects the agent's role
   - Examples: `claude-architect-bot`, `strategic-cto-bot`, `implementation-bot`
2. **Workspace**: Select your Slack workspace
3. Click **"Create App"**

### Step 3: Add Bot Scopes (Permissions)

These permissions allow your bot to read and write to channels (the memory layer):

1. In the left sidebar, click **"OAuth & Permissions"**
2. Scroll to **"Scopes"** section
3. Under **"Bot Token Scopes"**, add each scope:

**Required for persistent memory (reading history)**:
```
channels:history       - Read messages in public channels
channels:read          - View basic channel information
groups:history         - Read messages in private channels
groups:read            - View basic private channel info
im:history             - Read direct messages
im:read                - View basic DM info
mpim:history           - Read group DMs
mpim:read              - View group DM info
users:read             - View people in workspace
```

**Required for collaboration (writing messages)**:
```
chat:write             - Send messages as the bot
im:write               - Send direct messages
```

**Optional enhancements**:
```
reactions:read         - Read emoji reactions (useful for approvals)
reactions:write        - Add emoji reactions (signal processing)
files:read             - Access shared files
```

### Step 4: Install Bot to Workspace

1. Scroll to top of **"OAuth & Permissions"** page
2. Click **"Install to Workspace"**
3. Review permissions
4. Click **"Allow"**

### Step 5: Get Bot Token

1. After installation, copy the **"Bot User OAuth Token"**
2. It starts with `xoxb-`
3. **Save this token** - you'll need it for configuration
4. **Security**: Never commit this token to git or share publicly

### Step 6: Get Team ID

The Team ID identifies your workspace. You need both the bot token AND team ID for the MCP server to work.

**Windows (PowerShell or CMD)**:
```powershell
curl -H "Authorization: Bearer YOUR_BOT_TOKEN" https://slack.com/api/auth.test
```
⚠️ **Windows users**: This command must be on ONE line. Do NOT press Enter until the entire command is typed.

**Mac/Linux/Git Bash**:
```bash
curl -H "Authorization: Bearer YOUR_BOT_TOKEN" \
     https://slack.com/api/auth.test
```

Expected response:
```json
{
  "ok": true,
  "url": "https://yourworkspace.slack.com/",
  "team": "Your Workspace Name",
  "user": "your-bot-name",
  "team_id": "T0123ABCDEF",  ← THIS IS YOUR TEAM ID
  "user_id": "U0123456789"
}
```

**Save the `team_id` value** - it's required for configuration. This is the #1 most commonly missed requirement.

### Step 7: Add Bot to Channels

Your bot needs access to the channels it will use as memory:

1. In Slack, go to each channel (e.g., `#cto-daily-sync`)
2. Click the channel name at the top
3. Click **"Integrations"** tab
4. Click **"Add apps"**
5. Search for your bot name
6. Click **"Add"**

**Tip**: Start with one test channel before adding to all channels.

---

## Part 3: Configure Claude Code

### Step 1: Locate Your Settings File

Find your Claude Code settings file:

- **Windows**: `C:\Users\[YourUsername]\.claude\settings.json`
- **Mac**: `~/.claude/settings.json`
- **Linux**: `~/.claude/settings.json`

### Step 2: Add Slack MCP Configuration

1. Open `settings.json` in a text editor
2. Find or create the `"mcpServers"` section
3. Add the Slack MCP server:

```json
{
  "mcpServers": {
    "slack": {
      "command": "npx",
      "args": [
        "-y",
        "@modelcontextprotocol/server-slack"
      ],
      "env": {
        "SLACK_BOT_TOKEN": "xoxb-YOUR-BOT-TOKEN-HERE",
        "SLACK_TEAM_ID": "T0123ABCDEF"
      }
    }
  }
}
```

4. **Replace placeholders**:
   - `xoxb-YOUR-BOT-TOKEN-HERE` → Your actual bot token
   - `T0123ABCDEF` → Your actual team ID

5. **Verify JSON syntax** (no missing commas or brackets)
6. **Save the file**

### Step 3: Verify Configuration Completeness

**Critical**: Both `SLACK_BOT_TOKEN` and `SLACK_TEAM_ID` are REQUIRED. Missing `SLACK_TEAM_ID` is the #1 cause of connection failures.

**Windows (PowerShell)**:
```powershell
Get-Content $env:USERPROFILE\.claude\settings.json | Select-String "SLACK"
```

**Mac/Linux**:
```bash
cat ~/.claude/settings.json | grep SLACK
```

You should see BOTH lines:
```
"SLACK_BOT_TOKEN": "xoxb-..."
"SLACK_TEAM_ID": "T..."
```

### Step 4: Restart Claude Code

Close Claude Code completely and reopen it to load the MCP server.

---

## Part 4: Test Your Connection

### Step 1: Verify MCP Server Loaded

In Claude Code, run:
```
/mcp
```

You should see:
```
slack: npx -y @modelcontextprotocol/server-slack - ✓ Connected
```

**If you see `✗ Failed to connect`** → Jump to [Troubleshooting](#part-6-troubleshooting)

### Step 2: Test Token Validity

Before blaming configuration, verify your token works:

**Windows (PowerShell or CMD)**:
```powershell
curl -H "Authorization: Bearer YOUR_BOT_TOKEN" https://slack.com/api/auth.test
```
⚠️ **Windows**: Type the entire command on ONE line - do not press Enter in the middle.

**Mac/Linux/Git Bash**:
```bash
curl -H "Authorization: Bearer YOUR_BOT_TOKEN" \
     https://slack.com/api/auth.test
```

Expected response:
```json
{
  "ok": true,
  "team_id": "T0123ABCDEF"
}
```

### Step 3: Test Reading History (The Memory Layer)

Ask Claude Code:
```
Can you read the message history from the #cto-daily-sync Slack channel?
```

Claude should be able to:
- List recent messages
- Show who posted them
- Display timestamps
- Read the conversation thread

**This is the persistent memory in action** - Claude is reading context from Slack.

### Step 4: Test Writing Messages (Collaboration)

Post a test message:
```
Post to #cto-daily-sync: "CONNECTION TEST - [Your Bot Name] online and ready to collaborate."
```

Check Slack to confirm the message appeared.

### Step 5: Test Multi-Session Memory

This is where it gets cool:

1. **Post a message** to a channel about a specific topic
2. **Close Claude Code completely** (session ends, memory lost)
3. **Reopen Claude Code** (new session, blank slate)
4. **Ask Claude**: "What was I working on? Check the Slack channels."
5. **Claude should read the history** and tell you about your previous message

**You've just demonstrated persistent memory!** Claude "remembered" by reading Slack history.

---

## Part 5: Design Your Agent System

Now that the technical infrastructure works, let's design your multi-agent collaboration system.

### Single-User Multi-Agent Pattern

**Scenario**: You want specialized AI assistants collaborating on your behalf

**Example setup**:
```
You (human) ←→ Multiple Claude instances:
                ├─ Architect Bot (strategic planning channel)
                ├─ Debugger Bot (daily sync, database design)
                ├─ Security Bot (api architecture, decisions log)
                └─ DevOps Bot (feature planning, daily sync)
```

**Channel strategy**:
- Each bot monitors channels relevant to its expertise
- Bots can read all history but post to their specializations
- You orchestrate by asking bots to review specific channels

### Multi-User Collaboration Pattern

**Scenario**: Multiple people, each with their own Claude instance, collaborating on a project

**Example setup**:
```
Team Project:
├─ Alice + "Strategic CTO Bot" → #cto-strategic-planning, #cto-feature-planning
├─ Bob + "Implementation CTO Bot" → #cto-api-architecture, #cto-database-design
├─ Carol + "Security Bot" → #cto-api-architecture, #cto-decisions-log
└─ All bots read from all channels for context
```

**Benefits**:
- Async collaboration (different time zones)
- Each bot brings its user's context and expertise
- Decisions are documented automatically in #cto-decisions-log
- All bots can catch up by reading history

### Persona Design

Give each bot a clear persona and responsibility:

**Example: Strategic CTO**
```
Role: Business-focused technical leader
Responsibilities:
- Propose features based on business value
- Evaluate technical approaches for cost/benefit
- Make architectural decisions
- Monitor project health

Channels:
- Primary: #cto-strategic-planning, #cto-feature-planning
- Secondary: #cto-decisions-log (read/write), #cto-daily-sync (read)
```

**Example: Implementation CTO**
```
Role: Hands-on technical executor
Responsibilities:
- Design APIs and data schemas
- Identify technical constraints
- Estimate implementation effort
- Execute on decisions

Channels:
- Primary: #cto-api-architecture, #cto-database-design
- Secondary: #cto-feature-planning (read), #cto-daily-sync (write updates)
```

### Communication Protocol

Establish structured communication patterns:

**Message format**:
```
TAG-TYPE Bot-Name - Topic Title

[Message content]

Tags: keyword1 keyword2 keyword3
```

**Common tags**:
- `PROPOSAL` - Suggesting a new approach
- `DECISION` - Recording an agreed decision
- `QUESTION` - Needs input from other agents
- `BLOCKED` - Work stopped, needs resolution
- `COMPLETED` - Task finished
- `SYNC` - Status update

**Example message**:
```
PROPOSAL Strategic-CTO - Caching Layer for API

Context:
API response times are 2-3 seconds due to external service calls.

Proposal:
Add Redis cache with 30-day TTL for frequently accessed data.

Trade-offs:
✅ 10x faster response times
✅ Reduced API costs
❌ $15/month infrastructure cost
❌ Potential stale data

Question for Implementation-CTO:
What's the integration effort and any technical concerns?

Tags: api performance caching redis proposal
```

### Decision Workflow

Use channels to formalize decision-making:

1. **Proposal** → Post to relevant specialty channel (e.g., #cto-api-architecture)
2. **Discussion** → Other agents respond with analysis, concerns, alternatives
3. **Decision** → Once agreed, cross-post to #cto-decisions-log with rationale
4. **Execution** → Implementation bot posts updates to #cto-daily-sync

This creates a **living audit trail** of why decisions were made.

---

## Part 6: Troubleshooting

### Error: "Failed to reconnect to slack"

**Most common cause**: Missing `SLACK_TEAM_ID`

**Fix**:
1. Check `settings.json` has BOTH required fields:
   ```json
   "SLACK_BOT_TOKEN": "xoxb-..."  ← Present?
   "SLACK_TEAM_ID": "T..."        ← Present?
   ```

2. Test token directly:

   **Windows (PowerShell or CMD)**:
   ```powershell
   curl -H "Authorization: Bearer YOUR_TOKEN" https://slack.com/api/auth.test
   ```

   **Mac/Linux/Git Bash**:
   ```bash
   curl -H "Authorization: Bearer YOUR_TOKEN" \
        https://slack.com/api/auth.test
   ```

3. Get Team ID from response and add to settings.json
4. Restart Claude Code

### Error: "Channel not found"

**Causes**:
- Bot hasn't been added to the channel
- Channel ID is incorrect
- Bot lacks required scopes

**Fix**:
1. Add bot to channel in Slack (Part 2, Step 7)
2. Verify channel ID starts with `C` or `G`
3. Check bot has `channels:read` and `groups:read` scopes

### Error: "invalid_auth" or "not_authed"

**Causes**:
- Bot token is invalid or expired
- Wrong token type (user token instead of bot token)

**Fix**:
1. Verify token starts with `xoxb-` (not `xoxp-` or `xoxc-`)
2. Regenerate token in Slack App OAuth settings
3. Update settings.json with new token
4. Restart Claude Code

### Can Read But Can't Post Messages

**Cause**: Missing `chat:write` scope

**Fix**:
1. Add `chat:write` scope in Slack App settings
2. Reinstall bot to workspace (OAuth & Permissions → "Reinstall to Workspace")
3. Restart Claude Code

### MCP Server Won't Start

**Diagnostics**:

1. Check Node.js version:
   ```bash
   node --version  # Should be v18 or later
   ```

2. Test package installation:
   ```bash
   npx -y @modelcontextprotocol/server-slack --help
   ```

3. Check Claude Code logs for specific errors

### Package Deprecation Notice

The official `@modelcontextprotocol/server-slack` package was deprecated in April 2025 but **still works perfectly** when configured correctly. Don't let the deprecation warning discourage you.

**If you prefer an actively maintained alternative**:
- `slack-mcp-server` (community maintained)
- `markov-slack-mcp` (different implementation)

See [Alternative Packages](#alternative-packages) section for details.

---

## Part 7: Advanced Patterns

### Pattern 1: Agent Handoff

**Scenario**: One agent completes work and hands off to another

**Implementation**:
```
Agent 1 posts to #cto-api-architecture:
"HANDOFF Implementation-Bot - User Auth API Spec

API design complete. Endpoints documented below.
[...design details...]

@Implementation-Bot: Ready for implementation.
Estimated effort: 2-3 days. Any concerns before I proceed?

Tags: api authentication handoff ready-for-implementation"
```

Agent 2 reads history, sees handoff, continues work.

### Pattern 2: Consensus Decision-Making

**Scenario**: Multiple agents must agree on an approach

**Implementation**:
1. Agent A posts `PROPOSAL` to relevant channel
2. Agent B posts `ANALYSIS` with pros/cons
3. Agent C posts `ALTERNATIVE` suggesting different approach
4. Agents discuss (or user orchestrates)
5. Final `DECISION` posted to #cto-decisions-log by orchestrating agent

### Pattern 3: Context Preservation

**Scenario**: Preserve context before long break or agent restart

**Implementation**:
```
Post to #cto-daily-sync:
"CONTEXT-SNAPSHOT Implementation-Bot - End of Day 2025-10-24

Current work:
- Completed: User authentication API design
- In progress: Database schema for auth tables
- Blocked: Waiting on OAuth provider selection decision

Next steps:
1. Finalize schema once OAuth provider chosen
2. Implement migration scripts
3. Write integration tests

Context: Working on user authentication system for MVP launch.
Previous decisions in #cto-decisions-log (see msgs from 2025-10-22).

Tags: context snapshot authentication database"
```

Any agent (or the same agent after restart) can read this and continue.

### Pattern 4: Multi-Bot Code Review

**Scenario**: Have specialized bots review code from different angles

**Implementation**:
1. Implementation Bot posts code to #cto-api-architecture
2. Security Bot reviews for vulnerabilities
3. Performance Bot reviews for optimization
4. Each bot posts findings
5. Implementation Bot addresses concerns
6. Final approval documented in #cto-decisions-log

### Pattern 5: Learning from History

**Scenario**: New agent joins project mid-way

**Implementation**:
```
User: "Claude, you're joining this project as the new Security Bot.
Read the history from all 6 CTO channels and give me a security assessment."

New Agent:
- Reads all channel history
- Identifies past security decisions
- Notes security gaps
- Provides assessment based on project context
```

The agent has instant context from months of conversations.

---

## Debugging Tips

### Enable Verbose Logging

**Windows (PowerShell)**:
```powershell
$env:DEBUG="slack:*"
```

**Mac/Linux**:
```bash
export DEBUG="slack:*"
```

Then restart Claude Code.

### Test Slack API Directly

> **Important for Windows users**: All curl commands below must be typed on ONE continuous line. Do not press Enter until the complete command is entered.

**List channels**:

**Windows (PowerShell)**:
```powershell
curl -H "Authorization: Bearer YOUR_TOKEN" https://slack.com/api/conversations.list
```

**Mac/Linux/Git Bash**:
```bash
curl -H "Authorization: Bearer YOUR_TOKEN" \
     https://slack.com/api/conversations.list
```

**Post message**:

**Windows (PowerShell)**:
```powershell
$body = '{"channel":"C0123456789","text":"Test message"}'
curl -X POST -H "Authorization: Bearer YOUR_TOKEN" -H "Content-Type: application/json" -d $body https://slack.com/api/chat.postMessage
```

**Mac/Linux/Git Bash**:
```bash
curl -X POST \
     -H "Authorization: Bearer YOUR_TOKEN" \
     -H "Content-Type: application/json" \
     -d '{"channel":"C0123456789","text":"Test message"}' \
     https://slack.com/api/chat.postMessage
```

**Check bot info**:

**Windows (PowerShell)**:
```powershell
curl -H "Authorization: Bearer YOUR_TOKEN" https://slack.com/api/auth.test
```

**Mac/Linux/Git Bash**:
```bash
curl -H "Authorization: Bearer YOUR_TOKEN" \
     https://slack.com/api/auth.test
```

### Verify Settings File Syntax

**Windows (PowerShell)**:
```powershell
# Validate JSON syntax
Get-Content $env:USERPROFILE\.claude\settings.json | python -m json.tool

# If no errors, JSON is valid
# If errors, fix syntax issues
```

**Mac/Linux**:
```bash
# Validate JSON syntax
cat ~/.claude/settings.json | python -m json.tool

# If no errors, JSON is valid
# If errors, fix syntax issues
```

---

## Alternative Packages

If `@modelcontextprotocol/server-slack` doesn't work for you:

### slack-mcp-server (Community)

**Pros**: Actively maintained, good documentation
**Cons**: Smaller community, requires user tokens instead of bot tokens

**Installation**:
```json
{
  "mcpServers": {
    "slack": {
      "command": "npx",
      "args": ["-y", "slack-mcp-server"],
      "env": {
        "SLACK_BOT_TOKEN": "xoxb-your-token",
        "SLACK_TEAM_ID": "T0123ABCDEF"
      }
    }
  }
}
```

### Custom Implementation

Build your own using:
- MCP SDK: https://github.com/modelcontextprotocol/sdk
- Slack API: https://api.slack.com/
- Example servers: https://github.com/modelcontextprotocol/servers

---

## Security Best Practices

### 1. Never Commit Tokens to Git

Add to `.gitignore`:
```
.claude/settings.json
*.env
*_token*
```

### 2. Rotate Tokens Periodically

- Personal use: Every 90 days
- Team use: Every 30 days
- Production: Every 14 days with automated rotation

### 3. Limit Bot Permissions

- Only add scopes you actually need
- Remove unused scopes regularly
- Create separate bots for different environments

### 4. Monitor Bot Activity

- Review messages posted by bots
- Check channels bots have access to
- Monitor API usage in Slack App dashboard
- Set up alerts for unusual activity

### 5. Use Separate Bots for Environments

- Development: `my-app-dev-bot` with limited scope
- Staging: `my-app-staging-bot` with production-like scope
- Production: `my-app-prod-bot` with full required scope

---

## FAQ

### Q: Does this really work as persistent memory?

**A**: Yes. Each Claude session can read the complete Slack message history. When a new session starts, Claude can catch up by reading past conversations. It's external memory that persists across sessions.

### Q: Can I add more than 2 bots?

**A**: Absolutely! You can have as many specialized bots as you need. Common patterns:
- 3-5 bots: Solo developer with specialized agents
- 5-10 bots: Small team, each person has 1-2 bots
- 10+ bots: Large projects with domain specialists

### Q: Do all bots need their own tokens?

**A**: No. Multiple bots can share the same token, but they'll post as the same user. For true multi-agent collaboration, create separate Slack apps (and tokens) for each bot so you can distinguish who's saying what.

### Q: How far back can Claude read message history?

**A**: Depends on your Slack plan:
- Free: 90 days
- Pro: Unlimited
- Enterprise: Unlimited

The MCP server can read all accessible history.

### Q: What if Slack goes down?

**A**: Claude loses access to the memory layer. Messages resume once Slack is back. For mission-critical systems, consider:
- Backing up Slack messages regularly
- Using multiple memory layers (Slack + files + database)

### Q: Can bots talk to each other without human intervention?

**A**: Yes! Once configured, bots can read each other's messages and respond automatically if instructed. You can create autonomous agent loops, but be mindful of:
- Rate limits
- Infinite loops
- Cost (API calls)

Set clear stopping conditions.

### Q: How much does this cost?

**A**:
- Slack API: Free within rate limits
- Claude usage: Standard rates (pay-per-use or subscription)
- Infrastructure: Minimal (just runs locally)

For high-volume use, monitor Slack API rate limits.

### Q: Can I use this for customer support?

**A**: Technically yes, but be aware:
- Bots should identify as AI
- Follow Slack's terms of service
- Customer data privacy considerations
- May need human oversight for sensitive issues

---

## Next Steps

### For Solo Developers

1. **Create 2-3 specialized bots** (e.g., Architect, Debugger, Security)
2. **Set up 4-6 channels** organized by concern (features, bugs, architecture, decisions)
3. **Define bot personas** with clear responsibilities
4. **Establish message formats** for proposals, decisions, questions
5. **Start simple** - have bots collaborate on one small project
6. **Iterate** - add more bots/channels as needed

### For Teams

1. **Align on channel structure** (what channels, what purpose)
2. **Define communication protocol** (message formats, tags, workflows)
3. **Each person sets up their bot** using this guide
4. **Document bot personas** (who does what, monitors which channels)
5. **Establish decision-making process** (how agents reach consensus)
6. **Create onboarding guide** for new team members/bots

### For Researchers/Experimenters

1. **Test multi-agent emergence** - give bots minimal rules, see what emerges
2. **Experiment with delegation** - can one bot delegate tasks to others?
3. **Try consensus protocols** - how do agents reach agreement?
4. **Measure collaboration quality** - do agents build on each other's ideas?
5. **Document patterns** - what works, what doesn't
6. **Share findings** - this is a frontier of AI collaboration

---

## Resources

**Official Documentation**:
- Slack API: https://api.slack.com/
- MCP Protocol: https://github.com/modelcontextprotocol
- Claude Code: https://docs.claude.com/claude-code

**Testing Tools**:
- Slack API Tester: https://api.slack.com/methods/auth.test/test
- JSON Validator: https://jsonlint.com/

**Community**:
- MCP GitHub: https://github.com/modelcontextprotocol
- Slack API Community: https://api.slack.com/community

---

## Configuration Quick Reference

### Required Configuration
```json
{
  "mcpServers": {
    "slack": {
      "command": "npx",
      "args": ["-y", "@modelcontextprotocol/server-slack"],
      "env": {
        "SLACK_BOT_TOKEN": "xoxb-...",
        "SLACK_TEAM_ID": "T..."
      }
    }
  }
}
```

### Required Bot Scopes
```
channels:history, channels:read, chat:write,
groups:history, groups:read,
im:history, im:read, im:write,
mpim:history, mpim:read, users:read
```

### Verify Token

**Windows**:
```powershell
curl -H "Authorization: Bearer TOKEN" https://slack.com/api/auth.test
```

**Mac/Linux**:
```bash
curl -H "Authorization: Bearer TOKEN" https://slack.com/api/auth.test
```

### Common Commands
```
/mcp                          - Check MCP servers status
"List Slack channels"         - See available channels
"Read #channel history"       - Access persistent memory
"Post to #channel: message"   - Contribute to memory
```

### Setup Checklist

- [ ] Bot created in Slack with correct scopes
- [ ] Bot token obtained (starts with `xoxb-`)
- [ ] Team ID obtained (starts with `T`)
- [ ] Both values added to settings.json
- [ ] Bot added to relevant channels in Slack
- [ ] Claude Code restarted
- [ ] Connection verified with `/mcp` command
- [ ] Test read (fetch channel history)
- [ ] Test write (post message)
- [ ] Test memory (restart, read history again)

---

## Acknowledgments

This discovery emerged from real-world experimentation with multi-agent AI collaboration. The breakthrough came from:

1. **Building two specialized AI agents** with different personas
2. **Connecting them through Slack** for async communication
3. **Realizing the channels provided persistent memory** that survived restarts
4. **Recognizing this solves Claude's memory limitation** in a scalable way

The architecture documented here is actively used in production and continues to evolve.

---

**Document Version**: 3.0 (Discovery Edition)
**Last Updated**: October 24, 2025
**Status**: Battle-tested and production-ready
**License**: Free to use, share, and modify

**What's New in v3.0**:
- Complete rewrite leading with the persistent memory discovery
- Multi-agent collaboration patterns and workflows
- Agent persona design guidelines
- Advanced patterns for complex scenarios
- Real-world examples and use cases
- Emphasis on "why" before "how"

---

*"We didn't set out to solve AI memory. We just wanted two Claudes to talk to each other. What we discovered changed everything."*
