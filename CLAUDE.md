# CLAUDE.md - LinkedIn Easy Apply Automation

This file provides guidance to Claude Code for automating LinkedIn Easy Apply job applications.

## Repository Purpose

Automate LinkedIn Easy Apply job applications using Claude Code + Chrome DevTools MCP.

**Features:**
- Automated job fetching based on your search criteria
- Form filling with your profile info, skills, and experience answers
- Sequential application with anti-detection delays
- CSV tracking for all applications

---

## Setup Requirements

### 1. Install Claude Code
Download and install from: https://claude.ai/code

### 2. Configure Chrome DevTools MCP Server

Add this to your `.claude.json` (usually in `~/.claude.json` or `~/.claude-personal/.claude.json`):

```json
{
  "mcpServers": {
    "chrome-devtools": {
      "type": "stdio",
      "command": "npx",
      "args": [
        "chrome-devtools-mcp@latest",
        "--userDataDir=C:\\Users\\YourUsername\\.chrome-debug-linkedin"
      ],
      "env": {}
    }
  }
}
```

**Important:** Replace `YourUsername` with your actual Windows username.

### 3. Configure Your Profile

Edit `config.json` with your details:
- Personal info (name, email, phone, LinkedIn, portfolio)
- Job search criteria (role, keywords, location, salary expectations)
- Skills with years of experience
- LinkedIn search URL

### 4. Add Your Experience Answers

Edit `data/EXPERIENCE_ANSWERS.md` with your personalized answers to common application questions.

### 5. First-Time LinkedIn Login

1. Open Claude Code in this directory
2. Ask: "Navigate to LinkedIn"
3. Log in manually in the browser that opens
4. Your session will persist for future runs

---

## Usage

### Basic Usage

**Fetch fresh jobs:**
```
Find me fresh Easy Apply jobs
```

**Apply to jobs:**
```
Apply to jobs
```

**Apply to a specific job:**
```
Apply to this job: https://linkedin.com/jobs/view/123456
```

### Workflow

1. **Fetch jobs** - Agent navigates to LinkedIn, finds 5-7 fresh Easy Apply jobs
2. **Apply sequentially** - Agent applies to each job with 3-5 min delays
3. **Track in CSV** - All applications logged to `tracking.csv`

---

## File Structure

```
.
|-- config.json              # Your profile and job search settings
|-- tracking.csv             # Application tracking log
|-- data/
|   |-- EXPERIENCE_ANSWERS.md  # Your answers to common questions
|-- .claude/
|   |-- agents/
|       |-- job-fetcher.md       # Job fetching agent
|       |-- easy-apply-automator.md  # Application automation agent
|-- docs/
    |-- CHROME_MCP_SETUP.md      # Detailed MCP setup guide
    |-- ANTI_DETECTION.md        # Rate limits and safety
```

---

## Configuration Reference

### config.json Fields

| Field | Description | Example |
|-------|-------------|---------|
| `profile.name` | Your full name | "John Doe" |
| `profile.email` | Email for applications | "john@example.com" |
| `profile.phone` | Phone with country code | "+91-9876543210" |
| `job_search.role` | Target job title | "Financial Analyst" |
| `job_search.keywords` | Search keywords array | ["Financial Analyst", "Finance Analyst"] |
| `job_search.location` | Job location | "India" |
| `salary.current_ctc` | Current salary (LPA) | "12" |
| `salary.expected_ctc` | Expected salary (LPA) | "15" |
| `salary.notice_period` | Notice period in days | "30" |
| `skills.*` | Skill name: years | "Excel": 4 |
| `linkedin_search_url` | Pre-configured search URL | See default in config.json |
| `daily_target` | Target applications/day | 20 |

---

## Safety & Rate Limits

**Daily Limits (Stay Safe):**
- Easy Apply applications: 50-100/day max
- Recommended target: 20/day
- Always use sequential applications with 3-5 min delays

**Warning Signs (Stop Immediately):**
- "We limit daily submissions" message
- HTTP 429 errors
- Account warnings

**If Rate Limited:**
1. Stop all automation
2. Wait 30-60 minutes
3. Resume with lower volume

See `docs/ANTI_DETECTION.md` for full safety guidelines.

---

## Agents

### job-fetcher
- Navigates to LinkedIn search URL from config
- Returns 5-7 fresh Easy Apply jobs
- Prioritizes recent posts with few applicants

### easy-apply-automator
- Completes Easy Apply forms
- Uses config.json for contact/salary info
- Uses EXPERIENCE_ANSWERS.md for text questions
- Handles multi-step applications

---

## Tracking

All applications are logged to `tracking.csv`:

```csv
Date,Channel,Company,Contact,Role,Status,Notes,Next Action,Priority,JobURL
2025-12-08,LinkedIn Easy Apply,Acme Corp,,Financial Analyst,Applied,Posted 1h ago | 15 applicants | Mumbai,Wait for response,High,https://linkedin.com/jobs/view/123/
```

---

## Troubleshooting

### "LOGIN_REQUIRED" Error
- Navigate to LinkedIn manually and log in
- Session should persist after first login

### Chrome Doesn't Open
- Verify Chrome DevTools MCP is configured in `.claude.json`
- Check the userDataDir path exists and is writable

### Forms Not Filling Correctly
- Update `data/EXPERIENCE_ANSWERS.md` with more specific answers
- Check `config.json` skills match what applications ask

### Rate Limited
- Stop immediately
- Wait 30-60 minutes
- Reduce daily target

---

## Best Practices

1. **Start slow** - 10-15 applications/day for first week
2. **Personalize answers** - Generic answers get flagged
3. **Monitor tracking.csv** - Spot issues early
4. **Check LinkedIn manually** - Verify applications went through
5. **Update config regularly** - Keep skills and salary current
