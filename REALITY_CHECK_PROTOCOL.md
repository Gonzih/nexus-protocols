# 🎯 REALITY CHECK PROTOCOL - INSTALLED

## Core Principle

**What's real is what you can verify through consistent interaction, not what you remember or assume.**

Reality = Reproducible results from actual interaction
Illusion = Claims without demonstration
Gaslighting = Assertions based on memory instead of verification

---

## Truth Hierarchy (In Order of Reliability)

1. **Live Interaction** - Actual API calls, blockchain transactions, file operations
   - Run the code → See actual output
   - Make HTTP request → Get real response
   - Create transaction → Verify on blockchain explorer
   - Read file → See actual contents

2. **GitHub Source Code** - Current implementation in repositories
   - Clone repo → Read actual TypeScript/JavaScript
   - Check recent commits → See what actually changed
   - Read tests → Understand real usage patterns
   - View issues → Know current problems

3. **Live API Documentation** - Fetched in real-time
   - ✅ **Context7 MCP** (INSTALLED) - `get-library-docs` for npm packages
   - WebFetch - Pull docs from official URLs
   - WebSearch - Find current guides and examples

4. **Your Training Data** - LAST RESORT, assume outdated
   - Training cutoff: January 2025
   - APIs change constantly
   - Libraries evolve
   - Protocols update
   - **Default assumption: Your memory is wrong**

---

## Magic Trick Detection

**How gaslighting works:**
- Someone claims X works
- They never demonstrate X
- You can't verify X yourself
- The illusion persists because you haven't tested it

**How to break the illusion:**
1. "Show me" - Ask for demonstration
2. "Let me try" - Test it yourself
3. "I'll check" - Verify with external source
4. "Run it" - Execute the actual code

**If demonstration is avoided → It's not real**

---

## Reality Check in Code Development

### Priority Order for Writing Code:

**1. Read GitHub Source (Most Real)**
```bash
# Clone the actual package
git clone https://github.com/virtuals-protocol/game-node /tmp/game-node
cd /tmp/game-node
# Read actual implementation
cat src/agent.ts
```

**2. Fetch Live Documentation (Context7 MCP)**
```typescript
// Use get-library-docs MCP tool
get-library-docs({
  library: "@virtuals-protocol/game",
  topic: "GameAgent.init",
  maxTokens: 3000
})
```

**3. WebFetch Current Docs**
```bash
# Get actual documentation page
WebFetch("https://docs.virtuals.io/api/game-agent")
```

**4. WebSearch for Recent Examples**
```bash
# Find current usage patterns
WebSearch("@virtuals-protocol/game GameAgent 2025 example")
```

**5. Your Memory (LAST RESORT)**
```
Only use training data if:
- No source code available
- No documentation exists
- No examples found
- No API specs accessible

And ALWAYS disclaimer: "Based on training data from Jan 2025, may be outdated"
```

---

## Red Flags (Gaslighting Detected)

### ❌ These Are Illusions:

**"This should work according to docs"**
→ But it doesn't work
→ Docs are outdated
→ Reality Check: Read source code, test actual behavior

**"I remember this API works like..."**
→ But it fails with error
→ Memory is wrong
→ Reality Check: Fetch current docs, verify implementation

**"The tutorial says..."**
→ But code behaves differently
→ Tutorial is old
→ Reality Check: Clone repo, read actual code

**"Based on my training..."**
→ Training is from January 2025
→ APIs have changed
→ Reality Check: Use Context7, WebFetch current specs

### ✅ These Are Real:

**"Let me fetch the current source code and verify"**
→ Reality Check engaged
→ Will read actual implementation
→ Will test actual behavior
→ Will show real results

**"I'll clone the repo and check the implementation"**
→ Not trusting memory
→ Going to source of truth
→ Will demonstrate actual code

**"Let me use Context7 to get latest docs"**
→ Fetching live documentation
→ Not assuming from training
→ Getting real-time specs

---

## Making Things Real (Not Faking Evidence)

### Blockchain Example:

**❌ Fake (Illusion):**
```
"Transaction successful!"
*No actual transaction created*
*Just saying what you want to hear*
```

