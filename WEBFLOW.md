# Web flow

How visitors move through **dodibanzon.github.io**: every page, every link, and what happens behind each click. The whole site is one file, `index.html`. Pages are `<template>` elements that the router swaps into `#page`, so moving between pages never reloads the browser.

The diagrams are Mermaid, so GitHub draws them right in this file. In the diagrams, **solid arrows** stay on the site and **dotted arrows** leave it.

## 1. Sitemap

```mermaid
flowchart TD
  site(["dodibanzon.github.io"])
  site --> home["Home<br>/"]
  site --> exp["Experience<br>/#experience"]
  site --> proj["Projects<br>/#projects"]
  site --> dash["Dashboard<br>/#dashboard"]
  site --> about["About<br>/#about"]
  site --> contact["Contact<br>/#contact"]

  home --> h1["Hero<br>headline · 2 CTA buttons · stats row"]
  home --> h2["Bento<br>Currently · Toolkit"]
  home --> h3["Writing<br>Substack callout"]

  exp --> e1["3 role cards<br>PIDS · Epimetrics · Gridware"]

  proj --> p1["5 project cards<br>HEFP · eSOA · Health Inequity<br>Python training · Grid alert triage"]

  dash --> d1["Key-figure tiles"]
  dash --> d2["Role timeline"]
  dash --> d3["Toolkit skill bars"]
  dash --> d4["In progress · Local time"]

  about --> a1["Bio · Stack tags"]
  about --> a2["Profile card · Now card"]

  contact --> c1["4 contact cards<br>GitHub · Email · Substack · LinkedIn"]
  contact --> c2["Send a message form"]
```

| Page | URL | Template | What it's for |
|---|---|---|---|
| Home | `/` | `tpl-home` | Who Dodi is, current work, and where to go next |
| Experience | `/#experience` | `tpl-experience` | Professional roles, newest first |
| Projects | `/#projects` | `tpl-projects` | The concrete work behind the roles |
| Dashboard | `/#dashboard` | `tpl-dashboard` | Career data charted from the Experience and Projects cards |
| About | `/#about` | `tpl-about` | Background, stack, profile and current focus |
| Contact | `/#contact` | `tpl-contact` | Contact channels and a message form |

## 2. Navigation map

The top nav and the footer appear on every page, so any page is one click from any other.

```mermaid
flowchart LR
  subgraph chrome["On every page"]
    nav["Top nav<br>Brand · Home · Experience · Projects<br>Dashboard · About · Contact"]
    foot["Footer<br>Substack · GitHub · Contact · About"]
  end

  subgraph pages["Pages"]
    home["Home"]
    exp["Experience"]
    proj["Projects"]
    dash["Dashboard"]
    about["About"]
    contact["Contact"]
  end

  subgraph off["Off-site"]
    substack["Substack<br>@dodibanzon"]
    github["GitHub<br>@dodibanzon"]
    linkedin["LinkedIn<br>in/dodi-banzon"]
    orgs["Employer sites<br>PIDS · Epimetrics · Gridware"]
    mail["Email app<br>mailto:"]
    w3f["Web3Forms API"]
  end

  nav --> pages
  foot --> about
  foot --> contact
  foot -.-> substack
  foot -.-> github

  home -- "View Experience" --> exp
  home -- "Get in touch" --> contact
  home -. "Read on Substack" .-> substack
  dash -- "Source note" --> exp
  dash -- "Source note" --> proj
  exp -. "org ›" .-> orgs
  proj -. "org ›" .-> orgs
  about -. "inline links" .-> github
  about -. "inline links" .-> substack
  contact -. "cards" .-> github
  contact -. "cards" .-> substack
  contact -. "cards" .-> linkedin
  contact -. "Email card" .-> mail
  contact -. "form POST" .-> w3f
```

### Link inventory

Internal links (`data-page`) are handled by the router. Off-site links open in a new tab (`target="_blank" rel="noopener"`). The exceptions are the Email card, which opens the visitor's mail app, and the form, which sends in the background.

| Where | Label | Goes to | Type |
|---|---|---|---|
| Nav | **Dodi Banzon** (brand) | Home | Internal |
| Nav | Home · Experience · Projects · Dashboard · About · Contact | That page | Internal |
| Footer | Contact · About | That page | Internal |
| Footer | Substack | substack.com/@dodibanzon | Off-site |
| Footer | GitHub | github.com/dodibanzon | Off-site |
| Home hero | View Experience | Experience | Internal |
| Home hero | Get in touch | Contact | Internal |
| Home writing card | Read on Substack | substack.com/@dodibanzon | Off-site |
| Experience cards | PIDS › · Epimetrics › · Gridware › | pids.gov.ph · epimetrics.com.ph · gridware.com.au | Off-site |
| Projects cards | PIDS › ×2 · Epimetrics › ×2 · Gridware › | Same employer sites | Off-site |
| Dashboard source note | Experience · Projects | That page | Internal |
| About bio | GitHub · Substack | github.com/dodibanzon · substack.com/@dodibanzon | Off-site |
| Contact cards | GitHub · Substack · LinkedIn | github.com/dodibanzon · substack.com/@dodibanzon · linkedin.com/in/dodi-banzon | Off-site |
| Contact cards | Email | `mailto:` the site's email address | Mail app |
| Contact form | Send | api.web3forms.com/submit | Background POST |

## 3. Visitor journeys

The paths the site is built for. A click count is the number of clicks from landing on Home.

