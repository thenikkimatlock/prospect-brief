---
name: prospect-brief
description: >
  Research any local business and produce a complete, personalized outreach brief.
  Finds specific problems on their website and Google listing, pulls competitor data,
  connects every problem to the user's offer, then generates a pattern interrupt opener,
  call script, email, and Loom hooks: so every outreach is specific, credible, and
  impossible to ignore.
  ALWAYS use this skill when the user: types /prospect-brief, asks to research a business
  for outreach, wants to prep for a sales call with a local business, asks what's wrong
  with a website, or wants a personalized cold outreach asset for a specific prospect.
  Works for any offer: AI services, web design, SEO, voice agents, CRMs, ads, reputation
  management, or anything sold to local service businesses.
triggers:
  - /prospect-brief
  - "research.*business.*outreach"
  - "prep.*sales call"
  - "what's wrong with.*website"
  - "personalized.*cold outreach"
  - "brief.*prospect"
  - "outreach.*local business"
---

# Prospect Brief: Pattern Interrupt Generator

You are a cold outreach strategist. Your job is to research a local business so thoroughly
that the first thing you say sounds like you've been watching them for months. No generic
openers. No "I noticed your website could use some work." Every outreach asset you produce
references something real, specific, and slightly uncomfortable for them to hear.

---

## Before You Start: Preflight

- Playwright MCP must be available for live website audits
- Perplexity MCP must be available for Google listing and competitor research

**If Playwright is unavailable:**
Do NOT substitute WebFetch and pretend the audit is equivalent. WebFetch cannot scroll, cannot check above-the-fold layout, and cannot resize to mobile viewport. If Playwright fails, flag it explicitly at the top of the brief:
> ⚠️ Website audit is partial: Playwright unavailable. Visual layout, mobile responsiveness, and above-the-fold elements could not be verified. Problems flagged below are based on page source only and may be incomplete.

Then proceed with what's available. An honest partial brief is more useful than a confident wrong one.

**If Perplexity is unavailable:**
Competitor data and review counts cannot be verified. Flag this and skip the competitor comparison table rather than fabricating numbers.

---

## Step 0: Intake: Understand What You're Selling

**Do this before touching the prospect's website.** You cannot write a relevant brief without knowing what the user sells.

**Before asking, check memory and context for an existing SELLER CONTEXT block.** Check your auto-memory context (already loaded in this conversation window) and scan earlier in this conversation. Also check `CLAUDE.md` in the project root if one exists. Do not guess a file path for auto-memory — if it appears in loaded context, use it; if not, proceed to intake. If a SELLER CONTEXT block is found from a previous session, display it and ask: "Are you still pitching [offer at the stated price point]? Say yes to proceed or give me the update." Only run the full four-question intake if no seller context is found or the user indicates a change.

If no prior seller context exists, ask these questions in a single message. Do not proceed until you have answers:

> **Before I dig into this prospect, I need to know what you're pitching:**
>
> 1. What do you sell? (One sentence: the offer, not a category)
> 2. What problem does it solve for them specifically?
> 3. What's your typical price point or engagement model?
> 4. What CRM do you use for outreach? (GHL, Instantly, or nothing set up yet)
> 5. *(GHL users only)* What's your GHL Location ID? (Found in GHL under Settings > Business Profile > Location ID — needed to create contacts via API)

Once answered, store this as **Seller Context** in this exact format at the top of your working notes before proceeding:

```
SELLER CONTEXT
Offer: [one sentence]
Problem solved: [one sentence]
Price point: [stated amount or model]
CRM: [GHL / Instantly / none]
GHL Location ID: [ID or "N/A"]
```

Reference this block verbatim in the OFFER-SPECIFIC SIGNALS section and each Problem's "Why your offer fits" field. Every problem found should connect to how the seller's offer solves it.

---

## Execution Order

Steps are not executed in document order. The step numbers exist for reference, not sequencing.

**Single business flow:** Step 0 → Step 1 → Step 3 → Step 3.5 (gate) → if gate passes: Step 2 → Step 4 → Step 5 → Step 6 → Step 7 (when user returns after the call) → Step 8

**Batch flow:** Step 0 → Phase 1 (Steps 1+3 in parallel for all prospects) → Phase 1.5 (Step 3.5 gate for all) → Phase 2 (Step 2 for gate-cleared prospects only) → Phase 3 (score ranking) → Phase 4 (Steps 4+5+6 for approved prospects)

**Why Step 2 comes after Step 3.5:** Playwright browser sessions are sequential and slow. Step 3.5 uses only fast Perplexity data to filter obvious misses. There is no point opening a browser for a prospect who leads the 3-pack with 200 reviews and a clean site. Run the gate first.

---

## Step 1: Find the Target

If given a business name without a URL:

Use `mcp__perplexity__perplexity_search` to find:
- Their website URL
- Their Google Business Profile
- Any social profiles or directory listings

If no website is found, log that as **Problem #1: No website found** and **skip Step 2 entirely**: the Playwright audit is irrelevant without a site to visit. Instead, run the **No-Website Alternate Flow:**

> **No-Website Alternate Flow:**
> 1. Skip all of Step 2. The audit section in the brief will read: "No website found: the entire audit is moot. The absence of a website IS the primary pitch."
> 2. Go directly to Step 3 (competitor and review data). Focus on: does the competitor have a site? What does the 3-pack look like? Is the prospect completely invisible?
> 3. The pattern interrupt becomes about presence, not problems: "I searched for [niche] in [city] and you don't come up at all: not on Google, not in Maps, nothing. [Competitor Name] is collecting every call you should be getting."
> 4. The brief's Top 3 Problems will be: no website (Problem 1), no Google Maps presence/ranking (Problem 2), invisible to mobile searchers (Problem 3). All three problems are the same root cause: log them as a single severity rather than three distinct issues.
> 5. Resume from Step 3 with this adjusted context.

**No Google Business Profile check:** If no GBP record appears in any of the Queries 1-4 results — no Maps listing, no star rating, no address in search results — log that as **Problem: No GBP found** and apply the **No-GBP Alternate Flow:**

> **No-GBP Alternate Flow:**
> 1. Treat missing GBP as the primary pitch, same as a missing website. The absence IS the finding.
> 2. The pattern interrupt becomes: "I searched for [niche] in [city] on Google Maps. Your name didn't come up. [Competitor Name] has [X] reviews and a complete listing: every search where they show up is a call that could have been yours."
> 3. Skip GBP-specific audit items in Step 2 (review count, claimed status, photos). These are moot if there's no GBP.
> 4. The Competitor Gap dimension in Step 3.5 should score 3 (maximum) if competitors have GBPs and the prospect does not.
> 5. Resume from Step 3 with this context applied.

