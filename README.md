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

# 2. Run the interactive setup wizard
eng-team-reporter --setup

# 3. Generate your first report
eng-team-reporter --period week

# 4. Open the report
open ~/eng-team-reporter/weekly_report_*.html
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

### Ship/Show/Ask Analysis

- **PR Process Classification** — classifies every PR as Ship, Show, or Ask using [Martin Fowler's framework](https://martinfowler.com/articles/ship-show-ask.html)
- **Streamlining Suggestions** — identifies PRs that could use a lighter review process based on change size, author seniority, and PR patterns
- **Per-Author & Per-Repo Breakdowns** — see each contributor's and repository's Ship/Show/Ask distribution
- **Draft PR Recognition** — draft PRs are treated as healthy "Ask" signals seeking early feedback
- **Optional Seniority Context** — provide team seniority levels to refine suggestions

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
# Setup and configuration
eng-team-reporter --setup                                    # Interactive setup wizard
eng-team-reporter --activate /path/to/license.json           # Activate license

# Report generation
eng-team-reporter --period week                              # Last complete week
eng-team-reporter --period week --week-number 2025-08        # Specific week
eng-team-reporter --period month                             # Last complete month
eng-team-reporter --period month --month 2025-01             # Specific month
eng-team-reporter --period custom --start-date 2025-01-01 --end-date 2025-01-31

# Output options (default: ~/eng-team-reporter)
eng-team-reporter --period week --output-dir ./my_reports    # Custom output directory
eng-team-reporter --period week --verbose                    # Verbose logging

# Ship/Show/Ask PR process analysis
eng-team-reporter --period month --ship-show-ask             # Analyze last month's PRs
eng-team-reporter --period custom --start-date 2025-01-01 --end-date 2025-03-31 \
  --ship-show-ask --seniority-map "dev1:junior,dev2:mid,dev3:senior"

# Confluence publishing
eng-team-reporter --period week --publish                    # Publish to default space
eng-team-reporter --period week --publish --publish-space "TEAM"

# Info
eng-team-reporter --version                                  # Show version
eng-team-reporter --help                                     # Show all options
```

## Configuration

Run the interactive setup wizard for guided configuration with connection testing:

```bash
eng-team-reporter --setup
```

The wizard saves credentials to `~/.etr/config` with restrictive permissions (0600). You can also create a `.env` file in your working directory. Config precedence: **environment variables > `~/.etr/config` > `.env` in CWD**.

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

Reports are saved to `~/eng-team-reporter/` by default (override with `--output-dir`):

```
~/eng-team-reporter/
├── weekly_report_2025-W08.html           # Main team report
├── weekly_report_2025-W08/               # Detail reports
│   ├── people/
│   │   ├── John_Doe.html
│   │   └── Jane_Smith.html
│   └── repositories/
│       ├── org_repo1.html
│       └── org_repo2.html
├── monthly_report_2025-01.html
├── custom_report_20250101_20250131.html
└── ship_show_ask_20250101_20250331.html  # --ship-show-ask
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

### Ship/Show/Ask Analysis Report (`--ship-show-ask`)
- Framework explanation with Ship/Show/Ask definitions
- Overview stats: classification distribution, merge times, streamlining opportunities
- Current vs. suggested distribution bar charts
- Per-author breakdown: classification mix, draft usage, reviews given, avg merge time
- Per-repository breakdown with distribution bars
- Streamlining opportunities table: PRs that could use a lighter process
- Draft PR analysis mapping to the Ask pattern
- Actionable recommendations based on team patterns
- Full PR list with classification reasoning

## Automation

Since config is stored in `~/.etr/config`, no `cd` to a working directory is needed:

```bash
# Weekly report every Monday at 9am
0 9 * * 1 /usr/local/bin/eng-team-reporter --period week

# Monthly report on the 1st of each month
0 9 1 * * /usr/local/bin/eng-team-reporter --period month

# Weekly report with Confluence publishing
0 9 * * 1 /usr/local/bin/eng-team-reporter --period week --publish
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

| License Type | Users | Duration |
|-------------|-------|----------|
| **Trial** | 1 | 30 days |
| **Standard** | 1 | 12 months |
| **Enterprise** | 10+ | 12 months |

```bash
eng-team-reporter --activate /path/to/license.json
# License is stored at ~/.etr/license.json
```

Contact info@zagware.com for purchasing or renewal. Visit [www.zagware.io](https://www.zagware.io) for more information.

## Troubleshooting

| Issue | Solution |
|-------|----------|
| Authentication errors | Check API tokens are valid and have correct scopes |
| Missing data | Verify project keys, repo names, and date ranges |
| Confluence publishing fails | Ensure API token has write access to target space |
| Rate limiting | GitHub allows 5000 req/hr; run reports during off-peak hours |
| JIRA refs not detected | Keys must be uppercase `PROJECT-123` format in PR title or body |
| License expired | Contact info@zagware.com for renewal |

## License

Copyright (c) 2026 Zagware. All rights reserved. For licensing inquiries, contact info@zagware.com.
