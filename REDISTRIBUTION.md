# Redistribution

## Ours: CC BY 4.0

The authors' own outputs are licensed CC BY 4.0 (LICENSE): forecasts,
scouting reports, introspected and extracted premises (including the second
introspection samples), matching criteria and the recorded reranker scores in
`provenance/`. Our question ids are slugs of the Metaculus titles and are
covered by the next section, not by CC BY 4.0.

## Metaculus content

Metaculus content is proprietary; its terms allow no redistribution with
attribution only and carve out access through their API.

What ships, for each of the 15 Metaculus questions: its Metaculus post id
(`datasets/metaculus/metaculus_post_ids.json`), our question id (a slug of
the title) and our recorded title (in `datasets/metaculus/questions.json`,
and quoted in the `question` field of our forecast files). The titles are the Metaculus titles. They
ship on the basis that the paper prints them and that they are needed to
identify the questions.

What does not ship: everything else Metaculus owns, namely the question text
(description, resolution criteria, fine print), the resolution and the
community forecast. These are marked pending written permission from
Metaculus (legal@metaculus.com). `datasets/metaculus/metaculus_snapshot/` is
the slot for them, currently a placeholder README, filled only if Metaculus
gives permission.

Readers can query the API themselves with
`datasets/metaculus/fetch_metaculus.py`, which reads what a reader's account
token is shown. For resolved questions, which all of ours are, that is ids,
titles, times, status and tournament, and not the question text, resolution
or community forecast.

Verification needs none of the Metaculus content.
