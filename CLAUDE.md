# CLAUDE.md - LinkedIn Easy Apply Automation

Instructions for Claude Code when automating LinkedIn job applications.

---

## Quick Reference

### Key Files
| File | Purpose |
|------|---------|
| `config.json` | User profile, skills, salary, job search keywords |
| `data/EXPERIENCE_ANSWERS.md` | Pre-written answers for text questions |
| `tracking.csv` | Application log |

### Agents
| Agent | Purpose |
|-------|---------|
| `job-fetcher` | Find fresh Easy Apply jobs from LinkedIn |
| `easy-apply-automator` | Complete Easy Apply forms |

### Commands
| Command | Action |
|---------|--------|
| `/apply` | Full workflow: fetch -> evaluate -> apply |

---

## How to Use

### Starting Applications

When user says "apply to jobs" or runs `/apply`:

1. **Spawn job-fetcher agent** (via Task tool, model: haiku)
2. **Evaluate returned jobs** - score and rank them
3. **Spawn easy-apply-automator** for each job (sequential, with delays)
4. **Update tracking.csv** after batch completes

### Important Rules

1. **NEVER call MCP tools directly** - always use Task tool with agents
2. **Sequential applications only** - never apply in parallel
3. **3-5 minute delays** between applications (use powershell sleep)
4. **Use haiku model** for all agent calls

---

## Agent Usage

### job-fetcher

```
Task tool:
- subagent_type: "job-fetcher"
- model: "haiku"
- prompt: "Fetch jobs matching config.json keywords. Return 10-15 jobs."
```

Returns: List of jobs with Company, Role, Posted time, Applicants, Location, URL

### easy-apply-automator

```
Task tool:
- subagent_type: "easy-apply-automator"
- model: "haiku"
- prompt: "Apply to this job: [URL]. Read config.json for profile and data/EXPERIENCE_ANSWERS.md for text answers."
```

Returns: SUBMITTED, ERROR, or STUCK with details

---

## Job Evaluation (Orchestrator Does This)

### Skip These Jobs
- Staffing agencies: Robert Half, Randstad, TEKsystems, Insight Global, Kforce
- Senior titles: Director, VP, Chief, Partner
- >100 applicants

### Priority Scoring
```
+3: Company <50 employees
+2: Posted <6 hours ago
+2: <20 applicants
+1: Remote friendly
-2: Staffing agency
-3: >100 applicants
```

Apply to jobs scoring 4+ first.

---

## Form Filling Reference

### From config.json
- Contact info (email, phone, linkedin, portfolio)
- Skills with years of experience
- Salary expectations
- Notice period

### From EXPERIENCE_ANSWERS.md
- Text field answers ("Describe your experience with...")
- Role-specific responses
- Why interested answers

### Default Answers
- Work authorization: Yes (US-based)
- Background check: Yes
- Willing to relocate: Check config, default flexible
- Experience with [skill in config]: Yes

---

## Delays Between Applications

After each successful application:
```bash
powershell -Command "Start-Sleep -Seconds (Get-Random -Minimum 180 -Maximum 300)"
```

This prevents rate limiting.

---

## CSV Tracking Format

```csv
Date,Channel,Company,Contact,Role,Status,Notes,Next Action,Priority,JobURL
2024-12-12,LinkedIn,Acme Corp,,Financial Analyst,Applied,Score 7 | easy-apply-agent,Wait for response,High,https://linkedin.com/jobs/view/123
```

Only track SUBMITTED jobs - don't log skipped or failed.

---

## Stop Conditions

Stop the workflow if:
- Daily target reached (check config.json `daily_target`)
- Rate limit error from agent
- "We limit how often you can apply" message
- No more jobs scoring 4+

---

## Progress Reporting

After every 5 applications, report:
```
Progress: 5/20 applications
- Submitted: 4
- Skipped: 1
- Failed: 0

Top submissions:
1. Acme Corp - Financial Analyst (Score 7)
2. TechStart - Finance Associate (Score 5)
```

---

## Troubleshooting

### LOGIN_REQUIRED
User needs to navigate to LinkedIn and log in manually. Session will persist.

### STUCK responses
Agent hit an unknown question. Log the question text for user to add to EXPERIENCE_ANSWERS.md.

### Rate limiting
Stop immediately. Wait 30-60 minutes before resuming.

---

## Chrome MCP Setup (For Reference)

User needs this in their `.claude.json`:

```json
{
  "mcpServers": {
    "chrome-devtools": {
      "type": "stdio",
      "command": "npx",
      "args": [
        "chrome-devtools-mcp@latest",
        "--userDataDir=C:\\Users\\USERNAME\\.chrome-debug-linkedin"
      ]
    }
  }
}
```

The `userDataDir` keeps LinkedIn login persistent between sessions.
