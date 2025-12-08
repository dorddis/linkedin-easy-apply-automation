# LinkedIn Anti-Detection & Rate Limits

Safety guidelines to avoid account restrictions when automating LinkedIn applications.

## TL;DR - Safe Daily Limits

| Action | Safe Limit | Our Target |
|--------|-----------|------------|
| Easy Apply | 50-100/day | 20/day |
| Connection Requests | 15-25/day | 0 (not automated) |
| Messages | 50-100/day | 0 (not automated) |

**Golden Rule:** Sequential applications with 3-5 min random delays.

## What Triggers Detection

### 1. Robotic Timing
- Same time between actions
- Actions bunched together
- No variation

**Solution:** Random delays (3-5 min) between applications

### 2. High Volume Bursts
- 50 applications in 30 minutes
- All activity in one hour

**Solution:** Spread across the day, limit to 20/day

### 3. Suspicious Patterns
- Parallel applications (multiple tabs)
- Perfect mouse movements
- Instant form filling

**Solution:** Sequential only, natural delays

## Warning Signs - STOP IMMEDIATELY

If you see any of these:
- "We limit the number of submissions"
- HTTP 429 error
- "Unusual activity detected"
- Temporary restrictions on your account

**Action:**
1. Stop all automation immediately
2. Wait 30-60 minutes minimum
3. Resume at 50% volume
4. If persists, wait 24 hours

## Safe Automation Strategy

### Week 1 (Warming Up)
- 10-15 applications/day
- Morning + afternoon sessions
- Manual review of each application

### Week 2+
- 20 applications/day
- 3-5 min delays between apps
- Monitor for warnings

### Never Exceed
- 50 applications/day absolute max
- Sequential only (never parallel)
- Always with random delays

## Timing Strategy

### Good Pattern
```
9:00 AM  - App 1
9:04 AM  - App 2
9:08 AM  - App 3
(break until afternoon)
2:00 PM  - App 4
2:05 PM  - App 5
...
```

### Bad Pattern
```
9:00:00 AM - App 1
9:00:30 AM - App 2
9:01:00 AM - App 3
9:01:30 AM - App 4
...
```

## Built-In Safety

This automation system includes:
- 3-5 minute random delays between applications
- Sequential processing only
- Daily target of 20 (well under limits)

## If Rate Limited

1. **Immediate:** Stop all automation
2. **Wait:** Minimum 30-60 minutes
3. **Review:** Check tracking.csv for patterns
4. **Resume:** Lower volume (10/day for a week)
5. **Monitor:** Watch for more warnings

## If Account Restricted

1. **DO NOT** create a new account
2. **DO NOT** try to bypass (VPN, new device)
3. Contact LinkedIn support
4. Wait for restriction to lift (7-14 days)
5. Resume with Phase 1 (10/day) when back

## Best Practices

1. **Quality over quantity** - Better 15 good apps than 50 rushed
2. **Monitor your account** - Check for warnings daily
3. **Take breaks** - Don't run 7 days straight
4. **Review applications** - Spot check they're going through
5. **Update answers** - Generic answers get flagged
