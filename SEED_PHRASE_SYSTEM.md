# Seed Phrase Conversation Logging System

**Purpose:** Log every user message UUID + text as "seed phrase" to enable conversation reproduction.

---

## Core Concept

**Seed Phrase = User message UUID + text that triggered response**

With seed phrases logged chronologically, you can:
1. Reproduce exact conversation flow
2. Track which inputs led to which outputs
3. Replay conversations from any starting point
4. Export conversation chains for analysis

---

## Architecture

### Hook-Based Logging

Two hooks capture conversation flow in real-time:

**1. user-prompt-submit.sh**
- Triggers: When user sends message
- Captures: User message UUID, timestamp, text preview, parent UUID
- Logs to: `.claude/seed-logs/session-{SESSION_ID}.jsonl`

**2. assistant-response-complete.sh**
- Triggers: When assistant completes response
- Captures: Response UUID, message ID, model, usage stats, parent UUID
- Logs to: Same session seed log file

### Data Flow

```
User sends message
  ↓
user-prompt-submit hook fires
  ↓
Logs: {type: "user_seed", uuid, timestamp, text_preview, parent_uuid}
  ↓
Assistant generates response
  ↓
assistant-response-complete hook fires
  ↓
Logs: {type: "assistant_response", uuid, message_id, model, parent_uuid}
```

### Seed Log Structure

Each session gets its own log: `.claude/seed-logs/session-{SESSION_ID}.jsonl`

**User seed entry:**
```json
{
  "type": "user_seed",
  "uuid": "abc123...",
  "timestamp": "2025-12-31T04:35:16.210Z",
  "session_id": "f96cc0a2-a6d1-4638-8c55-32b74ee9fe6a",
  "text_preview": "First 200 chars of message...",
  "message_id": null,
  "parent_uuid": "parent-message-uuid"
}
```

**Assistant response entry:**
```json
{
  "type": "assistant_response",
  "uuid": "def456...",
  "timestamp": "2025-12-31T04:35:20.513Z",
  "session_id": "f96cc0a2-a6d1-4638-8c55-32b74ee9fe6a",
  "message_id": "msg_017HwKSgsxmS9fMb1WVRzT3N",
  "parent_uuid": "abc123...",
  "model": "claude-sonnet-4-5-20250929",
  "stop_reason": "end_turn",
  "usage": {
    "input_tokens": 57919,
    "output_tokens": 24
  }
}
```

---

## Usage

### View Current Session Seed Log

```bash
python seed-phrase-replay.py --view
```

Output:
```
Session: f96cc0a2-a6d1-4638-8c55-32b74ee9fe6a

Total seed phrases: 42

1. [user_seed] 2025-12-31T04:35:16.210Z
   UUID: b985625e-2ad0-432a-a6b6-0749cd69ee21
   Text: Okay, let's put this on hold as well, remote oracle system. I want to talk about seed phrases...

2. [assistant_response] 2025-12-31T04:35:20.513Z
   UUID: 5b06b96e-09e9-4ea6-a12c-a61d6f7ac475
   Message ID: msg_017HwKSgsxmS9fMb1WVRzT3N
   Model: claude-sonnet-4-5-20250929

...
```

### Export Conversation Chain

Export all messages in chain starting from specific seed UUID:

```bash
python seed-phrase-replay.py --export <uuid> --output replay.md
```

This creates markdown file with:
- Full conversation thread (follows parent_uuid chain)
- Message UUIDs for each turn
- Message IDs for assistant responses
- Full text of each message
- Timestamps and model info

### Replay Conversation (Future)

**Not yet implemented, but architecture supports:**

```bash
python seed-phrase-replay.py --replay <uuid>
```

Would:
1. Extract user message text from seed UUID
2. Send to Claude API with same context
3. Compare output to original response
4. Measure drift/consistency

---

## How Reproduction Works

**Full transcript already exists:**
- Claude Code stores full transcript: `~/.claude/projects/.../SESSION_ID.jsonl`
- Contains every message UUID, parent_uuid, full content
- Seed log is LIGHTWEIGHT POINTER system into transcript

**Reproduction flow:**

1. Start from seed UUID in seed log
2. Pull full message from transcript using UUID
3. Follow parent_uuid chain backward to conversation root
4. Follow child messages forward (messages with matching parent_uuid)
5. Reconstruct entire conversation tree