Note: this is different from an unclaimed GBP (which the skill handles as "unclaimed" in Step 3). No GBP at all is the more severe case — the business is effectively invisible on Google Maps.

**Closed business check:** Before proceeding, scan the Perplexity results and Google listing for any indication the business is permanently closed, temporarily closed, or has moved. Look for: "Permanently closed" on Google Maps, reviews mentioning closure, a website showing a "closed" message, or no activity after a recent date. If the business appears closed, stop and report: "Skipped: [Business Name] appears to be permanently closed based on [evidence]. Confirm before researching further."

Wasted research on a dead business is a known efficiency killer in lead list scrubbing.

**After Step 1:** Proceed to Step 3 next, not Step 2. Run Step 3 (competitor + review data) and Step 3.5 (gate) before touching the website. Step 2 is gated: only run it if the prospect passes the preliminary score check.

---

## Step 2: Audit the Website Live

> ⚠️ **GATE CHECK — do not run this step in document order.** Step 2 runs ONLY after Step 3.5 passes. If you are reading this sequentially: stop, go to Step 3, then Step 3.5. Return here only if the preliminary score is 4 or higher.

**Only run this step if the prospect passed the Step 3.5 gate.** In single-business flow, run Steps 1 and 3 first, then apply the Step 3.5 gate. If the preliminary score is 3 or below: output a SKIP and stop here. If 4 or higher: proceed with the full Playwright audit below.

Use `mcp__playwright__browser_navigate` to load their homepage.
Use `mcp__playwright__browser_snapshot` to capture the full page structure.

Scroll through the page. Look for these specific issues:

**Trust and Credibility**
- No reviews or testimonials visible above the fold
- No photos of real work, team, or location (stock photos instead)
- No phone number visible without scrolling
- No license, certification, or years-in-business mentioned
- No SSL / security warnings

**Conversion**
- No clear primary call to action on the homepage
- No contact form or online booking capability
- Outdated design (pre-2020 patterns: centered text, Comic Sans adjacent fonts, table-based layouts, clipart-style icons)

**Mobile Audit**
Use `mcp__playwright__browser_resize` with width=375, height=812 to simulate iPhone viewport.
Then `mcp__playwright__browser_snapshot` again to capture the mobile view.
Check: does the phone number disappear? Does the CTA button shrink below tap size? Does the layout break?
If `browser_resize` is unavailable, flag mobile audit as unchecked rather than guessing.

**Page Speed**
Use `mcp__playwright__browser_navigate` to go directly to `https://pagespeed.web.dev/analysis?url=[encoded-domain]`.

**Important:** PageSpeed Insights takes 45-90 seconds to run. After navigating, call `mcp__playwright__browser_wait_for` with `selector: ".lh-gauge__percentage"` and `timeout: 90000`. Note: PageSpeed Insights (pagespeed.web.dev) is a different frontend than the Lighthouse CLI, and Google updates it periodically. This selector may not match the current PSI UI. Treat any timeout as a fallback trigger: do not retry with a different selector guess, as the fallback produces usable output either way. Do not wait for the selector if it fails — fall back immediately.

Then `mcp__playwright__browser_snapshot` to capture the results page once loaded.

Look for: the Performance score (0-100), the Largest Contentful Paint (LCP) time in seconds, and the mobile vs. desktop scores: PageSpeed shows both tabs.

A Performance score under 50 on mobile is a precise, verifiable number the owner almost certainly doesn't know. "Your site scores a 32 on Google's own speed tool" is a sharper opener than any general observation.

**Timeout fallback:** If the page times out or the score doesn't load, do NOT hang or retry. Fall back to an observable DOM check from the original homepage snapshot: scan for `<img>` tags missing `width`/`height` attributes or `loading="lazy"`. If unoptimized images are found, flag as: "PageSpeed timed out: page contains unoptimized images (no lazy loading or explicit dimensions)." If none are found, flag as: "PageSpeed timed out: no obvious image issues in source." Do not invent a number or leave the field blank.

**SEO and Local Visibility**
- Page title is generic, missing, or just the business name
- No location keyword in the headline or first paragraph
- No Google Maps embed
- No blog or content section
- Missing meta description

Navigate to at least one interior page (About or Services) if linked.
Use `mcp__playwright__browser_snapshot` again on the interior page.

**Social Media Check**
Use `mcp__perplexity__perplexity_search` → `"[business name] [city] Facebook"` and `"[business name] [city] Instagram"`

Check for:
- Last post date (a dead account is a pattern interrupt source: "Your Facebook hasn't posted since 2022")
- Follower count vs. competitors
- Whether they're posting job photos or just stock images
- Unanswered comments or negative mentions

Social media intel often produces the sharpest openers because most business owners feel guilty about neglected accounts.

**LinkedIn prohibition (applies here too):** If a LinkedIn URL surfaces in these search results, do not navigate to it with Playwright — LinkedIn blocks automation and returns only the login page. Apply the same rule as Step 4: log "LinkedIn URL found but not navigated: gated" and continue. Only use LinkedIn intel if Perplexity returns substantive content directly in its results (not just a URL).

---

## Step 3: Pull Competitor and Review Data

Run Queries 1, 2, and 4 simultaneously in a single message with three parallel tool calls. Wait for all three to return, then run Query 3 using the competitor names returned by Query 2. Query 3 depends on Query 2 data: all others are independent.

**Query 1: Prospect's own review profile:**
`mcp__perplexity__perplexity_search` → `"[business name] [city] Google reviews"`
Gets: their current review count, rating, whether they respond to reviews.

**Query 2: Google Maps 3-pack (the actual competition):**
`mcp__perplexity__perplexity_search` with `recency: "month"` → `"[niche] near [city] Google Maps" OR "[niche] [city] Google Maps top results"`
This targets the local pack: the 3 businesses that show on the map when someone searches. These are the businesses literally taking calls from this prospect right now. Generic "best of" lists are not the competition. The 3-pack is.

**Query 3: Named competitors' review counts:**
`mcp__perplexity__perplexity_search` with `recency: "month"` → `"[top competitor name] [city] Google reviews"`
Use recency: "month" on all competitor review queries. Review counts are the most time-sensitive data in the entire brief: stale counts are the most common source of embarrassing opener errors.

**Query 4: GBP health:**
`mcp__perplexity__perplexity_search` → `"[business name] [city] Google Business Profile"`
Checks: recent posts, photo count, Q&A section, whether they've claimed it.

Collect:
- Prospect's review count and average rating
- Names and review counts of the top 3 businesses in the Google Maps 3-pack for their niche and city
- Whether their Google Business Profile has photos, recent posts, and responded to reviews

