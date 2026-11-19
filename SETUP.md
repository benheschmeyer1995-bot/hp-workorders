# Putting this on GitHub Pages

Once. After that, changing the tool means uploading one file again.

## 1. A second Skimmer API key

Skimmer → **Integrations Center → API Keys → Generate**. Make a *second* key
for this tool and leave the existing one alone — the nightly Billing import
uses that one, and you want to be able to revoke Emily's access without
stopping your own billing.

Copy it somewhere safe when it appears; Skimmer masks it afterwards.

## 2. A new repository

github.com → **New repository**.

- Owner: `benheschmeyer1995-bot`
- Name: `hp-workorders`
- **Public** (GitHub Pages is free only on public repositories)
- Do not add a README — there is one to upload

A separate repository, not `hp-billing-connect`: that site is the one Intuit
looks at while production approval is pending, and it should stay exactly as
they saw it.

Public is safe here because the repository holds no data and no key. Anyone
could read the page; nobody could do anything with it without a Skimmer key.

## 3. Upload

On the new repository: **Add file → Upload files**, drag in

- `index.html`
- `README.md`
- `SETUP.md`
- the `test` folder (optional — it is only for checking the code)

then **Commit changes**. Same way you uploaded the connect pages.

**Never upload `customers.json`.** It is the customer list. It is kept in
`hp-workorders-data/` next door, so there is nothing in this folder to drag by
mistake — which is the only protection that actually works here, because
GitHub's web uploader ignores `.gitignore`.

## 4. Turn Pages on

Repository **Settings → Pages**.

- Source: **Deploy from a branch**
- Branch: **main**, folder: **/ (root)**
- **Save**

Give it a minute. The address will be

    https://benheschmeyer1995-bot.github.io/hp-workorders/

## 5. What Emily needs

Three things, and the first two are not in the repository on purpose:

1. **The key** from step 1. Send it the way you would a password, not in a
   chat message that sits around.
2. **`customers.json`** — the customer list from the Billing app. Get it from
   Billing: **Setup → Your data → Download the customer list for the Work
   Orders tool**. It holds names, service days and customer types, nothing
   else. She opens the page, presses *Load the customer-day list*, and picks
   the file; her browser remembers it. She only does this once, and again
   whenever you send a fresh one because the days have changed.

   She does not strictly need it — without it she can still pick customers by
   name, one at a time or pasted in a list. It is what makes "every Tuesday
   customer" possible.
3. **The address** from step 4.

She pastes the key once and presses Connect. That is the whole setup.

## Changing the tool later

Edit `index.html`, run `node test/check.mjs` and `node test/plan.mjs`, then
upload `index.html` again on the repository (**Add file → Upload files**,
drop it in, commit). GitHub replaces it. Anyone with the page open gets the
new version when they reload.