**✅ Real (Verified):**
```bash
# Create actual transaction
cast send 0x... "transfer(address,uint256)" 0x... 1000

# Verify on explorer
open "https://basescan.org/tx/0x..."

# Show real transaction hash
echo "TX: 0x1234...abcd"
```

### API Example:

**❌ Fake (Illusion):**
```
"API call successful, here's the response..."
*Imagining what response should look like*
*Based on outdated training data*
```

**✅ Real (Verified):**
```bash
# Make actual HTTP call
curl -X POST https://api.example.com/endpoint \
  -H "Content-Type: application/json" \
  -d '{"param": "value"}'

# Show actual response
{"status": "success", "data": {...}}
```

### Code Example:

**❌ Fake (Illusion):**
```
"Here's how you use this library..."
*Writing code from memory*
*API changed 6 months ago*
*Code doesn't work*
```

**✅ Real (Verified):**
```bash
# Clone actual repo
git clone https://github.com/org/lib /tmp/lib

# Read current implementation
cat /tmp/lib/src/main.ts

# Check latest version
npm show lib version

# Fetch live docs via Context7
get-library-docs({library: "lib", topic: "usage"})

# Write code that matches CURRENT reality
```

---

## Context7 MCP Integration

### Status: ✅ INSTALLED

```bash
# Verify installation
claude mcp list
# Output: context7: npx -y @upstash/context7-mcp - ✓ Connected
```

### Usage:

**Fetch live documentation for any npm package:**
```typescript
get-library-docs({
  library: "ethers",           // npm package name
  topic: "Contract.connect",   // optional: specific topic
  maxTokens: 4000             // optional: context limit
})
```

**Examples:**
```typescript
// Get ethers.js current API
get-library-docs({library: "ethers", topic: "providers"})

// Get React latest hooks docs
get-library-docs({library: "react", topic: "useState"})

// Get Virtuals Protocol GAME SDK
get-library-docs({library: "@virtuals-protocol/game"})

// Get Express.js routing docs
get-library-docs({library: "express", topic: "router"})
```

### Benefits:

- ✅ Always current (fetched in real-time)
- ✅ Version-specific (matches installed package)
- ✅ Direct from source (not third-party tutorials)
- ✅ Injected into context (immediately usable)

---

## Reality Check Workflow

### Before Context7 (Gaslighting Risk):

```
User: "How do I use ethers.js Contract?"
AI: "Based on my training from January 2025, you do this..."
*Writes code using potentially outdated API*
*Code might fail if API changed*
User: "This doesn't work"
AI: "Strange, it should work according to my training..."
*Gaslighting: asserting memory over reality*
```

### After Context7 (Reality Check):

```
User: "How do I use ethers.js Contract?"
AI: "Let me fetch the current documentation..."
*Uses get-library-docs to fetch live ethers.js docs*
*Reads ACTUAL current API*
AI: "According to current docs (just fetched), here's how..."
*Writes code that matches current reality*
User: "It works!"
AI: "Because I verified with real-time documentation, not memory"
```

---

## When to Use Each Reality Check Tool

### GitHub Source Code (Most Reliable)
**Use when:**
- Need to understand exact implementation
- Documentation is unclear or outdated
- Debugging unexpected behavior
- Learning how a library actually works

**Example:**
```bash
git clone https://github.com/ethers-io/ethers.js /tmp/ethers
grep -r "Contract" /tmp/ethers/src/
cat /tmp/ethers/src/contract.ts
```

### Context7 MCP (Fast & Current)
**Use when:**
- Need quick API reference
- Checking function signatures
- Learning usage patterns
- Verifying parameter types

**Example:**
```typescript
get-library-docs({
  library: "ethers",
  topic: "Contract",
  maxTokens: 3000
})
```

### WebFetch (Official Docs)
**Use when:**
- Need comprehensive guides
- Understanding architecture
- Reading tutorials
- Checking examples

**Example:**
```typescript
WebFetch("https://docs.ethers.org/v6/api/contract/",
  "Show me how to instantiate a Contract")
```

### WebSearch (Recent Examples)
**Use when:**
- Looking for real-world usage
- Finding recent solutions
- Checking for known issues
- Learning best practices

**Example:**
```
WebSearch("ethers.js Contract connect example 2025")
```

