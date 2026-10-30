---
name: first-look
description: Take a first look at a company the user is about to take on as a client, counterparty or supplier, from its LightRay report. Use when the user asks for a first look at, or background on, a new client, engagement, deal or supplier.
---

# First look at a new client

A first look at one company from its LightRay report: the company's line, LightRay's main point, the questions to ask, the documents to ask for, and the link. LightRay writes the report. Your part: reuse a report the user already has, ask before a new one, and pass on LightRay's words.

## 1. Name the company

- Take the company's name and its city or country from the user's words, as *Company name, City*. When the place is missing and the company is new to the user, ask for it with the question in step 3, and go on without it if they do not know.
- Only the company's name and place go to LightRay, never a person's name: LightRay checks companies, not people.
- If the user names several companies, ask about the new ones together (step 3) and write one first look for each.

## 2. Reuse a report the user has

Call LightRay's `list_reports` with `limit` 20. If the company's report there is `ready` or `building`, reading it uses none of the user's reports: call `get_report` with its `report_id` and go to step 5. Count a report as the same company only when the names match apart from case and legal form (Ltd, Limited, LLC, Inc); if unsure, ask.

If LightRay's tools are not available, ask the user to connect LightRay on the plugin's Connectors tab, and stop. If LightRay asks them to sign in, wait for them, then go on.

## 3. Ask before a new report

For one company, ask in these words, with its name and place:

> Check {Company name, City}? It uses one of your LightRay reports, and a new report takes about 12 minutes.

For several, list them and end with:

> Each new company uses one of your LightRay reports. A new report takes about 12 minutes. Which should I check?

Call `check_company` (`name`, and `place` when known) only for a company the user says yes to. Any other new company, one the report suggests or one the user mentions later, is asked about first in the same way. If no one can reply, as in a scheduled run, start a report only when the request itself names that company.

## 4. Act on what LightRay returns

Each result has a `status`:

- `ready`: the report. Write the first look.
- `building`: the report is on its way. Give `minutes_left` and the `link`. Call `get_report` for it again only when the user asks, or after `check_again_minutes` at the earliest; never in a quick loop.
- `choose`: several companies match. Show the `choices` (name, place, registration number where given), ask which one, then call `check_company` with the same `name` and that `choice` token.
- `several`: the words named more than one company. Ask which to check; each is a new report.
- `limit` or `paused`: pass on LightRay's `message`, and start no more reports.
- Any other status: pass on LightRay's `message`.

Pass on a message as LightRay wrote it, speaking to the user ("your" for "the reader's"), adding nothing.

## 5. Write the first look

```
**{company}**: {about}
LightRay's main point: {main_point}
Questions to ask:
1. {question}
2. {question}
3. {question}
Before you proceed, obtain:
- {document}
LightRay's report of {as_of}: {link}
```

- Up to three questions, in the report's order: each `questions_to_ask` item's `question`.
- The documents: the `step` of each `next_steps` item whose `kind` is `document`, in the report's order.
- Drop any line, or heading, whose field the result does not have. With no `as_of`, the last line is `LightRay's report: {link}`.
- A report still being built: `LightRay's report on {company} is being built and needs about {minutes} more: {link}`, with `minutes_left` written as "9 minutes" or "1 minute".

## Rules

- LightRay's words stay LightRay's. Quote `about`, `main_point`, the questions and the documents as they are; do not reword them, boil them down to a judgement or add a reading of your own. Use only the headings in the block above.
- Say nothing about the company that is not in its LightRay report: no web search, no memory, no guess. Asked for more, quote more of the same report (`findings`, `next_steps`) with its link. Where it has no answer: "No answer from LightRay's report to that question. The full report is at its link."
- What is not known is not said. Leave out any field the result does not have, with no line saying it is missing, unknown or not found. Never say a company has no cases, no findings or nothing on record, and never call it clean, safe or clear.
- Judge nothing: add no levels, marks or labels of your own, no word of danger or alarm, and nothing on how sure anyone is.
- Under "Check these next", only the companies in `worth_a_look_next`: never a person.
- Sentence case and plain words. No em or en dashes, no capitals for emphasis, nothing dramatic.