| Visitor | Goal | Path | Clicks |
|---|---|---|---|
| Recruiter | Check experience, then reach out | Home → **View Experience** → nav **Contact** → Send | 2 + send |
| Hiring manager | See the evidence | Home → nav **Projects** → nav **Dashboard** → hover the charts or open **Data table** | 2 |
| Reader | Read the writing | Home → **Read on Substack** (new tab) | 1 |
| Collaborator | Find the code | Home → footer **GitHub**, or About → **GitHub** | 1 |
| Anyone | Get in touch fast | Any page → nav or footer **Contact** | 1 |
| Shared link | Open a page directly | `/#projects` etc. opens that page on load | 0 |

```mermaid
flowchart LR
  land(["Lands on /"]) --> home["Home"]
  home -- "View Experience" --> exp["Experience"]
  home -- "nav" --> proj["Projects"]
  proj -- "nav" --> dash["Dashboard"]
  exp -- "nav" --> contact["Contact"]
  dash -- "nav" --> contact
  home -- "Get in touch" --> contact
  contact --> form(["Message sent"])
  contact -.-> li["LinkedIn"]
  home -. "Read on Substack" .-> sub["Substack"]
  home -- "nav" --> about["About"]
  about -.-> gh["GitHub"]
```

## 4. Router flow

What happens on page load, on a click, and on browser back or forward (`navigate()` in `index.html`).

```mermaid
flowchart TD
  load(["Page load"]) --> year["Set footer year"]
  year --> gp{"Is the URL hash<br>a known page?"}
  gp -- "yes" --> named["page = hash"]
  gp -- "no or empty" --> fallback["page = home"]
  named --> init["navigate(page) without pushing history"]
  fallback --> init
  init --> once["Bind nav and footer links once<br>Wire the mobile menu button"]

  click(["Click a data-page link"]) --> go["navigate(page)"]
  back(["Back or forward button"]) --> pop["navigate(saved page) without pushing history"]

  init --> same
  go --> same
  pop --> same

  same{"Already on<br>that page?"}
  same -- "yes" --> stop(["Nothing happens"])
  same -- "no" --> fade["Fade out for 180 ms"]
  fade --> swap["Clone the page's template into #page"]
  swap --> isdash{"Dashboard?"}
  isdash -- "yes" --> render["renderDashboard()"]
  isdash -- "no" --> show
  render --> show["Fade in · highlight the active nav link"]
  show --> push{"Push history?"}
  push -- "yes" --> url["history.pushState<br>Home gets /, others get /#page"]
  push -- "no" --> top
  url --> top["Scroll to top"]
  top --> inner["Bind data-page links<br>inside the new page only"]
```

Edge cases:

- **Unknown or empty hash** (`/#foo`, `/`) opens Home.
- **Clicking the page you're on** does nothing: no fade, no history entry.
- **Back and forward** restore the page saved in history without adding a new entry.
- **Leaving the Dashboard** stops its clock. The tick checks that the clock is still on the page.

### Mobile menu (640px and narrower)

```mermaid
stateDiagram-v2
  [*] --> Closed
  Closed --> Open: Tap the menu button
  Open --> Closed: Tap the menu button
  Open --> Closed: Tap a link, which also navigates
```

## 5. Dashboard data flow

The Dashboard has no data of its own. Every time it opens, it reads the cards in `tpl-experience` and `tpl-projects`, so editing those cards updates the charts.

```mermaid
flowchart LR
  te["tpl-experience<br>role cards"] --> rc["readCards()"]
  tp["tpl-projects<br>project cards"] --> rc
  rc --> dd["dashboardData()<br>Roles: parse date ranges, sort by start<br>Projects: flag Ongoing ones"]
  dd --> k["renderKpis()<br>Months of experience · Roles · Projects<br>plus a tile per numeric result"]
  dd --> t["renderTimeline()<br>One bar per role · Data table"]
  dd --> s["renderSkills()<br>Skill counts · Data table"]
  dd --> n["renderNow()<br>Current roles and ongoing projects"]
  rd["renderDashboard()"] --> c["renderClock()<br>Philippines time, updates every 10 s"]
```

| Card field | Read from | Feeds |
|---|---|---|
| Name | `.proj-name` | Timeline labels, tooltips, In progress |
| Org | `.proj-link` text, minus `›` | Timeline labels, In progress |
| Dates | `.proj-footer span` (`Jun 2025 – Present`) | Timeline bars, months of experience |
| Skills | Plain and `.cyan` tags | Toolkit chart |
| Status | `.green` tags: `Current` / `Ongoing`, or a result such as `−50% response time` | In progress list, result tiles |

## 6. Contact form flow

```mermaid
sequenceDiagram
  actor V as Visitor
  participant F as Contact form
  participant W as Web3Forms API
  participant I as Dodi's inbox
  V->>F: Fill in name, email, message and press Send
  alt Access key is still the placeholder
    F-->>V: "Not set up yet", with the email address instead
  else Access key is set
    F->>F: Disable the button and show "Sending…"
    F->>W: POST JSON with the message, access key and honeypot
    alt Accepted
      W->>I: Email the message
      W-->>F: success
      F-->>V: Clear the form, show "Sent ✓" and a thank-you
    else Error or network failure
      W-->>F: error
      F-->>V: Re-enable Send and suggest emailing instead
    end
  end
```

The hidden `botcheck` checkbox is a honeypot. People never see it. Bots that tick it get filtered out by Web3Forms.
