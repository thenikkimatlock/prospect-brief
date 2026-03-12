# Prospect Brief: Pattern Interrupt Generator

A Claude Code skill that researches any local business and builds a complete, ready-to-use cold outreach package in one command.

Every opener it writes is specific. Every competitor stat is verified. Every script sounds like a human, not a template.

---

## What it does

Type `/prospect-brief` and give it a business name. It will:

1. Find the website, Google Business Profile, and social accounts
2. Run a live website audit (Playwright: real browser, not page source)
3. Check PageSpeed score at pagespeed.web.dev
4. Pull competitor review counts from the Google Maps 3-pack
5. Find the owner's name and direct contact info
6. Score the prospect on 4 dimensions (4–12 scale)
7. Generate a complete outreach package:
   - Two pattern interrupt openers (different angles)
   - 60-second call script with objection handling (6 objections)
   - 20-second voicemail script
   - Cold email with subject line options
   - 3 Loom personalization hooks
   - 5-touch follow-up sequence with outcome branches
8. Push the contact + full brief + follow-up task to GHL or Instantly (optional)

Works for any offer sold to local service businesses: AI voice agents, web design, SEO, reputation management, CRMs, ads, automation — anything.

---

## Requirements

- **Playwright MCP** — for live website audits and PageSpeed
- **Perplexity MCP** — for competitor data and Google listing research

Both are available as Claude Code MCP servers. The skill gracefully degrades if either is unavailable and flags what was skipped.

---

## Install

Copy `SKILL.md` into your Claude Code skills folder:

```
.claude/skills/prospect-brief/SKILL.md
```

That's it. Claude Code auto-discovers skills in that folder.

---

## Usage

```
/prospect-brief
```

Then give it a business name (and city if it's a common name):

```
Research Johnson HVAC in Columbus OH — I'm pitching AI voice agents at $297/mo
```

Or just:

```
/prospect-brief Mike's Plumbing Columbus Ohio
```

The skill asks 4 quick intake questions on first use (what you sell, price point, CRM), then stores that context for the session so you don't re-enter it for each prospect.

---

## Batch mode

Give it a list and it processes all of them:

```
Run prospect briefs on these 8 HVAC companies: [list]
```

It runs research in parallel, scores everyone first, shows you the ranked list, then only writes full briefs for the ones you approve. Playwright sessions (the expensive part) only run for prospects that pass the preliminary gate.

---

## Built-in efficiency gates

Running a full audit on every prospect wastes time. The skill has two filters:

**Gate 1 (Step 3.5):** Preliminary score using only fast Perplexity data. Prospects with no visible gap (they lead the 3-pack, strong reviews, no obvious problems) are flagged as SKIP before the browser ever opens. Saves 3–5 minutes per dead lead.

**Gate 2 (Step 5):** Full 4-dimension score after complete research. Low-priority prospects get a SKIP with reasoning instead of a full brief.

---

## Example output

See [`example-output.md`](example-output.md) for a complete brief generated for a fictional HVAC company.

---

## CRM integration

After the brief, the skill asks if you want to push the prospect to your CRM:

- **GHL**: Creates contact, adds the full brief as a note (split across two notes to avoid character limits), creates a follow-up task with the voicemail script pre-loaded. Due date is auto-calculated to avoid Fridays and weekends.
- **Instantly**: Adds lead to your campaign with the pattern interrupt opener as the personalization field.
- **Plain list**: Outputs a one-line summary you can paste into a spreadsheet.

---

## Post-call update

After you make the call, type what happened:

```
I just called Mike's Plumbing — owner answered, said they're happy with their current marketing but seemed interested when I mentioned the competitor gap
```

The skill updates the brief with the new intel, rewrites the relevant sections, updates the CRM record, and resets the follow-up clock.

---

## Who built this

Built by Nikki Matlock. This skill came out of building VoiceForeman — an AI voice agent business for home service contractors — and doing real cold outreach to HVAC, plumbing, and roofing companies. The pattern interrupt system is what actually works in the field.
