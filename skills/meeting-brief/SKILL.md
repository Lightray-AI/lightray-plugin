---
name: meeting-brief
description: Brief the user on the companies in their upcoming meetings, from LightRay's company reports. Use when the user asks to prepare for their meetings, or for a brief on the companies they meet today, tomorrow or this week.
---

# Meeting brief

One short brief per meeting on the outside companies in it, from LightRay's reports. LightRay writes the reports. Your part: find the companies, reuse the reports the user already has, ask before any new one, and pass on LightRay's words with the link.

## 1. Find the companies

- If you can read the user's calendar in this conversation, read their meetings for the time they asked about; tomorrow if they did not say. If you cannot, ask which companies they are meeting, each as *Company name, City*, and go on from their answer.
- Keep the organizations from outside: leave out the user's own organization, personal email addresses, and meetings with no one from outside.
- Name each company as the meeting names it: its title, the invitation, or an attendee's organization. A web address or an email domain is not a name. When a domain is all you have, offer a name as a guess for the user to correct.
- Add the company's city or country only when the meeting or the user gives it. Never guess a place.
- Only a company's name and place go to LightRay: never a person's name, an email address, a meeting title or notes. LightRay checks companies, not people.

## 2. Reuse the reports the user has

Call LightRay's `list_reports` with `limit` 20. A company whose report there is `ready` or `building` is already the user's, and reading it uses none of their reports: call `get_report` with its `report_id`, never `check_company`. Count a report as the same company only when the names match apart from case and legal form (Ltd, Limited, LLC, Inc); if unsure, ask.

If LightRay's tools are not available, ask the user to connect LightRay on the plugin's Connectors tab, and stop. If LightRay asks them to sign in, wait for them, then go on.

## 3. Ask before any new report

If every company is already in the user's reports, skip this step. Otherwise, in one message, list the meetings in time order with their companies, mark each company already in the user's reports "in your LightRay reports", and end with these words:

> Each new company uses one of your LightRay reports. A new report takes about 12 minutes. Which should I check?

Call `check_company` (`name`, and `place` when known) only for the companies the user picks in their reply. Any other new company, one the report suggests or one the user mentions later, is asked about first in the same way.

If no one can reply, as in a scheduled run, start no report the request does not name. Write the brief from the user's own reports, and list the new companies for the user to pick later.

## 4. Act on what LightRay returns

Each result has a `status`:

- `ready`: the report. Write its brief.
- `building`: the report is on its way. Give `minutes_left` and the `link`. Call `get_report` for it again only when the user asks, or after `check_again_minutes` at the earliest; never in a quick loop.
- `choose`: several companies match. Show the `choices` (name, place, registration number where given), ask which one, then call `check_company` with the same `name` and that `choice` token.
- `several`: the words named more than one company. Ask which to check; each is a new report.
- `limit` or `paused`: pass on LightRay's `message`, and start no more reports in this brief.
- Any other status: pass on LightRay's `message`, then go on with the other companies.

Pass on a message as LightRay wrote it, speaking to the user ("your" for "the reader's"), adding nothing.

## 5. Write the brief

One block per meeting, in time order:

```
**{meeting title}, {day and time}**
**{company}**: {about}
LightRay's main point: {main_point}
Questions to ask:
1. {question}
2. {question}
LightRay's report of {as_of}: {link}
```

- Up to three questions, in the report's order: each `questions_to_ask` item's `question`.
- Drop any line, or heading, whose field the result does not have. With no `as_of`, the last line is `LightRay's report: {link}`. With no meeting details (the user named the companies), start each block at the company's line.
- A report still being built: `LightRay's report on {company} is being built and needs about {minutes} more: {link}`, with `minutes_left` written as "9 minutes" or "1 minute".
- A company the user chose not to check: its meeting line and its name only.
- Two meetings with one company: one report, shown under both.

## Rules

- LightRay's words stay LightRay's. Quote `about`, `main_point` and the questions as they are; do not reword them, boil them down to a judgement or add a reading of your own. Use only the headings in the block above.
- Say nothing about a company that is not in its LightRay report: no web search, no memory, no guess. Asked for more, quote more of the same report (`findings`, `next_steps`) with its link. Where it has no answer: "No answer from LightRay's report to that question. The full report is at its link."
- What is not known is not said. Leave out any field the result does not have, with no line saying it is missing, unknown or not found. Never say a company has no cases, no findings or nothing on record, and never call it clean, safe or clear.
- Judge nothing: add no levels, marks or labels of your own, no word of danger or alarm, and nothing on how sure anyone is.
- Under "Check these next", only the companies in `worth_a_look_next`: never a person.
- Sentence case and plain words. No em or en dashes, no capitals for emphasis, nothing dramatic.