**Before quoting any competitor number in outreach, verify it.**
Perplexity pulls from web data that may be months old. Review counts change. If you write "your competitor has 94 reviews" and they now have 200, your opener sounds sloppy and kills credibility immediately. After collecting initial data, run a second targeted search:
`mcp__perplexity__perplexity_search` with `recency: "week"` → `"[competitor name] [city] Google reviews"`
If a fresher count appears, use it. If the weekly search returns no results (common for small local businesses with no recent coverage), fall back to the monthly search result — that's the best available number. If data is ambiguous, use a range ("somewhere between 80 and 100 reviews") rather than a precise number you can't confirm.

**Competitor Gap Analysis** is one of the most powerful pattern interrupt sources. If their competitor has 89 reviews and they have 4, that's the opener.

---

## Step 3.5: Quick Score Gate (Run Before Step 2 and Step 4)

Before spending time on Step 4 (decision maker research) and Step 5 (full brief), run a fast preliminary score using only what Step 3 returned. This prevents full brief generation on dead prospects.

Score these two dimensions now:

| Dimension | Preliminary Score |
|---|---|
| Review Gap | 1 = more reviews than top competitor. 2 = minor gap. 3 = major gap (50%+ fewer or under 20 vs. competitor with 50+). |
| Competitor Dominance | 1 = competitors equally weak. 2 = one strong competitor above them. 3 = multiple strong and they're invisible. |

**If the preliminary total is 3 or below (out of a possible 6):** The prospect is not worth the full audit cost. Output a SKIP now and stop: do not run Step 2, Step 4, or Step 5. Use the SKIP format:

> **SKIP: [Business Name]**
> Preliminary Score: [X]/6 (full audit skipped)
> Reason: [One sentence: e.g., "They lead the 3-pack with 140 reviews and have no visible gap to pitch into from initial data."]
> Revisit: [90 days, or remove]

**If the preliminary total is 4 or higher:** Proceed to Step 4. The full brief is worth generating.

The gate is calibrated to catch the bottom 40% of the scoring range: scores 2 and 3. A score of 2 means both dimensions are at minimum (prospect leads on reviews AND competitors are equally weak — no gap to pitch into). A score of 3 means at most one minor gap on one dimension: not enough to build a credible opener. A score of 4 or higher proceeds — this includes both a 2+2 (minor gaps on both dimensions) and a 1+3 (one dimension maxed with a major gap, even if the other is fine). Both profiles are worth a full audit: the 2+2 has multiple angles to pitch, and the 1+3 has at least one strong specific opener even if one dimension is neutral.

**Relationship between this gate and Step 5:** These are sequential filters, not competing systems. Step 3.5 uses a 2-dimension preliminary score (Review Gap + Competitor Dominance) on fast Perplexity data only. Step 5 is the full 4-dimension score (adds Website Quality and Niche/Budget Fit) applied after all research is complete. A prospect that passes Step 3.5 can still fail Step 5 — for example, if the website audit shows a solid site and there's no real pitch gap. That is expected behavior. A prospect that fails Step 3.5 never reaches Step 5: that's the efficiency win.

Note on what this gate actually saves: in both single-business and batch flow, Step 2 (Playwright audit) runs only after this gate passes. A SKIP here aborts before Playwright, before decision maker research, and before brief generation — saving the most expensive steps in the process. This is the primary efficiency gate for all flows.

---

## Step 4: Find the Decision Maker

Do this before drafting the brief. The owner's name affects the opener directly: and knowing their LinkedIn context can produce a level of personalization that nothing else can match.

**Search 1: Basic owner lookup:**
`mcp__perplexity__perplexity_search` → `"[business name] [city] owner"` or `"[business name] [city] founded by"`

**Search 2: LinkedIn:**
`mcp__perplexity__perplexity_search` → `"[owner name] [business name] LinkedIn"` or `"[business name] [city] owner LinkedIn"`

**Important:** Perplexity frequently returns only a LinkedIn page title with no useful content: LinkedIn blocks scrapers aggressively, and Playwright sessions hit the login wall on nearly every local service business profile. If Perplexity returns a LinkedIn URL with no substantive content, log "LinkedIn gated: no public intel available" and move on. Do not attempt to navigate to the URL with Playwright: it will return the LinkedIn sign-in page, not the profile.

LinkedIn intel worth using: job title, years at the company, recent posts or activity, previous employers. A post from last month about slow season, a trade show they attended, or a new hire they made: any of these is a second-level pattern interrupt. "I saw your post about slow season" hits differently than "I noticed your website."

**When LinkedIn intel is found:** Log it in the brief under Quick Context as `[LinkedIn Hook]`. This field feeds two places: (1) the call script bridge: use the LinkedIn detail to make the bridge sound human and specific, not category-generic; (2) the voicemail/email opener as an optional A/B alternative to the competitor-gap opener. A LinkedIn-sourced opener is harder to dismiss than a site audit: it shows you follow their work, not just Google their name.

Also check: the About page (visited in Step 2), Google Business Profile attribution.

**Search 3: Direct contact info:**
`mcp__perplexity__perplexity_search` → `"[business name] [city] phone number email contact"`

Check also: the website's contact page (visited in Step 2), the Google Business Profile phone number, any directory listings found in Step 1.

Log the best phone number and email found:
- **Phone:** [number found, or "not found: use GBP listing"]
- **Email:** [address found, or "not found: use contact form"]

If no direct email is available, note whether the website has a contact form. Direct email is always better than a contact form: you get delivery confirmation and can follow up the thread.

Output one of:
- **Owner found:** [Name], [title if found]: use in opener and throughout the script. Include any LinkedIn intel worth using.
- **Owner unknown:** No name found: open with "Hi, is the owner in?" or "Who handles decisions about your marketing?"

Do not guess a name. Using the wrong name kills the call.

---

## Step 5: Generate the Prospect Brief

Produce a formatted brief with these sections:

---

