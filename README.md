# Engineering Team Reporter

Generate comprehensive engineering team activity reports from JIRA, GitHub, and Confluence. Beautiful HTML reports with automatic JIRA-GitHub linking, per-person analytics, code area breakdowns, and optional Confluence publishing.

## Installation

### Homebrew (macOS — Recommended)

```bash
brew tap zagware/tap
brew install eng-team-reporter

# Upgrade later
brew upgrade eng-team-reporter
```

### Manual Download

Download the latest binary for your platform from [Releases](https://github.com/zagware/eng-team-reporter-dist/releases):

| Platform | File |
|----------|------|
| macOS (Apple Silicon) | `eng-team-reporter-*-macos-arm64.tar.gz` |
| macOS (Intel) | `eng-team-reporter-*-macos-x64.tar.gz` |
| macOS (Apple Silicon) installer | `eng-team-reporter-*-macos-arm64.pkg` |
| macOS (Intel) installer | `eng-team-reporter-*-macos-x64.pkg` |
| Linux (x64) | `eng-team-reporter-*-linux-x64.tar.gz` |
| Windows (x64) | `eng-team-reporter-*-windows-x64.zip` |

No Python or other dependencies required — the binary is fully self-contained.

## Quick Start

```bash
# 1. Activate your license
eng-team-reporter --activate /path/to/license.json

# 2. Create a .env file with your API credentials (see Configuration below)

# 3. Generate your first report
eng-team-reporter --period week

# 4. Open the report
open reports/weekly_report_*.html
```

## Features

### Core Integrations

| Platform | What's Tracked |
|----------|---------------|
| **JIRA** | Issues created and closed, categorized by type and priority |
| **GitHub** | Pull requests, commits, code reviews, lines changed |
| **Confluence** | Pages created and updated by team members |

Automatic cross-platform linking extracts JIRA issue keys from GitHub PR titles and descriptions.

### Report Types

- **Weekly reports** — last complete week or a specific week number
- **Monthly reports** — last complete month or a specific month
- **Custom date ranges** — any start/end date pair
- **Individual detail reports** — per-person breakdown with all activity
- **Repository detail reports** — per-repo analysis with code area classification (Frontend, Backend, Testing, DevOps, Documentation)

### Smart Features

- **JIRA Reference Detection** — automatically extracts JIRA issue keys from PR titles and descriptions
- **Fuzzy JIRA Matching** — correlate PRs with JIRA issues via branch name patterns or description similarity
- **Issue Summary Display** — fetches and displays JIRA issue titles alongside GitHub PRs
- **Missing Reference Alerts** — visually highlights PRs that don't reference JIRA issues
- **Code Area Classification** — categorizes file changes into Frontend, Backend, Testing, DevOps, etc.
- **Confluence Publishing** — publish reports directly to Confluence with linked sub-pages per engineer and repository

### Performance

- **Parallel data collection** — JIRA, GitHub, and Confluence data collected concurrently
- **Fast CLI startup** — `--help`, `--version`, and `--setup` return instantly
- **Timing output** — elapsed time displayed for each phase and total runtime

## Command Reference

```bash
# Report generation
eng-team-reporter --period week                              # Last complete week
eng-team-reporter --period week --week-number 2025-08        # Specific week
eng-team-reporter --period month                             # Last complete month
eng-team-reporter --period month --month 2025-01             # Specific month
eng-team-reporter --period custom --start-date 2025-01-01 --end-date 2025-01-31

# Output options
eng-team-reporter --period week --output-dir ./my_reports    # Custom output directory
eng-team-reporter --period week --verbose                    # Verbose logging

# Confluence publishing
eng-team-reporter --period week --publish                    # Publish to default space
eng-team-reporter --period week --publish --publish-space "TEAM"

# License management
eng-team-reporter --activate /path/to/license.json           # Activate license

# Info
eng-team-reporter --version                                  # Show version
eng-team-reporter --help                                     # Show all options
```

## Configuration

Create a `.env` file in the directory where you run the tool.

### Required

| Variable | Description | Example |
|----------|-------------|---------|
| `JIRA_URL` | JIRA instance URL | `https://yourcompany.atlassian.net` |
| `JIRA_EMAIL` | JIRA account email | `you@company.com` |
| `JIRA_API_TOKEN` | [JIRA API token](https://id.atlassian.com/manage-profile/security/api-tokens) | `abc123...` |
| `JIRA_PROJECT_KEYS` | Comma-separated project keys | `PROJ1,PROJ2` |
| `GITHUB_TOKEN` | [GitHub PAT](https://github.com/settings/tokens) (scope: `repo`) | `ghp_abc123...` |
| `GITHUB_REPOS` | Comma-separated repos | `org/repo1,org/repo2` |

### Optional

| Variable | Description | Example |
|----------|-------------|---------|
| `CONFLUENCE_URL` | Confluence URL (defaults to JIRA_URL) | `https://yourcompany.atlassian.net/wiki` |
| `CONFLUENCE_SPACES` | Spaces to track (leave empty for all) | `ENG,TEAM` |
| `CONFLUENCE_PUBLISH_SPACE` | Space for publishing reports | `~accountid` |
| `JIRA_TEAM_MEMBERS` | JIRA users to filter (emails) | `john@co.com,jane@co.com` |
| `GITHUB_TEAM_MEMBERS` | GitHub usernames to filter | `johndoe,janesmith` |
| `USER_MAPPING` | Explicit JIRA-to-GitHub mapping | `john@co.com:johndoe,jane@co.com:janesmith` |
| `FUZZY_JIRA_MATCHING` | Fuzzy matching mode | `off`, `branch`, `description`, `all` |
| `FUZZY_JIRA_THRESHOLD` | Similarity threshold (0.0-1.0) | `0.3` (default) |

## Output Structure

```
reports/
├── weekly_report_2025-W08.html           # Main team report
├── weekly_report_2025-W08/               # Detail reports
│   ├── people/
│   │   ├── John_Doe.html
│   │   └── Jane_Smith.html
│   └── repositories/
│       ├── org_repo1.html
│       └── org_repo2.html
├── monthly_report_2025-01.html
└── custom_report_20250101_20250131.html
```

## Report Contents

### Main Team Report
- Summary cards: JIRA issues, GitHub PRs/commits/reviews, Confluence pages
- JIRA metrics by type and priority
- GitHub PRs with JIRA reference badges (blue = linked, yellow = missing)
- Per-person summary cards with click-through detail links

### Individual Detail Reports
- Complete breakdown of all JIRA issues, PRs, commits, reviews, and Confluence pages
- JIRA issue titles displayed alongside referenced PRs
- Warning badges for PRs without JIRA references

### Repository Detail Reports
- Code area breakdown: Frontend, Backend, Testing, DevOps, Documentation
- Lines changed and files modified per area
- PRs grouped by code area

## Automation

```bash
# Weekly report every Monday at 9am
0 9 * * 1 cd /path/to/workdir && eng-team-reporter --period week

# Monthly report on the 1st of each month
0 9 1 * * cd /path/to/workdir && eng-team-reporter --period month

# Weekly report with Confluence publishing
0 9 * * 1 cd /path/to/workdir && eng-team-reporter --period week --publish
```

## Best Practices

### JIRA Reference Tracking

Include JIRA keys in PR titles for full traceability:
- `COD-123: Add user authentication`
- `[COD-456] Fix login bug`
- `Update docs (COD-789)`

PRs without JIRA references are flagged in reports.

### Fuzzy JIRA Matching

If your team doesn't always include JIRA keys in PR titles:

```bash
FUZZY_JIRA_MATCHING=branch       # Match keys in branch names (e.g. feature/cod-123-add-login)
FUZZY_JIRA_MATCHING=description  # Match PR descriptions against JIRA summaries
FUZZY_JIRA_MATCHING=all          # Try branch first, then description similarity
FUZZY_JIRA_THRESHOLD=0.4        # Adjust similarity threshold (default 0.3)
```

Fuzzy matches appear with amber dashed badges and a `~` suffix to distinguish them from exact matches.

### Team Member Mapping

**Explicit mapping** (recommended):
```bash
USER_MAPPING=john.doe@company.com:johndoe,jane.smith@company.com:janesmith
```

**Position-based** (order must match):
```bash
JIRA_TEAM_MEMBERS=john.doe@company.com,jane.smith@company.com
GITHUB_TEAM_MEMBERS=johndoe,janesmith
```

## Licensing

Engineering Team Reporter requires a valid license.

| License Type | Team Size | Duration |
|-------------|-----------|----------|
| **Trial** | Up to 10 | 30 days |
| **Standard** | Up to 25 | 1 year |
| **Enterprise** | Unlimited | 1 year |

```bash
eng-team-reporter --activate /path/to/license.json
# License is stored at ~/.eng-team-reporter/license.json
```

## Troubleshooting

| Issue | Solution |
|-------|----------|
| Authentication errors | Check API tokens are valid and have correct scopes |
| Missing data | Verify project keys, repo names, and date ranges |
| Confluence publishing fails | Ensure API token has write access to target space |
| Rate limiting | GitHub allows 5000 req/hr; run reports during off-peak hours |
| JIRA refs not detected | Keys must be uppercase `PROJECT-123` format in PR title or body |
| License expired | Contact vendor for renewal |

## License

Copyright (c) 2026 Davy McAleer. All rights reserved.
