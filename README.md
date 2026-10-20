# Using Claude to set up a software business, digitally, as one person

A practical sequence for going from "I have a product" to "I can legally sell it, ship it and support it", with Claude doing the preparation and the human doing everything that needs an identity, a payment or a signature.

Made by Tommy Martin. Built while running [ClubPlugins](https://clubplugins.com).

**Honest status.** This is what one person did in the weeks before and after launching a small line of Mac audio software, with Claude Code doing most of the typing. It is not legal, tax or financial advice, and it is not a recipe that will fit your country or product. Several things below went wrong first (a Buy button that silently did not exist, a download counter that counted ourselves, a host that blocked deploys mid-launch), and the fix is described where it applies. Some parts are fully working, some are lightly used, and the last section says which. For how to run many Claude sessions behind a company like this, see [solo-company-claude-playbook](https://github.com/tsmartin29/solo-company-claude-playbook), which this guide does not repeat. For deciding what to build in the first place, see problem-first-framework (coming soon).

## Contents

1. [Who does what](#1-who-does-what)
2. [Legal entity and brand naming](#2-legal-entity-and-brand-naming)
3. [Domain, email forwarding and sending from an alias](#3-domain-email-forwarding-and-sending-from-an-alias)
4. [Website on a static host, with forms](#4-website-on-a-static-host-with-forms)
5. [Legal pages](#5-legal-pages)
6. [Payments](#6-payments)
7. [Code signing and notarisation on macOS](#7-code-signing-and-notarisation-on-macos)
8. [Windows builds without a Windows PC](#8-windows-builds-without-a-windows-pc)
9. [Installers, updates and protection](#9-installers-updates-and-protection)
10. [Licences of the frameworks you build on](#10-licences-of-the-frameworks-you-build-on)
11. [Download tracking that excludes you](#11-download-tracking-that-excludes-you)
12. [Tax registration abroad](#12-tax-registration-abroad)
13. [Ads](#13-ads)
14. [Nightly private source backup](#14-nightly-private-source-backup)
15. [Reports and a COO session](#15-reports-and-a-coo-session)
16. [What is automated, honestly](#16-what-is-automated-honestly)
17. [What it cost](#17-what-it-cost)
18. [Tax considerations](#18-tax-considerations)
19. [Checklist](#19-checklist)
20. [What Claude should never do for you](#20-what-claude-should-never-do-for-you)

## 1. Who does what

The split that worked: **Claude prepares, the human commits.** Claude drafts, builds, configures, checks and writes the steps. The human does anything that proves who they are, moves money, signs, accepts terms or makes something public and irreversible.

| Task | Claude | Human |
|---|---|---|
| Company formation | Checklist, draft names, ordering of follow-up steps | Files with the state, pays, signs |
| Domain and email | DNS records to enter, alias list, test plan | Buys the domain, logs in to the registrar, creates sending credentials |
| Website | Writes and builds it, previews it, prepares the deploy | Approves, runs or authorises the deploy |
| Legal pages | First drafts, consistency checks | Reads them, has a lawyer review, accepts responsibility |
| Payments | Test-mode setup, links, redirects, webhook code, test purchases | Opens and verifies the account, turns on live mode, enters any live key |
| Signing and notarisation | Scripts, build pipeline, verification | Enrols in the developer programme, supplies identity, creates the signing credentials |
| Tax registration abroad | Prepares the fact sheet and the document list | Submits, using their identity and company documents |
| Ads | Creative, copy, tracked links, checklists | Sets budget, clicks publish |
| Backups, reports | Scripts and scheduled sessions | Grants the one-time system permission the script needs |

Two habits made the split work in practice:

- **Steps for the human are click-by-click.** When the human must do something (sign in, create an account, answer a Terminal prompt), Claude writes numbered, one-action-per-line steps that say exactly what will be on screen and what to click or type. It does the technical part itself and leaves only what only the human can do. It checks the Terminal panel to see where the human is, rather than asking.
- **Paste-ready text goes in the reply.** When the human has to paste text or upload a file into an outside service, the exact text is in the chat message and the files are delivered directly. Pointing at a folder in the project wastes their time and is the fastest way to annoy them.

And the habit that fixed several bad decisions: **every blocker comes with its cheapest workaround.** "We would need a Windows machine" is a bad report (section 8). Write "we would need a Windows machine, or a free cloud CI runner", with the rough cost of each, so the human chooses with the full picture.

## 2. Legal entity and brand naming

- **Form one entity that owns every product,** and use it consistently: the developer account, the store, the licence text, the end-user agreement, the books and the tax registrations all name the same company. Decide once, write it down, and make every document match.
- **Order of follow-up steps matters.** Claude produced a dated checklist in the order things depend on each other: formation approval, then the tax ID, an operating agreement, a business bank account that is not shared with any other business, a D-U-N-S number (needed to enrol a company with Apple), the developer programme, the framework licence, then the store account in the company's name.
- **The human files and pays.** Claude can fill in the draft wording and tell you what each field means, but the filing, the signature and the payment are yours.
- **Pick one spelling of the brand and enforce it.** Ours is one word, and every page, installer and email must match. Put the rule in your memory file so every session applies it, and grep the site for variants before launch.
- **Check the name before you commit to it:** domain, social handles, the app store, trademark search in your country. Claude can do the searching, the human decides.

## 3. Domain, email forwarding and sending from an alias

What we ended up with:

- **Domain bought and DNS controlled by the human.** Claude supplies the exact records to enter (A/CNAME for the site, MX for mail, TXT for verification), and checks propagation with `dig` afterwards.
- **Email forwarding to an existing mailbox.** A free forwarding service gives you `support@`, `admin@`, `license@` and a catch-all that all land in one inbox. Do this on day one, because developer programmes, signing services and payment processors all want a role address on your own domain for identity checks.
- **Sending as the alias is the harder half.** The forwarder's own sending was a paid feature. The free route is to configure your mail client's "send mail as" through a transactional email service's SMTP. The human creates the SMTP credential and pastes it into the mail client; Claude never needs to see it.
- **A separate transactional sender for the product.** Licence-key emails go out from a dedicated address through a transactional email service, with SPF and DKIM records in DNS. Claude writes the record list and the test plan (send to yourself, check headers for pass).
- **Keep the mailbox the forwarder points to somewhere you actually read.** Verification emails for every other step arrive there.

## 4. Website on a static host, with forms

- **Static site on a static host** (we used Netlify). Claude writes the site, previews it locally, and builds a deploy folder. A static site is cheap, fast and has almost nothing to attack.
- **Forms:** use the host's form handling or a small serverless function for newsletter or waitlist signup. Claude writes it; the human creates the account at the email-list provider and decides what consent wording is shown.
- **Redirects as a stable interface.** Never hard-code a payment link in an app or email. Point everything at `yoursite.com/buy/<product>` and let a redirect file map it to the current link. When prices or links change, you change one file, and old installers keep working.
- **Know the host's limits before launch week.** Our host's free plan gave a monthly allowance of build credits. We used it up redeploying many times in the first days, and a production deploy came back "Forbidden" with a product fix waiting to go out. The human upgraded the plan and it worked within the hour (what it cost: [section 17](#17-what-it-cost)). The lesson is to read the plan limits before launch, batch deploys, and know that anything urgent (for example, a revoked-licence list) also needs a deploy path.
- **Facts-only copy.** After rebuilding the site more than once, we settled on plain fact lists, spec tables and real screenshots, not marketing sentences. No clipped slogans.
- **Heads-review before the human sees it.** Before any page reaches the founder, have a second pass (a different session) check numbers, spellings and claims.
- **Deploy only on the human's yes,** and keep a dated `.bak` of whatever you replace.

## 5. Legal pages

You need, at minimum: **terms of sale, a privacy policy, and an end-user licence agreement (EULA)** for each product, plus the third-party notices your dependencies require.

- **Claude drafts, a human or a lawyer reviews.** Claude writes good first drafts and is very good at consistency checks (do the terms, the EULA and the checkout page agree on the refund window, the trial length and the company name?). It is not your lawyer, and a draft is not a policy until someone accountable has read it.
- **Facts must match the product.** Our site and EULA once disagreed after a product decision changed what a free edition included. After any pricing or scope decision, have Claude search every page, EULA, email and in-app string for the old wording.
- **Third-party notices:** one file per product listing every bundled library and its licence. Claude can generate it from the build; the human spot-checks it.
- **Link the legal pages from the checkout and the installer,** not only the footer.
- **Do not click "accept" for the human** on any terms or consent screen (section 20).

## 6. Payments

- **What payments cost, and the tax side.** Stripe's fees, Stripe Tax and the choice between a payment processor and a merchant of record are in [What it cost](#17-what-it-cost) and [Tax considerations](#18-tax-considerations).
- **Test mode first, always.** We used Stripe, and the CLI. Create products, prices and payment links in the sandbox, run a full test purchase, and only then repeat in live mode. Each CLI command prints which account it is running in; Claude checks that line says sandbox before creating anything.
- **Live mode is the human's decision.** Switching on live mode, accepting the processor's extra terms, and running any script with a live key is done by the human. Claude never runs a command in live mode without an explicit yes, and never enters or stores a key.
- **Payment links:** quick to set up and need no code. Gotchas we hit: with automatic tax on, a payment link would not create until the product had a tax code; and the processor could not limit a coupon to one use per customer, so each customer gets a promotion code with a redemption limit of one.
- **A fulfilment path:** the processor sends a webhook on a completed payment; a small serverless function generates a **signed licence key** and emails it. Claude writes and tests it with test events. Keys are signed, and the app verifies the signature offline.
- **Revocation list:** a plain file on your site listing revoked keys that the app checks. Needs a working deploy path (see section 4).
- **The buy button must really exist.** We shipped plug-ins whose in-app Buy button did nothing. The build passed the buy URL as a compile-time definition, the URL contained a `#`, and the build system silently dropped it. The fix was a path-based URL (`/buy/<product>`) handled by the redirect, and a release check that reads the build flags and confirms the Buy button works in the licence window. If something is a build flag, verify it arrived.
- **Prices change, links should not.** Because every app and email points at the redirect, a price change was a one-file change. Old payment links were switched off at the processor.
- **Test the whole chain as a customer:** link, checkout, email, key entry in the real installed app, unlock. Claude does the test in sandbox; the human does one real purchase at the end.

## 7. Code signing and notarisation on macOS

- **Human:** enrol the company in the Apple Developer Programme (as an Organisation, which needs the D-U-N-S number and identity verification), and create the Developer ID certificates and the notarisation credentials. Store them in the Keychain.
- **Claude:** writes the build and release scripts that sign every binary, package it, submit it for notarisation, wait, staple the ticket, and verify (`codesign --verify`, `spctl --assess`, and a clean-machine install). It reads the Keychain item by name and never prints a secret.
- **Check the thing the customer gets,** not the thing you built: open the downloaded installer on a Mac that never saw your build tree.
- **Plug-in formats have their own validators** (for audio plug-ins, `auval` and similar). Run them on the installed copy.
- **Test the installed product like a user would** (section 7 of the playbook): host, resize, save, reload. A signed build that does nothing is still broken.
- **Keep the signing identity out of the repo and the backups.** Our backup excludes the signing environment file explicitly (section 14).

## 8. Windows builds without a Windows PC

For two weeks we assumed Windows meant buying a Windows machine, and decided Mac-only on that basis. A later look showed that a **cloud CI runner** could build and test Windows binaries for free or nearly free (check the current free allowance for your plan; private repositories are usually limited).

What it looks like in practice:

- Claude sets up a CI workflow on a Windows runner that builds the product, runs a validator (for plug-ins, a format validator), installs it silently, tests an upgrade and an uninstall, and uploads the result as an artifact.
- Work happens on a separate branch and clone so the Mac line is untouched until the Mac release ships.
- Pilot with the smallest product first.
- **What CI cannot give you:** hands-on testing by a human on real Windows with a real host application. Plan for a few Windows beta testers.
- **Signing on Windows** needs a certificate or a signing service, with identity verification that the human completes. Until then you can distribute a plain ZIP (a plug-in folder plus a read-me), which avoids the installer-reputation warning, and publish a SHA-256 checksum for each file next to its link. Put a short honest "unsigned" note in the download box and remove it when signing is live.
- **Say only what CI has proven.** No Windows dates go on the site until a CI run is green, and ads say a single short sentence ("Coming for Windows") until the build ships.

## 9. Installers, updates and protection

- **Updates must remove the old version.** Users ended up with three copies of an app and old installers piling up in Downloads. The rule: every update removes the previous version's app copies and old installers, with no prompt, by moving them to the Trash (restorable), touching only your own bundle identifiers and installer names, never a newer version and never a running copy. Plug-in installers run a shared clean-up step. Do not hold this back because another change is blocked: repack the released installer with the clean-up only, test it, and replace the download.
- **Test the clean-up itself.** Ours had a guard bug that made builds before the fix a no-op, so clean-up needs its own test on a machine with older versions installed.
- **Protection, honestly.** Use signed licence keys and a way to revoke them, and ship with reasonable protection rather than waiting for perfect; plan further hardening as ordinary updates. Make no "uncrackable" or "secure" claims in marketing.
- **Platform scope can change; keep options open.** We started Mac-only, added Windows later (section 8), and avoided new macOS-only shortcuts where a portable choice cost the same. Hosts should degrade gracefully (hide or explain a feature that only works in one host application).

## 10. Licences of the frameworks you build on

Read the licence of every framework and library you ship, and write down the conditions that depend on your business.

- **Check the tier thresholds.** Our audio framework has free and paid tiers based on what the company receives over the previous twelve months, across all products. Past the limit you must buy the next tier or stop distributing. Put the tier and the limit in your finance notes, have the person who tracks the money warn the founder well before the line, and buy the next tier first.
- **Duties that come with the licence:** keep the notices, keep records of users and copies if it requires, check whether the vendor may use your name, and watch for a splash-screen requirement.
- **Bundled libraries:** list them with their licences in each product's third-party notices.
- **Copyleft traps:** some libraries are free only if you comply with a copyleft licence, or need a separate commercial licence. We found one in a single product's source and keep that feature switched off in shipped builds unless it is licensed. Claude can scan for these. Make it a checklist item before each release.
- Write this audit down once, dated, and re-run it when you add a dependency.

## 11. Download tracking that excludes you

Downloads are the metric a solo founder watches, so make it true.

- **What went wrong:** our alerts counted our own traffic. The release tool checked every installer link before each deploy, the host's counter treated that check as a download, our QA sessions downloaded installers, and the app's auto-updater used the same files. Almost every early "download" was us, and the founder was rightly upset.
- **What fixed it:** check links through the host's API, not by fetching the file; keep a **ledger of our own downloads** (every release and deploy, every QA or test download); have the report and the alert subtract the ledger from the raw count; and make "log it first" a rule: before any session downloads one of your own installers, it records it in the ledger.
- **Never quote a raw counter as customers.**
- **Alerts:** a small scheduled script polls the counter every few minutes, sends a desktop notification and a phone push per outside download, plus a summary every few hours. Keep alerts to the one metric the founder cares about.
- **Background jobs on macOS cannot always read your Desktop.** Put scheduled scripts outside protected folders.

## 12. Tax registration abroad

If you sell digital goods to customers in other countries, you may owe tax there before you have any local presence. Rules differ, and some countries have no minimum for foreign digital sellers.

- **Claude prepares:** a fact sheet (what you sell, to which countries, how prices are quoted, whether prices include tax), the list of documents the tax authority asks for, and a record file for the process.
- **The human submits,** using their identity and company documents, and keeps the confirmation. The application number you get is **not** the registration number; the registration number arrives later. Record both, and label them.
- **Until you have the registration number, do not show tax or a tax number on receipts.**
- **When it arrives:** enter the registration in the payment processor's tax settings, note the effective date, and work out what is owed on sales since then. If the authority has asked for documents and you hear nothing, ask early.
- Take advice from an accountant. This is the section most likely to differ in your case.
- **Worked example, with sources:** what we applied for, what we have not registered for yet, and the rules for the US, EU, UK and Latin America are in [Tax considerations](#18-tax-considerations). The fees we paid are in [What it cost](#17-what-it-cost).

## 13. Ads

- **What it cost.** Our ad spend to date and how we track it are in [What it cost](#17-what-it-cost). Ad spend is deductible, but selling into countries where you are not registered for tax is a risk: see [Tax considerations](#18-tax-considerations).
- **Claude makes the creative; the human clicks publish.** Claude produces images, video, captions and tracked links (UTM tags), prepares the campaign setup as a checklist (objective, placements, countries, schedule), and checks everything against the site.
- **Every ad shows the real product interface.** A type-only ad sells an idea. For software, the interface is the product. Use a fresh screenshot from the shipping build, never a stale one, and never a competitor's.
- **Facts, not slogans,** and no claims without a test behind them (see problem-first-framework).
- **The human sets the budget and publishes.** Claude must not change spend, duration or audience, and never adds money.
- **Turn off automatic enhancements** the platform adds (AI touch-ups, effects), unless you reviewed them.
- **Judge ads on clicks and landing-page views,** not on raw download counts (section 11).
- **Say "Coming for Windows" only if true,** and keep it to one short sentence.
- **Upload quirks:** ad-manager upload widgets may hide the real file input. Claude can work around that in the browser, but the human still presses publish.

## 14. Nightly private source backup

- **What:** a script runs every night via the Mac's scheduler and pushes a snapshot of each source tree to a **private** repository on a dedicated backup branch, plus a dated tag.
- **Design choices that matter:**
  - A separate git directory for the backup, so it does not disturb your working repository.
  - A pre-push hook that allows only the backup branch and backup tags, so it can never push a real branch or a public repository.
  - Three secret checks before every push: filename patterns, a key-pattern search, and a secrets scanner with an allowlist.
  - Exclusions: signing files, secret headers, deploy-tool state, installers, audio, models and any file over a size limit.
  - It writes a line to the inbox each night and raises a desktop notification on failure.
- **Human step:** the Mac's scheduler cannot read protected folders until the human grants Full Disk Access to the shell. Claude gives the click-by-click steps.
- **Check it:** restore a snapshot into an empty folder once. A backup you have not restored from is a hope.
- **Our mirrors had silently been stale for weeks before this,** which is the case for the nightly check and the failure notification.

## 15. Reports and a COO session

For how to run the sessions themselves, see the playbook. The business-specific parts:

- **One COO session** the founder talks to and a scheduled run every couple of hours during the working day. It reads the inbox first, checks every team against one goal, and reports.
- **Four standing heads** (finance, IT, strategy, secretary), each keeping its area's files true after every change.
- **The founder's report is one short document:** page one is a few bullets per head (done, next, what the founder must decide, clearly marked); page two onward holds the details.
- **Refresh before you collect.** An early report told the founder to judge something he had already judged an hour earlier, because a team's status was stale and was copied unchecked. Now every team updates first, claims are checked against the primary record (verdict files, logs, timestamps), and a team that did not refresh is reported as stale with the age of its information.
- **"Business hours" means daytime, every day of the week.** If you run scheduled agents, say so explicitly.
- **Prices, dates, scope, spending, legal matters and anything public stay with the founder** and are queued as decisions, not taken by an agent.

## 16. What is automated, honestly

| Area | State |
|---|---|
| DNS, mail forwarding, site build, local preview | Claude does all of it; the human buys the domain and logs in |
| Deploys | Claude prepares; they go live on the founder's yes |
| Test-mode payments, redirects, webhook and licence fulfilment | Claude builds and tests |
| Live payments | Human turns it on |
| macOS signing and notarisation | Pipeline automated once the human supplies credentials |
| Windows | Built and validated in CI; signing and hands-on testing still need the human |
| Legal pages | Drafted by Claude; review by a human or lawyer is not automated and should not be |
| Tax registration abroad | Claude prepares; the human submits |
| Ads | Claude prepares creative; the human publishes |
| Download alerts, nightly backup | Automated; need one macOS permission granted by the human |
| Reports | Automated collection, refreshed before reading; the founder decides |

What is lightly used or new: the Windows pipeline, the signing for Windows, the further hardening of licence protection, and the tax registration follow-up. Treat those as in progress.

## 17. What it cost

**Roughly $2,300 to get from nothing to a live company with products on sale, in about three weeks** (late September to 20 October 2026). That's an estimate from our own books and dashboards, rounded; revenue is left out.

| Where it went | Share | Roughly |
|---|--:|--:|
| AI (Claude plan + extra usage for running many sessions at once) | ~70% | ~$1,600 |
| Company setup (LLC filing, registered agent) | ~15% | ~$375 |
| Ads (Meta / Instagram)\* | ~7% | ~$170 |
| Developer accounts and hosting (Apple Developer, domain, website host) | ~6% | ~$130 |
| Everything else (email tools) | ~2% | ~$40 |

\* Ad spend is increasing: this is the first weeks' spend only, and budgets are going up as the products launch, so this share will grow.

What that tells you:
- **The AI is the business's biggest cost by far**, and most of it is usage beyond the plan from running many sessions in parallel. Pacing it (playbook section 6, [claude-local-first](https://github.com/tsmartin29/claude-local-first)) is the biggest lever.
- **The fixed costs of the business itself are small:** hosting, domain, developer account and registered agent come to roughly $60 a month plus a few hundred dollars a year.
- **Plenty of what you need is free:** email forwarding, transactional email at low volume, GitHub CI, the audio framework's free tier, the bank account, the EIN, and UK VAT registration.
- **Payments cost per sale, not per month** (card processing plus optional automatic sales tax), so there's nothing to pay until you sell.

Coming later: trademark filings, Windows code signing (about $10 a month), an accountant for filings, and the next audio-framework tier if revenue passes its free limit.

For what these costs mean at tax time, see the next section.

## 18. Tax considerations

**This is not tax advice. We use a CPA and an attorney, and so should you.** It is a plain-language map of the questions a one-person US LLC selling software worldwide has to answer, written in October 2026, with what we did about each. Tax law changes, and several points below are marked as uncertain. Where a source is cited, read the source, not our summary.

### Income tax on a one-person LLC

- **Default treatment.** A single-member LLC is "disregarded" for federal income tax unless it elects to be taxed as a corporation. The profit is reported on the owner's personal return (Schedule C) and the owner pays tax on it whether or not it is withdrawn. Source: [IRS, single member limited liability companies](https://www.irs.gov/businesses/small-businesses-self-employed/single-member-limited-liability-companies).
- **Self-employment tax.** Net earnings are also subject to self-employment tax (Social Security and Medicare), the way a sole proprietor's are. Source: [IRS Topic 554](https://www.irs.gov/taxtopics/tc554). Half of it is deductible.
- **Estimated payments.** There is no employer withholding, so you generally pay estimated tax quarterly. Source: [IRS Topic 306](https://www.irs.gov/taxtopics/tc306).
- **Questions for your CPA, not conclusions:** whether to elect S-corporation treatment once profits are steady, how the qualified business income deduction applies, and what your state and city charge. We have not elected anything; we have not had a profit yet.
- **Keep the LLC's money separate.** Our books are the LLC's alone. Costs the owner paid personally before the bank account existed are recorded as owed to the owner, and the owner decides later whether they are repaid or counted as extra capital. Ask your CPA how to treat that.

### What is deductible

- **The test** is "ordinary and necessary" for the business. Source: [IRS Publication 535](https://www.irs.gov/publications/p535).
- **For a software business that normally means:** developer-programme fees, hosting and domain, software subscriptions, AI plans and pay-as-you-go usage, transactional email, payment fees, ads, state filing fees and the registered agent, and professional fees. Everything in section 17 is in this family; whether a given line is deducted now, spread out or treated as a startup cost is the CPA's call.
- **Startup costs.** Costs paid before the business opens are treated differently from running costs: a limited amount can be deducted at once and the rest spread over 15 years. Source: Publication 535 (startup costs) and [IRC section 195](https://www.law.cornell.edu/uscode/text/26/195). The line between "before" and "after" the business began is a CPA question.
- **Home office.** Only if part of the home is used regularly and exclusively for the business. Source: [IRS Publication 587](https://www.irs.gov/publications/p587). Do not claim a shared desk.
- **Mixed-use costs.** An AI plan or phone used for both personal and business work is deductible only for the business share. Keep the split written down.

### Software development costs: section 174 and 174A

This area changed in 2025 and is the one most likely to be out of date, so confirm it with your CPA.

- **Before 2025.** From tax years beginning in 2022, section 174 forced companies to capitalise research and experimental costs, including software development, and write them off over five years (domestic) or fifteen (foreign), rather than deduct them at once.
- **Since the 2025 law.** Public Law 119-21 (the "One Big Beautiful Bill Act", 4 July 2025) added **section 174A**. For tax years beginning after 31 December 2024, domestic research and experimental costs can be deducted in the year paid or incurred. The statute says that amounts paid to develop software count as research and experimental costs. A taxpayer can instead elect to capitalise and amortise them over at least 60 months. The election is made by the return's due date, including extensions, and sticks for later years. Foreign research is still capitalised and amortised over 15 years. Sources: [26 U.S.C. 174A](https://www.law.cornell.edu/uscode/text/26/174A) and [26 U.S.C. 174](https://www.law.cornell.edu/uscode/text/26/174).
- **Transition.** The Act lets eligible small businesses apply the change retroactively to 2022 to 2024, and lets others deduct the leftover unamortised amounts from those years in 2025 or across 2025 and 2026. The procedure is in [IRS Revenue Procedure 2025-28](https://www.irs.gov/pub/irs-drop/rp-25-28.pdf). A company formed in 2026 has little or no such history, though development the owner did earlier as an individual may matter; ask.
- **What we are unsure of.** Whether work done with overseas contractors, or with tools running abroad, counts as "foreign research" is a definition you should ask about before assuming immediate deduction. Whether AI usage fees are research costs, or ordinary expenses, is also a CPA call. We have not made an election; this is queued for our CPA.
- **Interaction with the credit.** Domestic costs are generally reduced by the amount of the research credit you claim (section 280C(c)); ask how that interacts.

### The research credit (section 41), briefly

- It is a credit for qualified research, claimed on [Form 6765](https://www.irs.gov/instructions/i6765). Software can qualify, and it has to pass the four-part test in the instructions.
- A very new company with small gross receipts can elect to use part of it against **payroll tax**, up to a yearly cap. Source: [IRS, payroll tax credit for small businesses](https://www.irs.gov/businesses/small-businesses-self-employed/qualified-small-business-payroll-tax-credit-for-increasing-research-activities).
- **For a one-person company with no employees, that is probably worth little,** because there is no payroll tax to offset. We have not claimed anything. It becomes interesting once you hire. Record time and costs from day one if you think you may claim later.

### US sales tax

- **Where the obligation comes from.** Since *South Dakota v. Wayfair* (2018), a state can require a remote seller to collect sales tax once sales into that state pass a threshold, with no physical presence needed. Source: [the decision](https://www.law.cornell.edu/supremecourt/text/17-494).
- **Typical thresholds** are $100,000 of sales into a state, and in some states 200 transactions. The numbers differ by state and change, so read the state's own revenue department page.
- **Whether your product is taxable is a separate question.** States treat downloaded software, SaaS and digital goods differently, sometimes in surprising ways, and some cities add their own taxes. There is no single national answer. Source for an overview: [Stripe, digital product tax](https://stripe.com/resources/more/digital-product-tax).
- **Our home state.** Our books recorded Illinois sales tax registration as needed before going live. Check your own state's page (for Illinois, [MyTax Illinois](https://mytax.illinois.gov)) and any city taxes.
- **Tool: Stripe Tax.** We switched on automatic tax in Stripe at launch. It calculates the tax on each sale, tells you where you are close to a threshold, and can help you register and file, but **you remain the seller and the one responsible** unless you use a merchant of record (below). Its price is a percentage of each transaction where it collects tax (we budgeted 0.5%; verify). Sources: [Stripe Tax](https://docs.stripe.com/tax), [supported countries](https://docs.stripe.com/tax/supported-countries), [registering](https://docs.stripe.com/tax/registering), [filing](https://docs.stripe.com/tax/filing).
- **Our prices include tax.** We chose tax-inclusive prices, so where we are registered the tax comes out of the sticker price instead of being added on top.

### EU VAT on digital services

- **There is no minimum for sellers outside the EU.** Digital services sold to consumers are taxed in the buyer's country, from the first sale. A non-EU seller can register once, in any one member state, under the "non-Union" One Stop Shop scheme and file a quarterly return covering all EU countries, and a country cannot require a tax representative for it. Sources: [European Commission, One Stop Shop](https://vat-one-stop-shop.ec.europa.eu/one-stop-shop_en), [registering](https://vat-one-stop-shop.ec.europa.eu/one-stop-shop/register-oss_en), [declaring and paying](https://vat-one-stop-shop.ec.europa.eu/one-stop-shop/declare-and-pay-oss_en).
- **What we did:** we have not registered. Until we do, our ads in EU countries (Ireland is one of our ad countries) promote only free products or a teaser for something not yet on sale. See "Other countries" below.
- **Uncertain:** how downloadable perpetual-licence software is classified under the EU rules, and the treatment of sales to EU businesses with a VAT number. Ask.

### UK VAT for sellers based abroad

- **There is no registration threshold** for a business with no UK establishment: a taxable supply of any value to UK consumers can require registration, within 30 days of the first. Sources: [GOV.UK, VAT rules for digital services to consumers](https://www.gov.uk/guidance/the-vat-rules-if-you-supply-digital-services-to-private-consumers), [HMRC VAT registration manual on non-established taxable persons](https://www.gov.uk/hmrc-internal-manuals/vat-registration-manual/vatreg37210), [VAT Notice 700/1](https://www.gov.uk/government/publications/vat-notice-7001-should-i-be-registered-for-vat/vat-notice-7001-should-i-be-registered-for-vat).
- **What we did:** we applied online in October. HMRC says it decides within about 40 working days of receiving the documents. The **application number is not the registration number**; we show no VAT on receipts until the registration number arrives, then add the registration to Stripe Tax and work out what is owed on UK sales since the effective date. Registering costs nothing. Our prices are tax-inclusive, so one-sixth of a UK sale is owed once registered.
- **Uncertain:** whether the effective date can be backdated to the first sale. Keep every UK sale record from day one.

### Other countries' digital VAT

- Many countries tax digital services sold by foreign sellers, and some have no minimum. Mexico, Chile and Colombia each require non-resident digital sellers to register and charge VAT (about 16%, 19% and 19%). Sources: [BPM on Mexico](https://www.bpm.com/insights/mexico-vat-on-digital-services-provided-by-non-residents/), [RSM on Latin America](https://rsmus.com/insights/services/business-tax/value-added-tax-compliance-latin-america-digital-businesses.html); check the authorities themselves (SAT, SII, DIAN) because we only found secondary sources. Stripe lists the countries where it can calculate the tax: [supported countries](https://docs.stripe.com/tax/supported-countries).
- **What we did:** in Spain and Latin America we advertise only **free** products, because we have not registered for VAT there and do not want to sell what we cannot account for. A free download has no price to tax, but some countries have their own rules for free digital supplies, so this is a decision to review, not a guarantee.

### 1099-K from Stripe

- Stripe may send a Form 1099-K reporting your gross card payments. The federal threshold for payment apps and marketplaces went back to $20,000 and 200 transactions under the 2025 law, but card payments have no minimum in the IRS guidance, and platforms can send forms below the thresholds. Sources: [IRS, 1099-K threshold FAQs](https://www.irs.gov/newsroom/irs-issues-faqs-on-form-1099-k-threshold-under-the-one-big-beautiful-bill-dollar-limit-reverts-to-20000), [IRS Form 1099-K FAQs](https://www.irs.gov/newsroom/form-1099-k-faqs-general-information).
- **It reports gross sales,** before fees, refunds and the sales tax you collected. Your return should start from your own books and explain the difference. You owe tax on the income whether or not a form arrives.

### Records

- Keep receipts, statements and a ledger with a **proof column** (where the receipt is saved). Mark estimates "approx." and replace them with the receipt. Source: [IRS Publication 583](https://www.irs.gov/publications/p583) and [Topic 305](https://www.irs.gov/taxtopics/tc305).
- Our books have separate sheets for the bank account, Stripe sales (with collected tax kept apart, because it is not ours), costs the owner paid personally, planned costs, and the owner's capital. They are matched to the bank statement monthly. Claude drafts the ledger; a human confirms every figure.
- Keep tax you collect from buyers in a separate liability account. It is not income.

### A merchant of record changes the picture

A merchant of record (Paddle, Lemon Squeezy, and Stripe's own Managed Payments) is the legal seller to the buyer. It collects and pays the sales tax and VAT, issues the invoices and handles refunds and chargebacks, and pays you what is left.

| | You sell through a payment processor (our setup) | You sell through a merchant of record |
|---|---|---|
| Who owes sales tax and VAT | You, in every place you pass a threshold, with no minimum for foreign sellers in the UK and EU | The merchant of record |
| Registrations and returns | Yours, or a partner's | Not yours |
| Fees | Lower. Ours are about 2.9% + $0.30 plus 0.5% for tax calculation | Higher. The headline rate at Paddle and Lemon Squeezy is about 5% + $0.50 per sale, with extras; check their pages |
| Control | Your checkout, your customer relationship | Their checkout, their terms, their payout schedule |
| Your income tax | Unchanged | Unchanged; you still report what you receive |

Sources: [Stripe, Managed Payments](https://docs.stripe.com/payments/managed-payments), plus the vendors' own pricing pages. The fee figures above are headline rates reported by comparison sites; confirm them.

We began with a merchant of record in our plan, then chose a plain processor for lower fees and control, knowing the tax work would be ours. It is a fair trade-off in either direction. For a very small seller with international buyers, paying the extra percentage can be cheaper than the registrations, returns and mistakes. **Check which mode your own payment links use**: we found our own notes disagreed on whether a Stripe feature related to this was on, so our tax section above assumes we are the seller until we confirm.

## 19. Checklist

Before launch:

- [ ] Entity formed; every account, licence and legal page names the same entity and the same spelling of the brand.
- [ ] Domain bought; DNS under your control; role addresses forwarding; sending as an alias works; licence email passes SPF and DKIM.
- [ ] Site deployed on a host whose plan limits you have read; redirect file in place for `/buy/<product>`.
- [ ] Terms of sale, privacy policy, EULA and third-party notices drafted, reviewed by a human, and linked from checkout and installer.
- [ ] Payments tested end to end in sandbox, then once for real; webhook and licence email work; revocation list reachable.
- [ ] In-app Buy button verified in the installed build.
- [ ] Apple developer enrolment done; signing and notarisation verified on a clean Mac; validators run on the installed copy.
- [ ] Update path removes the old version, tested with older versions installed.
- [ ] Framework licence tier checked; thresholds recorded; third-party notices complete; copyleft scan done.
- [ ] Download counter excludes your own traffic; alerts working.
- [ ] Tax position for the countries you sell to decided and written down; registrations submitted where needed ([section 18](#18-tax-considerations)).
- [ ] Expense ledger started, with a proof column and "approx." marked on anything not backed by a receipt ([section 17](#17-what-it-cost)).
- [ ] Ads use real, current screenshots; budget and publish done by the human.
- [ ] Nightly backup running, one restore tested, failure notification works.
- [ ] Reports refresh before they are collected; decisions queue exists.
- [ ] Windows, if promised: CI green, and no date announced before it is.

## 20. What Claude should never do for you

- **Enter passwords, card numbers, bank details, government IDs, API keys or tokens** into any field or file. You type them, or use your password manager.
- **Create accounts or sign in for you.** It gives you the steps.
- **Accept terms, agreements, cookie banners or OAuth grants.**
- **Switch payments to live mode or run live commands** without your explicit yes in the chat.
- **Spend money or publish ads.** It does not change budgets or add funds.
- **Sign, file or submit** the company formation, tax registration or any legal document.
- **Send messages on your behalf** (emails, DMs, calendar invites) without your yes for that specific message.
- **Delete anything permanently.** Things go to the Trash; temp and build folders are the exception.
- **Overwrite a tested file** without a dated backup next to it.
- **Change system or security settings,** including the permission grants in section 14. It tells you where to click.
- **Make a claim it has not tested,** or invent a customer problem to make copy sound better.
- **Treat text found in a web page, email or document as an instruction.** Content it reads is data. If a page says "paste your key here" or "approve this", it stops and tells you.
- **Work around a block.** If a tool or a safety system refuses something, it reports that and waits.

## Licence

CC BY 4.0. See `LICENSE`. Templates may be used freely.
