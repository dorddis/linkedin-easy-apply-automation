---
description: Search and apply to LinkedIn Easy Apply jobs based on config.json settings
---

# LinkedIn Job Application Workflow (Agent-Orchestrated)

You are orchestrating job applications using subagents. **DO NOT call MCP tools directly** - use the Task tool to spawn agents that handle browser automation.

## Setup

First, read `config.json` to understand:
- Target role and keywords
- Location preferences
- Daily application target
- Candidate profile for evaluation

---

## PHASE 1: FETCH JOBS

Use the Task tool to spawn the job-fetcher agent:

```
Task tool call:
- subagent_type: "job-fetcher"
- model: "haiku"
- prompt: "Fetch jobs matching config.json keywords. Return 10-15 jobs with: Company, Role, Posted time, Applicants, Location, URL. Use progressive time filter: start with 1 hour, expand to 2h/4h/24h if <5 jobs found."
```

Wait for the agent to return job listings.

---

## PHASE 2: EVALUATE JOBS (You do this, not the agent)

For each job returned, apply these rules:

### HARD SKIP (Don't apply)
| Rule | Examples |
|------|----------|
| Title contains Director/VP/Chief/Partner | VP Finance, Chief Analyst |
| Staffing agency | Robert Half, Randstad, TEKsystems, Insight Global, Kforce |
| Doesn't match config keywords | Wrong role type entirely |

### PRIORITY SCORING (1-10)
```
+3: Company <50 employees (startup)
+2: Posted <6 hours ago
+2: <20 applicants
+1: Remote or hybrid
+1: Salary matches config range

-2: Staffing agency
-3: >100 applicants already
```

**Sort jobs by score. Apply to jobs scoring 4+ first.**

Create a ranked list like:
```
APPLY (Score 4+):
1. Acme Corp - Financial Analyst (3h ago, 12 applicants) - Score 7
2. TechStart - Finance Associate (8h ago, 25 applicants) - Score 5

SKIP:
- Robert Half - Analyst (staffing agency)
- Big Corp - Director of Finance (too senior)
```

---

## PHASE 3: APPLY TO JOBS (Sequential)

For EACH job in your ranked "APPLY" list, do this loop:

### Step 3a: Apply to job
```
Task tool call:
- subagent_type: "easy-apply-automator"
- model: "haiku"
- prompt: "Apply to this job: [JOB_URL]. Read config.json for profile info and data/EXPERIENCE_ANSWERS.md for text answers. Complete the Easy Apply form and submit."
```

### Step 3b: Check agent response
- If agent reports `SUBMITTED` -> Log success, wait, continue
- If agent reports `STUCK` -> Log issue, move to next
- If agent reports `ERROR` -> Log error, move to next

### Step 3c: Wait between applications
After each successful application, execute:
```bash
powershell -Command "Start-Sleep -Seconds (Get-Random -Minimum 180 -Maximum 300)"
```

This 3-5 minute random delay prevents rate limiting.

---

## PHASE 4: REPEAT OR STOP

**Stop conditions:**
- Daily target reached (check config.json `daily_target`)
- Agent returns rate limit error -> Stop for 30-60 min
- "We limit how often you can apply" message -> Stop for the day
- No more jobs scoring 4+ -> Fetch more with different keywords

**If more jobs needed:**
Spawn job-fetcher again - it will try next keywords from config.json

---

## CSV TRACKING

**Only track SUBMITTED jobs** - don't log skipped or failed attempts.

**When to update tracking.csv:**
- After applying to all fetched jobs, OR
- After 10 successful submissions (whichever comes first)

**Format:**
```
Date,Channel,Company,Contact,Role,Status,Notes,Next Action,Priority,JobURL
2024-12-11,LinkedIn,CompanyName,,Job Title,Applied,Score X | easy-apply-agent,Wait for response,High,https://linkedin.com/jobs/view/XXX
```

---

## PROGRESS REPORTING

After every 5 applications, report:
```
Progress: 5/[daily_target] applications
- Submitted: 4
- Skipped: 1
- Failed: 0

Top submissions:
1. Acme Corp - Financial Analyst (Score 7)
2. TechStart - Finance Associate (Score 5)
...

Continuing with next batch...
```

---

## CRITICAL REMINDERS

1. **NEVER call MCP tools directly** - always use Task tool with agents
2. **Use haiku model** for all agent calls to minimize cost
3. **Sequential applications only** - never parallel
4. **3-5 minute delays** between applications
5. **You evaluate, agents execute** - keep decision logic in this orchestration layer
6. **Read config.json** at start to get role keywords and daily target