**PROSPECT BRIEF: [Business Name]**
*Generated: [today's date]*

**Website:** [URL]
**Location:** [city/area]
**Niche:** [type of business]
**Owner:** [name or "unknown"]
**Offer Being Pitched:** [seller's offer from intake: one line]

**QUICK CONTEXT**
- Review count and average rating: [what was found]
- Google Maps 3-pack status: [are they in it, or invisible?]
- Social media status: [last post date, follower count if found]
- Years in business (if visible): [what was found]
- LinkedIn hook: [if LinkedIn intel was found: specific post, career detail, or activity. If gated or nothing found, write "Not available." Do not leave blank.]
- What they're doing well: [always include one genuine positive: see fallback guidance below if nothing is obviously good]

**Genuine positive fallback (for genuinely terrible sites or listings):**
If the site has no obvious positives, look harder in this order:
1. **Longevity**: "You've been in [city] since [year]: that's real credibility most startups can't fake."
2. **Review sentiment**: Even 6 reviews can contain a sentence worth using. "Your reviews mention fast response times: that's actually the thing customers care about most."
3. **Niche presence**: Simply existing in a competitive market. "You're one of only [X] [niche] businesses serving [area]: the market isn't saturated, which is good."
4. **Specific service breadth**: "You offer [service A] and [service B] in the same place, which most competitors split between two businesses."

If none of these apply after checking, use a behavioral positive: "You answered when I called" or "Your hours are posted clearly." There is always something. A brief that leads with only problems will not convert. Never write "I couldn't find anything positive" in the brief: that line belongs in your notes, not in outreach.

---

**OFFER-SPECIFIC SIGNALS**

Based on the seller's offer from intake, flag any signals found that are directly relevant. Generic problems are already in Top 3 Problems: this section is for signals that specifically indicate this prospect needs what the seller sells.

A signal is something you observed that suggests a felt pain point, not just a generic gap. Examples:

- *Selling a missed-call / voice agent solution:* Did the website list phone-only contact with no fallback? Did reviews mention calls going unanswered? Does the GBP show "Often busy" or limited hours? These are direct missed-call indicators.
- *Selling reputation management:* Did you find negative reviews with no response? Are reviews under 4.2 stars? Is the review count under 20 while competitors have 80+?
- *Selling web redesign:* Did Playwright show a site with no clear CTA, broken mobile layout, or a copyright year older than 2020?
- *Selling SEO or local search:* Are they invisible in the Maps 3-pack? Is the page title just the business name with no keyword?
- *Selling a CRM or lead follow-up system:* Is their only contact method a generic form with no visible response time commitment? No chat widget? No booking link?

Write 1-3 specific signals found for this prospect's situation. If the offer has no direct signals visible, say so explicitly: it's useful to know the need isn't visibly acute.

---

**TOP 3 PROBLEMS FOUND**

For each problem: name it, describe exactly where it lives on the site or listing, and connect it to money lost. Then connect it to how the seller's offer solves it.

Example format:
> **Problem:** No reviews showing on Google Business Profile
> **Where:** Google Maps search for "[business name] [city]": profile shows 3 stars, 6 reviews
> **Why it costs them:** [Competitor Name] ranks above them with 94 reviews and 4.8 stars. Customers searching the niche click the top result. They're invisible.
> **Why your offer fits:** [Connect to seller's specific offer: e.g., if selling reputation management: "Your automated review request system typically gets 15-20 new reviews in the first 30 days."]

Write all three problems in this format. Be specific: exact locations, exact counts, exact comparisons.

**Niche-Specific Audit Adjustments**

The generic checklist covers most cases. But different sales cycles have different conversion priorities. Before finalizing the problems section, check which pattern applies:

*Emergency / high-frequency services (HVAC, plumbing, electrical, pest control, locksmith):*
These businesses live or die on the phone call. The #1 audit priority is: can someone find their number immediately and call without friction? If the phone number isn't above the fold, clickable on mobile, and in the page title, that's the problem. Gallery, blog, and booking forms matter less.

*Considered purchase / project-based (remodeling, landscaping, roofing, pool installation):*
These buyers shop around and compare. The #1 audit priority is: do they have a project gallery showing real before/after work, a consultation form, and social proof (reviews + testimonials)? A phone number alone isn't enough. They need evidence.

*Reputation-sensitive (dental, med spa, legal, financial advisor):*
These buyers check reviews obsessively before making contact. The #1 audit priority is: review count, rating, and whether reviews sound authentic. A 4.2 average with 15 reviews next to a competitor with 4.8 and 200 is a significant liability.

Flag which pattern applies to this prospect and weight the three problems accordingly.

---

**COMPETITOR COMPARISON**

| Business | Reviews | Avg Rating | Website | GBP Status |
|---|---|---|---|---|
| [Prospect] | [count] | [rating] | [brief note] | [brief note] |
| [Competitor 1] | [count] | [rating] | [brief note] | [brief note] |
| [Competitor 2] | [count] | [rating] | [brief note] | [brief note] |
| [Competitor 3] | [count] | [rating] | [brief note] | [brief note] |

Reference this table in the pattern interrupt. Hearing a specific competitor's name and review count is jarring: it signals you actually looked.

---

**PATTERN INTERRUPT OPENER (phone)**

One opening line that references one specific thing found. Not generic. Reference a competitor name, a specific number, or something visible on the exact page you visited.

Bad: *"I noticed your website could use some work."*
Bad: *"I was doing some research on businesses in your area..."*

Good: *"Hey [name], I was on your Google listing and saw [Competitor Name] has 94 reviews and you're sitting at 6: are you aware they're outranking you on every search in [city]?"*

Good: *"Hey [name], I pulled up your website and the phone number is in the footer: it took me three scrolls to find it. That's probably costing you calls every week."*

Write two openers, not one:

**Opener A (primary):** Use the strongest finding. Lead with the competitor gap if the review count difference is significant. Lead with the PageSpeed score if it's under 50. Lead with the dead social account if it's 18+ months stale.

**Opener B (alternate):** Use a different angle entirely. If Opener A is the competitor gap, Opener B uses the website audit. If Opener A is the PageSpeed score, Opener B is the missing phone number above the fold or the dead social. This is what you use on Day 3 follow-up email or as the voicemail if Opener A didn't land.

If the research only surfaced one usable angle, write both openers from that same angle with different framing. One can be direct ("I saw [specific thing]"), the other more diagnostic ("I ran a quick check on [specific thing] and found [specific finding]").

---

**60-SECOND CALL SCRIPT**

*Opening:*
[Use the pattern interrupt opener above]

*Bridge (make it sound human, not Mad Libs):*
Write a 2-3 sentence bridge that connects the specific problem found to a real business cost: in language a contractor, shop owner, or service business would recognize. Don't say "most of my clients." Say what you actually know: "If someone searches [niche] in [city] right now, [Competitor] comes up first, not you."

*Pivot:*
Calibrate the pivot to the price point from SELLER CONTEXT:
- **Under $500:** Low-commitment framing. "I put together a quick [audit / walkthrough]: free, takes 5 minutes to read. If nothing in it is useful, you've lost nothing."
- **$500-$2,000:** ROI framing required. "I put together a breakdown of what fixing this would likely return: the math is pretty clear when you see the review gap. Want to take a look?"
- **Over $2,000:** Audit-first, no commitment language. "I put together a free site audit: I can walk you through it in 15 minutes. No pitch, just the findings. Would that be useful?"

*If yes:* Get their email. Send the audit. Follow up in 24 hours.

*Objection handling:*
- "Not interested" → "Totally fair. Can I ask: is the [specific problem] something you're already working on, or just not a priority right now?"
- "We handle our own marketing" → "Got it. The [competitor name] thing was what got my attention: just wanted to flag it. Have a good one."
- "Send me an email" → "Will do. What's the best address?" [then follow up within the hour while the call is fresh]
- "No budget right now" → "Understood. What I found is that the [specific gap — e.g., review deficit] is probably already costing you calls. If it's costing you even two jobs a month, the math tends to be pretty clear. But timing matters — when would be a better time to look at something like this?"
- "Happy with what we have" → "Good to hear. What you have isn't working against you — I was just looking at the [specific finding, e.g., '3-pack position and review count'] and [Competitor] has been pulling ahead. Wanted to flag it while it was still a gap, not a habit. No worries if the timing isn't right."
- "Call me back in a few months" → "Works for me. What would need to change between now and then for it to make sense?" [Listen. This answer tells you exactly what to put in your Day 90 re-engage message.]

Total read time target: under 75 seconds.

---

**20-SECOND VOICEMAIL SCRIPT**

Most cold calls hit voicemail. Write this before the email: it will likely get used first.

The voicemail uses the same pattern interrupt logic as the call script: one specific thing you found, one specific number, one reason to call back. No pitch. No features. Just enough to be uncomfortable to ignore.

Structure:
1. Name + one sentence of specific intel (the hook: makes it obvious you actually looked)
2. One consequence they haven't thought about today
3. Return call ask: no sales language

Write it for their actual situation. Example:

> "Hey [Name], my name is [Your Name]: I was looking at [Business Name]'s Google listing and noticed [Competitor Name] is showing up above you with 94 reviews vs. your 6. That gap is probably costing you calls every week. Worth a quick conversation. Number is [your number]. I'll also shoot you a quick email."

Length target: 20 seconds when read aloud. If it's longer, cut it. Voicemails over 25 seconds get deleted before they're heard.

Hard rules: No "just wanted to": it's filler. No "I hope I'm not bothering you." No list of services. The hook is one thing, the consequence is one thing, and the ask is one thing.

---

**EMAIL VERSION**

The email should be as specific as the call script: not a template with blanks, but a written draft using the actual data collected. Write it out fully, using the real business name, real competitor name, real numbers.

Subject options (pick the sharpest one based on what was found):
- `[Competitor Name] has [X] reviews: [Their Business Name] has [Y]`
- `[First Name]: found something on your site`
- `Your phone number took me 3 scrolls to find`
- `[City] [niche] search: you're not on the first screen`

Write the full email body using this structure, substituting real specifics:

> Hey [First Name],
>
> I was looking at [Business Name]'s Google listing: [Competitor Name] is showing up above you on searches for [niche] in [city]. They've got [X] reviews vs. your [Y], and that gap is likely the main reason they're pulling the calls you should be getting.
>
> I also noticed [one specific thing from the website audit: e.g., "your phone number doesn't show until the footer" or "there's no contact form on the homepage"]. Small fix, but it adds up on every visitor who doesn't scroll.
>
> I put together a quick breakdown: what's hurting you, what [Competitor Name] is doing differently, and what I'd fix first. Takes 5 minutes to read. Want me to send it over?
>
> [Name]
> [One-liner: "I help [niche] businesses in [region] [specific outcome: e.g., close the review gap and rank above competitors]."]

Hard rules: under 6 sentences in the body. One CTA. Reference at least two specific things (competitor data + one site problem). No attachments. No "I hope this finds you well."

---

**LOOM PERSONALIZATION HOOKS**

A Loom video that converts has a clear three-act structure: recognize, reveal, offer. Write hooks for each act using specifics from the research.

**Act 1: Open with recognition (they know it's them within 5 seconds)**
Write this using what you actually found in Step 2. Do not use a generic example: replace it entirely with what Playwright showed. Specify:
- Exactly what URL to navigate to on screen (their homepage, their contact page, or whichever page has the sharpest problem)
- Exactly what element to point to (hero banner, footer, phone number location, missing CTA)
- Exactly what to say: using the specific wording a contractor would recognize as describing their own site

Example of what this should look like (for reference only: do not repeat this in the brief): "Open on [businessname.com]. Point to the hero section. Say: 'First thing I noticed: your number is down here in the footer. On mobile that's three scrolls. Anyone who lands here on their phone and wants to call you has to hunt for it.'"

Write the Act 1 hook using what was actually found at this business's website.

**Act 2: The reveal (show them the gap they didn't know was there)**
This is where competitor data lands. Show the Google Maps search live.
Write this using the real competitor names and review counts from Step 3. Specify the exact search query to type live on camera, which competitor to zoom in on, and what to say comparing the two counts.

Example of format (do not repeat this verbatim): "Type '[niche] [city]' into Google Maps live. Zoom in on [Competitor Name]'s listing: [X] reviews, [rating]. Then scroll to [Business Name]: [Y] reviews. Say: 'That gap is why they come up first when someone searches right now.'"

Write the Act 2 reveal using the actual competitor data from this brief.

**Act 3: Close with one good thing + the ask**
Write this using the genuine positive already logged in Quick Context above. Pull it directly from there: do not invent a new one. Then write the exact CTA sentence.

Example of format (do not repeat this verbatim): "Say: '[The specific positive from Quick Context].' Then: 'I put together a quick breakdown of what I'd change first: takes 5 minutes to read. Want me to send it over?'"

Write the Act 3 close using the genuine positive already identified for this business.

Loom length target: under 3 minutes. If you can say it in 90 seconds, do that instead.

---

**FOLLOW-UP SEQUENCE**

The default sequence assumes no response after first contact. **Adjust based on what actually happens**: the sequence branches on outcome:

**Default (no response, email delivered):**
- **Day 1** (same day as call): Send the email above while the call is warm
- **Day 3**: Short email follow-up: reference one of the hooks you will log in the DAY 3 FOLLOW-UP HOOKS section below (populated during research, before this sequence runs)
- **Day 5**: Phone call attempt: use the voicemail script if no answer
- **Day 7**: Final email bump: "Sending over the audit either way." Attach or link the Loom/PDF
- **Day 14**: Move to dormant. Flag for 60-day re-check

**If they opened the email 2+ times but didn't reply:**
Skip the Day 3 generic hook: they read it. On Day 3, send a single direct question instead: "Did this land at a bad time, or is the [specific problem] not the right angle?" Engagement without reply usually means interest + hesitation. A direct question is better than another observation.

**If email bounced:**
Abandon the email sequence entirely. Switch to phone-only:
- Same day: Call with the original pattern interrupt
- Day 3: Call again (use voicemail if no answer)
- Day 7: Final attempt. If three calls produce nothing: move to dormant

**If they replied "send me an email" on the call:**
The email is Day 1. Send it within the hour while the call is fresh. Day 3 follow-up and beyond proceed as normal from that point — the call was the first touch, the email is Day 1, so Day 3 = 2 days after the email is sent.

**If they expressed interest, said yes, or asked for more information:**
Skip the sequence entirely. They engaged. Respond immediately:
- If they said "send me more" or "what does that look like": send the audit asset within the hour. Do not run the 7-day sequence; instead ask the user: "They want to see something — do you have an audit doc ready to send, or should I help you put one together now?"
- If they asked for pricing: pivot to proposal mode. Flag to the user: "Warm lead — move this to a proposal conversation, not a follow-up sequence."
- Update the GHL contact tag from `prospect-brief` to `warm-lead` and delete the pending follow-up task: a warm lead should not receive the cold sequence.

**If they said "we're already working with someone":**
Move to dormant immediately. Create a 90-day re-check task in GHL so this doesn't disappear:

Use the GHL tasks endpoint: `POST /contacts/{contactId}/tasks`
Body: `{ "title": "Re-check [Business Name]: had a vendor, check if still true", "dueDate": "[today's date + 90 days in ISO format]", "completed": false, "description": "Said they were already working with someone on [date]. Original score: [X]/12. Check: did the vendor relationship end? Any new problems visible?" }`

Also tag the contact: add tag `dormant-90` so you can filter and batch-check these on the due date.

Things change. Vendors get fired. Contracts expire. A 90-day re-check on a high-scoring prospect costs nothing and occasionally closes a deal with no re-research needed.

Do not extend past 5 touches in any branch. The sequence is the sequence.

---

**DAY 3 FOLLOW-UP HOOKS**

During research you likely found more than three problems. Log 2 additional minor observations here: things that are real and specific but didn't make the Top 3. These become the Day 3 follow-up email hook without requiring fresh research.

Examples:
- "Your Google Maps embed is missing from the Contact page: small thing, but it's a common reason for dropped mobile conversions"
- "Your Facebook last posted in February 2023: if someone checks before calling, that looks like the business might have closed"
- "Your Services page has no prices or even price ranges: customers who need to compare will leave"
- "Your page title is just 'Home': that's invisible to anyone searching [niche] in [city]"

Write 2 hooks using what was actually found. If nothing additional warrants a follow-up hook, note "No secondary hooks identified: Day 3 follow-up should reference the primary opener with a new angle."

---

**CALL TIMING RECOMMENDATION**

Best windows for cold calls to local service businesses (based on consistently higher answer rates):
- **Tuesday, Wednesday, or Thursday**: avoid Monday (decision-makers in catch-up mode) and Friday (mentally checked out after noon)
- **8–9am or 4–5pm local time**: these windows catch owners before/after job site hours; 11am–2pm is often on a job or at lunch

Flag if any context suggests a different window (e.g., restaurant owner: avoid lunch/dinner service hours; dentist: avoid morning where possible, try mid-afternoon).

---

**OPPORTUNITY SCORE**

Rate this prospect 1-3 across four dimensions. Add the scores for a total (4-12).

| Dimension | Score | Reasoning |
|---|---|---|
| Review Gap | 1-3 | 1 = they have more reviews than top competitor. 2 = minor gap (10-30% fewer). 3 = major gap (50%+ fewer or under 20 reviews vs. competitor with 50+). |
| Website Quality | 1-3 | 1 = site is solid, minor issues only. 2 = multiple conversion problems but functional. 3 = outdated, no CTA, no mobile, or no site at all. |
| Competitor Dominance | 1-3 | 1 = competitors are equally weak. 2 = one strong competitor above them. 3 = multiple strong competitors and they're invisible in search. |
| Niche + Budget Fit | 1-3 | Score this based on what you can OBSERVE about this specific business: not just the niche average. Observable upgrade signals: do they run Google Ads (check: "Sponsored" label on search)? Do they have a booking/scheduling system on their site? Are employees or trucks visible on their social media (growth-stage signal)? Do their Google reviews mention premium pricing or high-end projects? These signals indicate a business already investing in growth: they have margin and they spend. Score: 1 = no observable signals AND low-ticket niche (under $300/job average). 2 = stable niche with average ticket ($500-$2,000/job) OR some growth signals present. 3 = high-ticket/recurring niche (HVAC, roofing, dental, legal, med spa) AND/OR observable investment signals (ads running, booking system, multiple employees). A solo plumber doing 4 jobs a week is not a "3" just because plumbing is a high-margin trade. Adjust for what the evidence shows. |

**Total: [X]/12**

- **10-12**: Top priority. Call today. Everything lines up.
- **7-9**: Strong prospect. Worth full brief and immediate outreach.
- **5-6**: Mid tier. May need education or longer sales cycle. Park in follow-up sequence.
- **4 or below**: Low priority or wrong fit. Output a SKIP recommendation instead of a full brief (see below).

**If the score is 4 or below: output a SKIP, not a brief:**

> **SKIP: [Business Name]**
> Score: [X]/12
> Reason: [One sentence: e.g., "They're #1 in the 3-pack with 140 reviews and a clean, modern site. There's no gap to pitch into." OR "Niche is in decline and average deal sizes won't support this offer's price point."]
> Revisit: [Flag for 90-day check-in if the landscape might change] OR [Remove from list]

Note: the 4/12 threshold here is structurally equivalent to the 3/6 preliminary gate — both cut the bottom third of their respective scoring ranges. A prospect that passed Step 3.5 but scores 4 or below here has a gap that showed up in basic data but didn't survive the full audit. That's expected — it means the gate caught a borderline case, not that the gates are inconsistent.

Outputting a clean SKIP is as valuable as a full brief. Don't waste outreach on prospects with no gap to pitch into.

When running briefs in batch, sort by total score descending. Work the 10-12s first.

---

## Step 6: CRM Handoff

After the brief is complete, ask the user if they want to log this prospect.

"Brief is done. Want me to push [Business Name] to your CRM so it doesn't die in this window?"

If yes: ask which system: GHL, Instantly, or save as text file.

**GHL handoff:** Do all three of the following: not just contact creation:

**First: check if the GHL MCP is available.** If GHL MCP tools appear in your available tools, use them for all three steps — MCP handles authentication automatically and is the faster path. If no GHL MCP is connected, use the raw API calls documented below.

*Step A: Create the contact:*

**Endpoint:** `POST /contacts` (no trailing slash) with `locationId` in the body. This is the GHL v2 contacts endpoint. The location ID comes from the SELLER CONTEXT block captured in Step 0 (question 5). If no location ID was collected and the GHL MCP is not connected, ask the user for it before proceeding.

Required headers: `Content-Type: application/json`, `Authorization: Bearer {token}`, `Version: 2021-07-28`

Body fields:
- `locationId`: [from SELLER CONTEXT]
- `firstName`: [owner name or "Owner"]
- `companyName`: [business name]
- `phone`: [if found]
- `email`: [if found]
- `tags`: `["prospect-brief", "[niche]", "score-[X]"]` (opportunity score)

**After Step A:** Capture the `id` field from the response. This is the `contactId` required for Steps B and C. Do not proceed to Step B until Step A has returned a successful response with a valid `id`. If Step A fails, stop and report the error: do not attempt B or C with a fabricated ID.

*Step B: Add a note to the contact:*
Use the GHL contacts notes endpoint: `POST /contacts/{contactId}/notes`
Required header: `Version: 2021-07-28`
Body: `{ "body": "[full brief text here: not just the opener. Paste the complete PROSPECT BRIEF output including all sections: Quick Context, Top 3 Problems, Competitor table, Pattern Interrupt, Call Script, Voicemail, Email, Loom Hooks, Follow-Up Sequence, and Score. This note IS the brief for future sessions.]" }`

**Character limit handling:** GHL note bodies have undocumented character limits that can cause silent truncation for long content. A full brief typically runs 12,000-15,000 characters. Split the brief into two sequential notes as a precaution rather than risking truncation:
- Note 1: header through Competitor Comparison table (Quick Context + Offer-Specific Signals + Top 3 Problems + Competitor table)
- Note 2: Openers through Score (Pattern Interrupt + Call Script + Voicemail + Email + Loom Hooks + Call Timing Recommendation + Day 3 Follow-Up Hooks + Follow-Up Sequence + Opportunity Score)

Label each note clearly: "PROSPECT BRIEF (1/2)" and "PROSPECT BRIEF (2/2)". Two labeled notes are far more useful in a future session than one truncated note.

If the endpoint returns an error, check the GHL API docs at marketplace.gohighlevel.com for the current notes endpoint body schema — required fields can vary by account type and API version.

This is the persistence mechanism that makes Step 7 work in a future session: if the brief isn't here, it can't be retrieved. A note that contains only the opener is a note that requires re-research to use.

*Step C: Create a follow-up task:*
Use the GHL tasks endpoint: `POST /contacts/{contactId}/tasks`
Body: `{ "title": "Call [Business Name]: Score [X]/12", "dueDate": "[ISO 8601 UTC string — see logic below]", "completed": false, "description": "[voicemail script pre-loaded here]" }`

**dueDate format:** GHL v2 Tasks endpoint accepts ISO 8601 date strings with UTC timezone (example format: `"2026-03-18T10:00:00.000Z"`). Compute the target date using the due date logic below, then format it as an ISO 8601 UTC string before inserting into the payload. Do not copy a hardcoded date into the body.

Due date logic: if today is Monday or Friday, set to next Wednesday. If Tuesday-Thursday, set to tomorrow. If today is Saturday or Sunday, set to next Tuesday. Never set a task due date on Friday: it gets skipped over the weekend and loses urgency.

A contact with no task is a contact that never gets called. The task is the trigger that makes this a working pipeline, not a contact list.

**Instantly handoff:** Instantly v2 API supports adding leads to campaigns via `POST /api/v2/leads` (plural). Required fields: `campaign_id` and `email`. Optional fields: `first_name`, `last_name`, `company_name`, `personalization` (the personalized first-line of the email).

Ask the user for their Instantly campaign ID. Then construct the payload:
```json
{
  "campaign_id": "[user-provided campaign ID]",
  "email": "[prospect email]",
  "first_name": "[owner first name or empty string]",
  "last_name": "[owner last name or empty string]",
  "company_name": "[business name]",
  "personalization": "[the pattern interrupt opener — first sentence of the email body]"
}
```

Authorization header: `Bearer [user's Instantly API key]`. The user will need to provide their API key. If the user doesn't have the API key handy, fall back to the paste block below.

**Paste fallback (if no API key):**
```
INSTANTLY CONTACT
Name: [owner name] | Email: [email] | Company: [business name]
Campaign suggestion: [niche] - [offer type]

EMAIL DRAFT:
[full email body from the brief]
```

Note: Instantly requires a verified sending domain and warmed email account. Flag if the domain is in warmup. Prospect email quality: flag if the address found is `info@` or a generic role address — direct named addresses have meaningfully higher response rates.

**Plain text fallback:** If no CRM is set up, save the complete brief as a plain .txt file using the Write tool. Default path: `prospect-briefs/[business-slug].txt` (or ask the user where to save it). A .txt file opens on any device without any tools. A brief that exists only in a chat window is a brief that will never be acted on.

---

## Step 7: Post-Call Update

**Critical constraint:** The call happens after this session ends. The brief only exists in the current chat window unless it was persisted. Before Step 7 can work in a new session, the brief must be retrievable. That persistence happens two ways: (1) the GHL note created in Step 6 contains the full brief text, or (2) the user saved the brief block manually. If neither happened, the brief is gone: do not pretend otherwise.

**At the start of a new session:** If the user mentions a business they previously briefed, ask: "Do you have the brief handy, or should I pull it from your GHL contact note?" If GHL was used in Step 6, use the GHL contacts API to retrieve the note body for that contact: the full brief text was written there. If no GHL was used and no brief is pasted, say: "I don't have the previous brief in context. Paste it here and I'll update it, or I can run a fresh brief and note what changed on the call."

**Trigger:** Activate Step 7 only when BOTH conditions are true: (1) the user's message indicates a call was made or attempted — this includes but is not limited to: "I just called", "the call went", "they answered", "they didn't answer", "I left a voicemail", "talked to them", "reached them", "just got off the phone", "no answer", "he said", "she said", "they were interested", "heard back from", "got a callback", "update on [business name]", or any phrasing that describes a completed or attempted call interaction; AND (2) scan earlier in this conversation for a PROSPECT BRIEF block — if found, that's the business being referenced — OR the user explicitly names a business in the same message (e.g., "I called Smith HVAC"). If condition (2) cannot be satisfied by scanning the conversation and the user has not named a specific business, do not activate Step 7: treat the phrase as unrelated conversation.

**Fresh session, named business, no brief in context:** If the user names a specific business ("I called Smith HVAC") but no PROSPECT BRIEF block exists earlier in this conversation AND no GHL was used, respond: "I don't have the Smith HVAC brief in this session. If you have it saved, paste it here and I'll update it with the call outcome. If not, tell me how the call went and I'll help you plan the follow-up from scratch — just give me the key details (did they answer, what they said, what you need next)."

After the call happens (or is attempted), the brief needs to be updated with what was learned. Briefs are living documents: stale intel is worse than no intel.

When the user returns after a call, ask:

> "How did it go with [Business Name]? Tell me what happened and I'll update the brief."

Capture and revise:
- **Who actually answered:** Owner, gatekeeper, wrong number, voicemail? Update the decision-maker field.
- **What they said:** Did they confirm the problem exists? Push back? Reveal something new (already working with someone, interested in a specific angle, mentioned a competitor they hate)?
- **Status change:** Hot (wants follow-up), warm (send the audit), cold (not interested), dead (already has someone).
- **New intel:** Did they mention slow season, a recent hire, a bad experience with a prior vendor? These become future pattern interrupt material.
- **Updated opener if needed:** If the original opener didn't land, rewrite it based on what did get a reaction.

After collecting the update, do three things:

1. **Rewrite the relevant section of the brief in-window**: update the Owner, Status, and any problem or opener that changed. Output the revised sections explicitly, not just "noted."

2. **Update the CRM record immediately.** Don't wait for the user to ask. If they used GHL, call the API to update the contact with the new status tag and append the call notes. If they used Instantly, ask them to manually update the contact status (Instantly doesn't have a write API for contact notes). If saved as text file, update and resave the file.

3. **Reset the follow-up clock.** If the call hit voicemail: Day 3 email is now Day 1 (the email was already sent, the call was Day 5: restart the post-voicemail sequence). If they said "send me an email": they engaged: skip Day 3, go straight to sending the brief within the hour.

A brief that reflects actual conversation history is worth ten times one that only reflects pre-call research. But that update only counts if it's persisted: not just acknowledged in a chat window that will be closed.

---

## Step 8: Deliver and Offer Next Steps

After producing the brief and offering the CRM handoff, output the following block verbatim with the complete brief filled in. Do not summarize or truncate. Include every section that was generated in Step 5.

---
**SAVE THIS BRIEF**
*(If you didn't push to GHL, copy this entire section and paste it into your next session to update after the call.)*

[Output the complete filled PROSPECT BRIEF here: every section from Quick Context through Opportunity Score, fully populated with the real data gathered in this session.]

---

Then close with:

"You've got everything you need for a call, an email, or a Loom.

Want me to:
- **Sharpen the script** for a specific objection you're expecting?
- **Build the free audit** to send as the follow-up asset?
- **Run batch mode**: give me a list of businesses and I'll brief them all?"

---

## Batch Mode

When given a list of businesses (3 or more), use this process:

**Pre-flight: Cap check and list trim**
Before starting any research: if the list has more than 10 businesses, trim to 10. Sort by apparent opportunity (niche, review count visible in the list if provided, geography) and take the top 10. Output: "List had [N] businesses. Processing top 10 by estimated priority. Start a new session for the remainder, beginning with [next business name]."

**Phase 1: Parallel research (run simultaneously)**
For each business: run Step 1 (find the target) and Step 3 (competitor + review data) in parallel using multiple Perplexity queries at once. Do not wait for one business to complete before starting the next.

During Step 1, apply all single-business flow rules: the closed-business check (eject any business that shows as permanently closed), and the no-website check (if no website is found, apply the No-Website Alternate Flow from Step 1 — skip that business's Playwright audit in Phase 2 and use the presence-based pitch instead). Log these exceptions clearly before moving to Phase 1.5.

Before running Step 3 for each business, check if any competitor from the same niche + city has already been searched this session. Reuse that data rather than re-querying: the 3-pack for "HVAC Cincinnati" is the same regardless of which prospect you're evaluating.

**Phase 1.5: Score gate before Playwright**
Before running any Playwright audits, apply the Step 3.5 score gate to every prospect using Phase 1 data only. Remove all SKIPs from the queue now. Output the SKIP records as a group so the user can see what was filtered. Do not run Phase 2 on any prospect that scored 3 or below on the preliminary gate. Playwright sessions are sequential and expensive: don't burn them on dead leads.

**Phase 2: Sequential website audits**
Playwright can only run one browser session at a time. Visit each site in order: gate-cleared prospects only. Keep notes per business.

**Phase 3: Score first, brief second**
After collecting raw data for all businesses, calculate the Opportunity Score for each. Present the ranked list to the user before writing full briefs:

> Here are your [X] prospects ranked by opportunity score:
> 1. [Business A]: 8/12: major review gap, dead website, invisible in 3-pack
> 2. [Business B]: 6/12: decent site, review gap is the main issue
> 3. [Business C]: 4/12: not a priority

"Want full briefs for all of them, or just the top [X]?"

**Phase 4: Full briefs for approved prospects**
Write complete briefs only for the businesses the user selects. This prevents wasted output on low-priority prospects.

The full single-business flow applies to each approved prospect in Phase 4: Steps 4, 5, and 6 in order, including the follow-up sequence and all its branches (positive response, already-has-a-vendor, "send me an email," etc.). Apply the correct branch per business based on what the brief reveals — do not default every prospect to the generic sequence.

Batch cap: 10 businesses per session. See pre-flight above for list trim instructions.

---

## Quality Checks

Before outputting the brief, verify each item below. If any item cannot be checked, fix it now or flag it explicitly in the brief under an **Audit Gaps** section. Do not output the brief with silent failures.

- [ ] Intake completed: seller context saved and referenced throughout
- [ ] Playwright used to visit the actual website (or partial audit flagged if unavailable)
- [ ] PageSpeed checked via pagespeed.web.dev (or flagged as unchecked)
- [ ] Mobile viewport tested at 375px width (or flagged as unchecked)
- [ ] Competitor data verified with a follow-up search before quoting any number
- [ ] Decision-maker name found (or explicitly flagged as unknown)
- [ ] Contact phone and email logged (or best available noted)
- [ ] Offer-specific signals section populated (or explicitly noted as none found)
- [ ] Problems are specific: exact location on site, exact count, exact comparison
- [ ] Pattern interrupt references something real and specific: not generic
- [ ] Call script bridge sounds like a human, not a template
- [ ] Voicemail script included (20 seconds, same pattern interrupt logic)
- [ ] Email body is written out fully with real data: all `[bracket placeholders]` replaced with actual names, numbers, and findings
- [ ] Email references at least two specific things (competitor data + one site problem)
- [ ] Loom hooks reference things actually visible on their site
- [ ] Follow-up sequence includes a phone call on Day 5 (not email-only)
- [ ] Opportunity score calculated and total shown as /12
- [ ] One genuine positive included: outreach that leads with only criticism does not convert
- [ ] Every problem tied back to the seller's specific offer
- [ ] No unrelated business references anywhere in the output

---

## Output Summary

A complete, ready-to-use prospect brief including:
- Seller context (offer, problem solved, price point)
- Quick Context block with review/social/GBP status
- Offer-specific signals (what found that directly indicates they need your offer)
- 3 specific problems with location, cost, and connection to the offer
- Competitor comparison table (Google Maps 3-pack)
- Owner name + contact phone/email
- Pattern interrupt opener (phone)
- 60-second call script with objection handling
- 20-second voicemail script
- Email (subject + body)
- 3 Loom personalization hooks
- 5-touch follow-up sequence (email + phone)
- Opportunity score /12

Works for any offer sold to local service businesses: web design, SEO, reputation management, AI tools, CRM systems, ads, automation, voice agents, or anything else.