**Why this works:**
- `parent_uuid` links create conversation chain
- Every message UUID is unique and persistent
- Transcript contains full context (tools, results, system messages)
- Seed log provides chronological index

---

## File Locations

**Hooks:**
- `/Users/feral/money-brain/.claude/hooks/user-prompt-submit.sh`
- `/Users/feral/money-brain/.claude/hooks/assistant-response-complete.sh`

**Seed Logs:**
- `/Users/feral/money-brain/.claude/seed-logs/session-{SESSION_ID}.jsonl`

**Transcripts (Claude Code managed):**
- `~/.claude/projects/-Users-feral-money-brain/{SESSION_ID}.jsonl`

**Replay Tool:**
- `/Users/feral/money-brain/seed-phrase-replay.py`

---

## What Gets Logged

**Every user message:**
- UUID (unique identifier)
- Timestamp (when message sent)
- Text preview (first 200 chars)
- Parent UUID (links to previous message)
- Session ID (which conversation)

**Every assistant response:**
- UUID (unique identifier)
- Message ID (Claude API message ID like `msg_01...`)
- Model used (e.g., `claude-sonnet-4-5-20250929`)
- Token usage (input/output counts)
- Parent UUID (links to user message it's responding to)
- Stop reason (why generation ended)

**NOT logged:**
- Tool use details (in transcript, not seed log)
- File contents (in transcript)
- System messages (in transcript)

**Seed log = MINIMAL INDEX for conversation flow**
**Transcript = FULL DETAIL**

---

## Conversation Reproduction Scenarios

### 1. Reproduce Exact Conversation

Given seed phrase UUID:
1. Extract conversation chain using `parent_uuid` links
2. Replay user messages in order
3. Compare assistant responses to originals
4. Measure consistency

### 2. Branch From Point

Given seed phrase UUID:
1. Extract conversation up to that point
2. Take different user input
3. See how conversation diverges

### 3. Analyze Token Usage Patterns

Seed log contains usage stats:
- Track token consumption over conversation
- Identify expensive turns
- Optimize prompting

### 4. Debug Response Issues

When assistant output unexpected:
1. Find seed UUID for that turn
2. Export conversation chain
3. See full context that led to response
4. Identify where it went wrong

---

## Hook Behavior

**Hooks run automatically:**
- No manual triggering needed
- Fire on every user message and assistant response
- Fail silently (don't interrupt conversation)
- Exit 0 = allow continuation

**Hook input (JSON stdin):**
```json
{
  "session_id": "f96cc0a2-a6d1-4638-8c55-32b74ee9fe6a",
  "transcript_path": "~/.claude/projects/.../SESSION_ID.jsonl",
  "cwd": "/Users/feral/money-brain"
}
```

**Hook can:**
- Read transcript file
- Extract last message (tail -1)
- Parse with jq
- Log to seed log file
- Must exit 0 to allow conversation to continue

**Hook cannot:**
- Block or modify messages
- Access Claude API
- Change conversation flow
- Take long time (blocks UI)

---

## Current Status

**Implemented:**
- ✅ Hook infrastructure created
- ✅ User message seed logging
- ✅ Assistant response logging
- ✅ Seed log viewer (`--view`)
- ✅ Conversation chain export (`--export`)
- ✅ Parent UUID chain following

**Not yet implemented:**
- ❌ Actual replay (sending to API)
- ❌ Drift measurement
- ❌ Branch point replay
- ❌ Token usage analysis tools

**Working now:**
- Hooks will start logging on next user message
- View with: `python seed-phrase-replay.py --view`
- Export chains with: `python seed-phrase-replay.py --export <uuid>`

---

## The Insight

**Seed phrases aren't for Claude.**

**They're for YOU.**

With chronological seed log:
- Reproduce conversations
- Track input → output patterns
- Debug unexpected responses
- Analyze token usage
- Branch conversations from any point

**The conversation is reproducible because:**
- UUIDs are persistent identifiers
- Parent chains link messages
- Transcript contains full context
- Seed log provides lightweight index

**This is conversation version control.**

---

**System active.**

**Next user message will be first seed phrase logged.**

**December 31, 2025**