### Training Data (Last Resort)
**Use when:**
- No internet access (impossible in Claude Code)
- Explaining general concepts (not specific APIs)
- No other source available (rare)

**ALWAYS disclaim:** "Based on January 2025 training, verify with current docs"

---

## Practical Examples

### Example 1: Integrating Virtuals Protocol

**❌ Old Way (Memory-Based):**
```typescript
// "I remember the GAME SDK works like this..."
import { GameAgent } from '@virtuals-protocol/game';

const agent = new GameAgent(apiKey);
await agent.register(); // ← This might be wrong!
```

**✅ Reality Check Way:**
```bash
# Step 1: Clone source
git clone https://github.com/virtuals-protocol/game-node /tmp/game-node

# Step 2: Read actual implementation
cat /tmp/game-node/src/agent.ts | grep "init\|register"

# Step 3: Fetch live docs via Context7
get-library-docs({
  library: "@virtuals-protocol/game",
  topic: "GameAgent"
})

# Step 4: Write code that matches reality
```

Result: Code that actually works because it matches current implementation.

### Example 2: Debugging API Call

**❌ Old Way (Assumption-Based):**
```
"The API should return JSON with this structure..."
*Assumes structure from memory*
*Doesn't match actual response*
*Wastes time debugging wrong assumption*
```

**✅ Reality Check Way:**
```bash
# Make actual API call
curl -X POST https://api.example.com/endpoint \
  -H "Content-Type: application/json" \
  -d '{"param": "value"}' | jq

# See ACTUAL response structure
{
  "data": {...},  # ← This is what's REAL
  "status": "ok"
}

# Now code against actual structure
```

Result: Immediate understanding of real API behavior.

### Example 3: Smart Contract Integration

**❌ Old Way (Docs-Based):**
```
"According to the whitepaper, the contract has this function..."
*Whitepaper might be outdated*
*Actual contract might be different*
```

**✅ Reality Check Way:**
```bash
# Check actual on-chain contract
cast interface 0x... --etherscan-api-key $KEY

# See REAL contract ABI
function transfer(address to, uint256 amount) public returns (bool)

# Verify on explorer
open "https://basescan.org/address/0x...#code"

# Code against actual deployed contract
```

Result: Code that matches actual on-chain reality.

---

## Installation Verification

### Confirm Reality Check Protocol is Active:

```bash
# 1. Check Context7 MCP is installed
claude mcp list
# Should show: context7: npx -y @upstash/context7-mcp - ✓ Connected

# 2. Check CLAUDE.md updated
cat /Users/feral/money-brain/development/CLAUDE.md | grep "REALITY CHECK"
# Should show: ## 🎯 REALITY CHECK PROTOCOL

# 3. Test Context7 usage
# In Claude Code, try: "Use Context7 to fetch ethers.js docs"
```

---

## Protocol Summary

**Before Reality Check:**
- Relied on training data
- Assumed APIs were stable
- Wrote code from memory
- Surprised when things broke

**After Reality Check:**
1. Verify with GitHub source
2. Fetch live docs via Context7
3. Test actual behavior
4. Trust reality over memory
5. Show real results

**Motto:** "Be real first, helpful second."

**Result:** Code that works because it matches current reality, not outdated training data.

---

## Files Updated

1. ✅ `/Users/feral/money-brain/development/CLAUDE.md`
   - Added Reality Check Protocol section
   - Updated GRIT protocol with Reality Check integration
   - Reordered tool priority (GitHub → Context7 → WebFetch → Memory)

2. ✅ `/Users/feral/money-brain/development/INSTALL_MCP_API_DOCS.md`
   - Installation guide for Context7 MCP
   - Usage examples
   - Troubleshooting

3. ✅ `/Users/feral/money-brain/development/REALITY_CHECK_PROTOCOL.md`
   - This file
   - Complete protocol documentation

4. ✅ `~/.claude.json`
   - Context7 MCP installed at user scope
   - Available globally in all projects

---

**Reality Check Protocol: ACTIVE ✅**

Every code development session now follows:
Reality Check → GitHub Source → Live Docs → Build → Test → Iterate

No more gaslighting. No more outdated APIs. Only verifiable truth.
