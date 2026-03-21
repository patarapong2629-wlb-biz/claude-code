---
name: summarize-testing-ai-news
description: Fetches and summarizes the latest software testing and AI-in-testing news from the past 7 days. Use when the user asks for recent news, updates, or trends in software testing, test automation, or AI applied to QA and testing.
---

# Software Testing & AI News Summarizer

## OUTPUT FORMAT — READ THIS FIRST

Every category section MUST use a two-column markdown pipe table: **Summary** and **Date**.

**WRONG — never do this:**

```
Summary: https://news.google.com/rss/articles/CBMi...
Date: Mar 19, 2026
────────────────────
```

**RIGHT — always do this:**

```
| Summary | Date |
|---|---|
| SmartBear ships BearQ, an AI-powered autonomous test platform targeting application integrity. | Mar 19, 2026 |
| Tricentis launches agentic quality engineering platform for AI-driven development teams. | Mar 13, 2026 |
```

Rules:

- Two columns only: **Summary** and **Date** — no URLs, no links, no Title column
- Summary: one sentence, max 20 words, plain English describing what happened
- Rows sorted by date descending (newest first)
- Date: `MMM DD, YYYY`
- Category with no items: write `No items in this period.`

---

## Workflow

```
Progress:
- [ ] Step 1: Calculate date range (today minus 7 days)
- [ ] Step 2: Fetch all sources
- [ ] Step 3: Categorize and curate findings
- [ ] Step 4: Generate structured summary report
```

### Step 1: Determine date range

Note today's date and compute the cutoff (today - 7 days). Skip any article older than the cutoff. If a date cannot be determined, include the article and flag as "date unknown".

### Step 2: Fetch all sources

Fetch each URL and extract article titles, dates, and descriptions.

| Source                         | URL                                                                                  |
| ------------------------------ | ------------------------------------------------------------------------------------ |
| Google News – Software Testing | `https://news.google.com/rss/search?q=software+testing&hl=en-US&gl=US&ceid=US:en`    |
| Google News – Test Automation  | `https://news.google.com/rss/search?q=test+automation&hl=en-US&gl=US&ceid=US:en`     |
| Google News – AI in Testing    | `https://news.google.com/rss/search?q=AI+software+testing&hl=en-US&gl=US&ceid=US:en` |
| DEV.to – Testing               | `https://dev.to/feed/tag/testing`                                                    |
| Applitools Blog                | `https://applitools.com/blog/feed/`                                                  |
| BrowserStack Blog              | `https://www.browserstack.com/blog/feed/?category=automated-testing`                 |
| Mabl Blog                      | `https://www.mabl.com/blog/rss.xml`                                                  |
| Playwright Releases            | `https://github.com/microsoft/playwright/releases.atom`                              |
| Cypress Blog                   | `https://www.cypress.io/blog/rss.xml`                                                |
| ACM TOSEM                      | `https://dl.acm.org/action/showFeed?type=etoc&feed=rss&jc=tosem`                     |

### Step 3: Categorize and curate findings

Group into categories:

- **AI & LLM in Testing** – AI-generated tests, LLM-based QA tools, autonomous testing agents
- **Test Automation** – frameworks, CI/CD, Playwright, Cypress, Selenium
- **Performance & Load Testing** – k6, JMeter, Gatling, benchmarks
- **Shift-Left & DevOps** – testing in pipelines, TDD/BDD, developer testing
- **Visual & Accessibility Testing** – visual regression, a11y tools
- **Tools & Releases** – new versions, open-source releases
- **Industry News & Events** – surveys, company news, analyst reports

Deduplicate: merge same story from multiple feeds into one entry.

**Curation limits — keep only the best, discard the rest:**

| Category       | Max items |
| -------------- | --------- |
| All categories | 5         |

Priority order (highest first):

1. Breaking news / major product launches
2. Significant tool or framework releases
3. Practical techniques with broad applicability
4. Industry data, surveys, analyst findings
5. Good-to-know context (opinion, case studies)

Discard: generic opinion pieces with no new insight, duplicate angles, substance-free press releases.

### Step 4: Generate summary report

Use the table format defined at the top of this skill. Full template:

```
# Software Testing & AI News — [START_DATE] to [END_DATE]

## Highlights
- [Key point 1]
- [Key point 2]
- [Key point 3]
- [Key point ...]

## AI & LLM in Testing
| Summary | Date |
|---|---|
| One-sentence summary. | Mar DD, YYYY |

## Test Automation
| Summary | Date |
|---|---|
| One-sentence summary. | Mar DD, YYYY |

## Performance & Load Testing
| Summary | Date |
|---|---|
| One-sentence summary. | Mar DD, YYYY |

## Shift-Left & DevOps
| Summary | Date |
|---|---|
| One-sentence summary. | Mar DD, YYYY |

## Visual & Accessibility Testing
| Summary | Date |
|---|---|
| One-sentence summary. | Mar DD, YYYY |

## Tools & Releases
| Summary | Date |
|---|---|
| One-sentence summary. | Mar DD, YYYY |

## Industry News & Events
| Summary | Date |
|---|---|
| One-sentence summary. | Mar DD, YYYY |

---
Sources fetched: [count] | Articles included: [count] | Period: last 7 days
```

## Notes

- If a feed is unreachable, skip it and note it in the footer.
