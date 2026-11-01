# Search Quality Rater Skill for Claude Code

A Claude Code skill that evaluates a webpage against Google's Search Quality Evaluator Guidelines (September 2025 edition). It returns a structured report with E-E-A-T levels, a Page Quality rating, a Needs Met rating and the top three fixes, and it requires independent reputation research before it scores trust.

## Quickstart

```bash
claude plugin marketplace add devkindhq/search-quality-rater-skill
claude plugin install search-quality-rater@search-quality-rater
```

Then ask Claude Code in plain English:

```text
Evaluate https://example.com/some-page against the quality rater guidelines.
The query that brings users here is "your target query".
```

You can pass a URL (fetched with WebFetch), a local file, or pasted page content. The query is optional. Without one, the Needs Met rating is reported as N/A.

## What the report contains

Every report uses the same structure, so results are comparable across pages:

| Section | What you get |
|---|---|
| Page type and YMYL | Page classification and whether the topic is Your Money or Your Life |
| E-E-A-T assessment | Experience, Expertise, Authoritativeness and Trustworthiness, each scored None to Very Strong with one line of evidence |
| Page Quality (PQ) | A rating from Lowest to Highest, with strengths, concerns and the deciding factors |
| Needs Met (NM) | A rating from FailsM to FullyM, plus the dominant query intent (needs a query) |
| Composite score | E-E-A-T, PQ and NM on a shared 0 to 10 scale |
| Top 3 improvements | The changes most likely to move the page up a tier |

For batch audits, an orchestrating agent can also request a short JSON return (URL, PQ rating, E-E-A-T level, NM rating, top issues).

## Why this one

Most E-E-A-T audits are surface-level checklists. This skill follows the actual rater process, including the parts most tools skip:

- **Independent reputation research.** It searches for news, reviews, complaints and awards. A site's own About page is never accepted as reputation evidence. If nothing independent exists, reputation is marked unverified, not positive.
- **Trust as the anchor.** A Trust failure caps the overall rating however strong Experience, Expertise or Authoritativeness look. Conflicts of interest such as affiliate links are flagged and weighed.
- **September 2025 guidelines throughout.** This includes the 2024 and 2025 spam patterns: expired domain abuse, site reputation abuse and scaled content abuse.
- **Needs Met and special content types.** Forums and user-generated content, news, government and health pages, and AI-generated content guidance are covered, not only Page Quality.
- **Read-only.** It needs no MCP servers and runs no commands. Multiple agents can rate different URLs in parallel.

## Example

A sample run (one page, not a benchmark) on `https://www.niddk.nih.gov/health-information/diabetes/overview/what-is-diabetes` with the query "what is diabetes":

- **YMYL:** Yes (Health/Safety). **PQ:** High. **NM:** HM. **E-E-A-T:** High.
- **Trust:** accurate, named physician reviewers, review date shown, no ads.
- **Reputation:** two searches found no independent evaluation, so it is marked unverified.
- **Held at High:** overview scope, 2019 statistics, last reviewed April 2023.

## Network access and data

Given a live URL, the skill uses WebFetch to retrieve the page. It also runs web searches on the site and author names for the required reputation research. The URL and those names are sent to Claude's web tools and search. The skill has no scripts, makes no other network calls, and does not reference credentials or environment variables.

## What is inside

```text
skills/search-quality-rater/
  SKILL.md           the skill
  references/        E-E-A-T, Page Quality, Needs Met, special content frameworks
  evals/evals.json   evaluation prompts
```

## Limits

This is an unofficial aid, not Google's rating process. Google's raters score pages for evaluating search systems. Their ratings do not directly set rankings, and this skill's output does not predict them. Use the report as a structured content review, and check the reasoning before you act on it.

## Contributing

Issues and pull requests are welcome. See [CONTRIBUTING.md](CONTRIBUTING.md). To report a security problem, see [SECURITY.md](SECURITY.md).

## License

MIT, see [LICENSE](LICENSE). Built by Devkind.
