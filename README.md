# LinkedIn Easy Apply Automation

Automate LinkedIn Easy Apply job applications using Claude Code + Chrome DevTools MCP.

## Quick Start (5 minutes)

### 1. Prerequisites
- [Claude Code](https://claude.ai/code) installed
- Node.js installed (for npx)
- Chrome browser

### 2. Clone & Configure

```bash
git clone https://github.com/yourusername/linkedin-easy-apply-automation.git
cd linkedin-easy-apply-automation
```

Edit `config.json` with your details:
```json
{
  "profile": {
    "name": "Your Name",
    "email": "your.email@example.com",
    "phone": "+91-9876543210"
  },
  "job_search": {
    "role": "Financial Analyst",
    "keywords": ["Financial Analyst", "Finance Analyst"],
    "location": "India"
  }
}
```

### 3. Setup Chrome MCP

Add to your Claude Code settings (`~/.claude.json`):

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

### 4. First Run

1. Open Claude Code in this directory
2. Say: "Navigate to LinkedIn and log in"
3. Log in manually (session persists)
4. Say: "Apply to jobs"

## How It Works

```
You: "Apply to jobs"
  |
  v
[job-fetcher agent]
  - Opens LinkedIn
  - Finds 5-7 fresh Easy Apply jobs
  - Returns prioritized list
  |
  v
[easy-apply-automator agent]
  - For each job:
    - Fills contact info from config.json
    - Fills skills/experience from config.json
    - Answers text questions from EXPERIENCE_ANSWERS.md
    - Submits application
    - Waits 3-5 minutes (anti-detection)
  |
  v
[tracking.csv]
  - Logs all applications
```

## File Structure

```
config.json                 # Your profile, skills, salary
data/EXPERIENCE_ANSWERS.md  # Answers to common questions
tracking.csv                # Application log
.claude/agents/             # Automation agents
docs/                       # Setup guides
```

## Safety

- **Daily limit:** 20 applications recommended (50 max)
- **Delays:** 3-5 min between applications (automatic)
- **Stop if:** You see rate limit messages

## Customization

### For Different Roles

1. Edit `config.json`:
   - Change `job_search.role` and `keywords`
   - Update `skills` section
   - Modify `linkedin_search_url`

2. Edit `data/EXPERIENCE_ANSWERS.md`:
   - Add role-specific Q&A

### For Different Countries

1. Edit `config.json`:
   - Change `job_search.location`
   - Update `linkedin_search_url` location parameter

## Commands

| Command | What it does |
|---------|--------------|
| "Find jobs" | Fetches 5-7 fresh Easy Apply jobs |
| "Apply to jobs" | Fetches + applies sequentially |
| "Apply to [URL]" | Applies to specific job |

## Troubleshooting

| Issue | Solution |
|-------|----------|
| Chrome doesn't open | Check MCP config in ~/.claude.json |
| Not logged in | Navigate to LinkedIn, log in manually |
| Rate limited | Stop, wait 30 min, reduce volume |
| Wrong answers | Update EXPERIENCE_ANSWERS.md |

## License

MIT - Use at your own risk. LinkedIn may limit accounts for automation.

## Disclaimer

This tool automates legitimate job applications. Users are responsible for:
- Following LinkedIn's Terms of Service
- Providing accurate information
- Not exceeding reasonable application volumes
