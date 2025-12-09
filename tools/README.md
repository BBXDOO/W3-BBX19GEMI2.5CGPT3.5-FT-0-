# File Integrity Check Tools

This directory contains tools for checking file integrity in the W3_HB_team_BXCGICOG repository.

## Tools

### 1. file_integrity_check.py

A comprehensive file integrity checker that validates:
- Missing directories referenced in module.json files
- Missing files
- Corrupted JSON files
- Suspicious empty files (excluding .gitkeep)
- Broken symbolic links

**Usage:**
```bash
python3 tools/file_integrity_check.py
```

**Output:**
- Console output with detailed report
- Report file saved to `tools/file_integrity_report.txt`

**Exit Code:**
- `0` - No issues found
- `1` - Issues detected

### 2. send_integrity_report.py

Email notification script that sends the file integrity report via email.

**Usage:**
```bash
# Set environment variables
export SENDER_EMAIL="your-email@example.com"
export RECIPIENT_EMAIL="recipient@example.com"
export SENDER_PASSWORD="your-app-password"

# Optional: Configure SMTP server (defaults to Gmail)
export SMTP_SERVER="smtp.gmail.com"
export SMTP_PORT="587"
export USE_TLS="true"

# Run the script
python3 tools/send_integrity_report.py
```

**Features:**
- Runs integrity check automatically
- Generates both plain text and HTML formatted reports
- Sends email with comprehensive issue details
- Falls back to file output if email configuration is incomplete

**Email Configuration:**

For Gmail:
1. Enable 2-factor authentication on your Google account
2. Generate an app password at https://myaccount.google.com/apppasswords
3. Use the app password as SENDER_PASSWORD

For other SMTP servers, adjust SMTP_SERVER and SMTP_PORT accordingly.

## Report Format

The report includes:

### 📁 Missing Directories
Directories that should exist based on module.json configuration but are not present.

### 📄 Missing Files
Files that are referenced but cannot be found.

### ⚠️ Corrupted JSON Files
JSON files that fail to parse or have syntax errors.

### 🔍 Suspicious Empty Files
Files that are empty (excluding .gitkeep files which are intentionally empty).

### 🔗 Broken Symbolic Links
Symbolic links that point to non-existent targets.

## Current Status

As of the last check, the following issues were detected:

**Missing Directories (6):**
- modules/ChatGPT/flows/
- modules/ChatGPT/requests/
- modules/ChatGPT/scenarios/
- modules/Gemini/reports/
- modules/Gemini/requests/
- workflows/orchestration/

These directories are referenced in the module.json files but do not currently exist in the repository.

## Integration

These tools can be integrated into:
- CI/CD pipelines for automated checks
- Pre-commit hooks
- Scheduled cron jobs for periodic monitoring
- GitHub Actions workflows

## Example GitHub Actions Workflow

```yaml
name: File Integrity Check

on:
  schedule:
    - cron: '0 0 * * *'  # Daily at midnight
  workflow_dispatch:

jobs:
  check-integrity:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v2
      - name: Run integrity check
        run: python3 tools/file_integrity_check.py
      - name: Send email report
        env:
          SENDER_EMAIL: ${{ secrets.SENDER_EMAIL }}
          SENDER_PASSWORD: ${{ secrets.SENDER_PASSWORD }}
          RECIPIENT_EMAIL: ${{ secrets.RECIPIENT_EMAIL }}
        run: python3 tools/send_integrity_report.py
```

## Notes

- The checker validates against the structure defined in module.json files
- Wildcard paths (containing '*') are intentionally skipped
- .gitkeep files are excluded from empty file checks as they are intentionally empty
- The .git directory is excluded from all checks
