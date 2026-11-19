# Work Orders

A single page that creates the same Skimmer work order for a whole list of
customers at once — gift deliveries, openings, closings. It is for the office,
not for billing: the Billing app does not write to Skimmer and should not.

`index.html` is the whole thing. No build, no server, no dependencies. Open it
and it runs.

## What is in this repository

Only the page. **No customer list, no API key, nothing about anybody.** The
customer list is a file Ben exports from the Billing app and each person loads
from their own computer; the key is typed in once and kept in that browser.
Neither ever comes near this repository, which is why it is safe for the page
to be published.

The real protection is that the customer list is not in this folder at all —
it lives next door in `hp-workorders-data/`, so there is nothing here to drag
by mistake. The `.gitignore` is a second line, and only a real one if you ever
push with `git`; GitHub's web uploader ignores it.

## How it works

1. You paste the Skimmer API key once. It is stored in your browser only.
2. You choose a work order type, a date, a tech and the instructions.
3. You choose who, one of two ways:
   - **A whole group** — by service day and customer type, from the Billing
     export. "Every Tuesday customer", "all the commercial pools".
   - **Just the people you pick** — type a name and click it, or paste a list
     of names, one per line. This works for a single customer, and for anyone
     who is not in the Billing list at all. Picking someone settles exactly
     which Skimmer customer is meant, so it also sorts out the two namesakes
     that a name alone cannot.
4. **Check the list.** This only reads. It shows exactly what would be created
   and why anyone is being skipped.
5. Create. One at a time, paced, with a receipt at the end.

## Two rules it will not bend

**A write is never sent twice.** Skimmer has no idempotency key and no delete,
so a work order created by a retry is a duplicate nobody can remove. If a send
gets no answer, the page *reads Skimmer back* to find out what happened and
tells you; it never sends again. A `429` is the single exception, because a
refused request was never processed in the first place.

**The duplicate check uses a season, not a day.** Last year's gift deliveries
went out over five dates, so checking only the date you typed would let a
second batch double up on the first. Checking "ever" is just as wrong — 171
pools carry two gift deliveries, which are simply last year's and this year's.
It looks 120 days either side, which covers gifts, closings and openings
without reaching the season before.

Both are measured, not assumed. The evidence is in the Billing app's
`SKIMMER_API_HANDOFF.md` §4.5.

## Names

The page matches the Billing app's customer names to Skimmer's by name, with
the Billing app's own matching rules. On the November 2026 data, 691 of 705
match outright. The rest are almost always a house that changed hands, and
Skimmer records that in the name — *"CHANCE LOWE (Formerly Bob & Amy Dunn)"*,
*"ANDRES BUR sold Nov 2026"*. The page therefore **suggests and never decides**:
it offers the likely customers, shows which are inactive, and leaves the row
unticked until a person picks one. Matching those automatically would send a
gift to a house Heschmeyer no longer services.

## Tests

    node test/check.mjs     # the helpers, and the retry policy
    node test/plan.mjs      # the planning and creating, against a fake Skimmer

No dependencies; plain Node. `test/plan.mjs` covers the two rules above
directly, including a write whose answer is lost after it landed.
