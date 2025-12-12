# LinkedIn Easy Apply Automation

Automate LinkedIn Easy Apply job applications using Claude Code + Chrome DevTools MCP.

**Works for any role** - just update `config.json` with your profile and target keywords.

---

## Setup Guide (10 minutes)

### Step 1: Install Claude Code

1. Download from https://claude.ai/code
2. Install and run it once to create config files
3. Close Claude Code

### Step 2: Install Node.js

Download from https://nodejs.org (LTS version)

Verify installation:
```bash
node --version   # Should show v18+
npm --version    # Should show 9+
```

### Step 3: Configure Chrome MCP Server

This connects Claude Code to a Chrome browser it can control.

**Find your Claude config file:**
- Windows: `C:\Users\YourUsername\.claude.json`
- Mac: `~/.claude.json`

**Open the file and add/merge this configuration:**

```json
{
  "mcpServers": {
    "chrome-devtools": {
      "type": "stdio",
      "command": "npx",
      "args": [
        "chrome-devtools-mcp@latest",
        "--userDataDir=C:\\Users\\YourUsername\\.chrome-debug-linkedin"
      ]
    }
  }
}
```

**IMPORTANT:** Replace `YourUsername` with your actual Windows username.

**Mac/Linux version:**
```json
{
  "mcpServers": {
    "chrome-devtools": {
      "type": "stdio",
      "command": "npx",
      "args": [
        "chrome-devtools-mcp@latest",
        "--userDataDir=/Users/yourusername/.chrome-debug-linkedin"
      ]
    }
  }
}
```

### Step 4: Clone This Repository

```bash
git clone https://github.com/dorddis/linkedin-easy-apply-automation.git
cd linkedin-easy-apply-automation
```

### Step 5: Configure Your Profile

Edit `config.json` with your details:

```json
{
  "profile": {
    "name": "Jane Smith",
    "email": "jane.smith@email.com",
    "phone": "+1-555-123-4567",
    "linkedin": "https://linkedin.com/in/janesmith",
    "portfolio": "https://janesmith.com"
  },
  "job_search": {
    "role": "Financial Analyst",
    "keywords": ["Financial Analyst", "FP&A Analyst", "Finance Analyst"],
    "location": "United States",
    "remote_only": true
  },
  "salary": {
    "current": "75000",
    "expected": "90000",
    "currency": "USD",
    "notice_period": "2 weeks"
  },
  "years_of_experience": "3-5",
  "skills": {
    "Excel": 4,
    "Financial Modeling": 3,
    "SQL": 3,
    "Power BI": 3
  },
  "daily_target": 20
}
```

### Step 6: Add Your Experience Answers

Edit `data/EXPERIENCE_ANSWERS.md` with YOUR actual experience. The automator uses these for text questions like "Describe your experience with..."

Replace all `[CUSTOMIZE]` sections with your real answers.

### Step 7: First-Time LinkedIn Login

1. Open Claude Code in the project folder:
   ```bash
   cd linkedin-easy-apply-automation
   claude
   ```

2. Ask Claude: `Navigate to linkedin.com`

3. A Chrome window opens - log in to LinkedIn manually

4. Your session is now saved (won't need to login again)

### Step 8: Start Applying!

Just type:
```
/apply
```

Or say:
```
Apply to jobs
```

---

## How It Works

```
/apply command
    |
    v
[1. FETCH JOBS]
    - Searches LinkedIn with your keywords
    - Finds 10-15 fresh Easy Apply jobs
    - Uses progressive time filter (1h -> 2h -> 4h -> 24h)
    |
    v
[2. EVALUATE JOBS]
    - Scores each job (freshness, applicant count, company size)
    - Skips staffing agencies and senior roles
    - Ranks by fit score
    |
    v
[3. APPLY SEQUENTIALLY]
    - Opens each job
    - Fills form using config.json
    - Answers questions from EXPERIENCE_ANSWERS.md
    - Submits application
    - Waits 3-5 min (random delay)
    |
    v
[4. TRACK]
    - Logs to tracking.csv
    - Reports progress every 5 applications
```

---

## Commands

| Command | What it does |
|---------|--------------|
| `/apply` | Full workflow: fetch -> evaluate -> apply |
| `Find jobs` | Just fetch and show jobs (no applying) |
| `Apply to [URL]` | Apply to a specific job URL |

---

## File Structure

```
config.json                   # Your profile, skills, salary, keywords
data/EXPERIENCE_ANSWERS.md    # Your answers to common questions
tracking.csv                  # Log of all applications
.claude/agents/               # Automation agents (don't edit)
.claude/commands/apply.md     # The /apply command definition
docs/                         # Additional documentation
```

---

## Safety Guidelines

| Limit | Recommendation |
|-------|----------------|
| Daily applications | 20 (safe), 50 max |
| Delay between apps | 3-5 minutes (automatic) |
| If rate limited | Stop immediately, wait 30-60 min |

**Warning signs to stop:**
- "We limit how often you can apply" message
- HTTP 429 errors
- Account warnings

---

## Customizing for Different Roles

### Change Target Role

1. Edit `config.json`:
   - Update `job_search.role` and `keywords`
   - Update `skills` section with relevant skills
   - Adjust `salary` expectations

2. Edit `data/EXPERIENCE_ANSWERS.md`:
   - Rewrite answers for your new target role
   - Add role-specific Q&A

### Example: Software Engineer

```json
{
  "job_search": {
    "role": "Software Engineer",
    "keywords": ["Software Engineer", "Backend Engineer", "Full Stack Developer"],
    "location": "United States"
  },
  "skills": {
    "Python": 4,
    "JavaScript": 3,
    "React": 3,
    "Node.js": 3,
    "SQL": 3,
    "AWS": 2
  }
}
```

---

## Troubleshooting

### Chrome doesn't open

1. Check `.claude.json` syntax (no trailing commas, correct escaping)
2. Verify the path uses `\\` on Windows
3. Test manually:
   ```bash
   npx chrome-devtools-mcp@latest --help
   ```

### "Not logged in" errors

1. Ask Claude: `Navigate to linkedin.com`
2. Log in manually in the browser
3. Try again

### Forms not filling correctly

1. Check `config.json` has correct field names
2. Update `data/EXPERIENCE_ANSWERS.md` with more specific answers
3. Watch the browser to see what's happening

### Rate limited

1. Stop all automation immediately
2. Wait 30-60 minutes
3. Reduce `daily_target` in config.json
4. Resume with fewer applications

### MCP server errors

1. Clear npm cache: `npm cache clean --force`
2. Reinstall: `npx chrome-devtools-mcp@latest --version`
3. Restart Claude Code

---

## License

MIT - Use at your own risk.

## Disclaimer

This tool automates legitimate job applications. Users are responsible for:
- Following LinkedIn's Terms of Service
- Providing accurate information in applications
- Not exceeding reasonable application volumes
- Reviewing applications before they're submitted
