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
17. [Checklist](#17-checklist)
18. [What Claude should never do for you](#18-what-claude-should-never-do-for-you)

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
- **Know the host's limits before launch week.** Our host's free plan gave a monthly allowance of build credits. We used it up redeploying many times in the first days, and a production deploy came back "Forbidden" with a product fix waiting to go out. The human upgraded the plan and it worked within the hour. The lesson is to read the plan limits before launch, batch deploys, and know that anything urgent (for example, a revoked-licence list) also needs a deploy path.
- **Facts-only copy.** After rebuilding the site more than once, we settled on plain fact lists, spec tables and real screenshots, not marketing sentences. No clipped slogans.
- **Heads-review before the human sees it.** Before any page reaches the founder, have a second pass (a different session) check numbers, spellings and claims.
- **Deploy only on the human's yes,** and keep a dated `.bak` of whatever you replace.

## 5. Legal pages

You need, at minimum: **terms of sale, a privacy policy, and an end-user licence agreement (EULA)** for each product, plus the third-party notices your dependencies require.

- **Claude drafts, a human or a lawyer reviews.** Claude writes good first drafts and is very good at consistency checks (do the terms, the EULA and the checkout page agree on the refund window, the trial length and the company name?). It is not your lawyer, and a draft is not a policy until someone accountable has read it.
- **Facts must match the product.** Our site and EULA once disagreed after a product decision changed what a free edition included. After any pricing or scope decision, have Claude search every page, EULA, email and in-app string for the old wording.
- **Third-party notices:** one file per product listing every bundled library and its licence. Claude can generate it from the build; the human spot-checks it.
- **Link the legal pages from the checkout and the installer,** not only the footer.
- **Do not click "accept" for the human** on any terms or consent screen (section 18).

## 6. Payments

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

## 13. Ads

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

## 17. Checklist

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
- [ ] Tax position for the countries you sell to decided and written down; registrations submitted where needed.
- [ ] Ads use real, current screenshots; budget and publish done by the human.
- [ ] Nightly backup running, one restore tested, failure notification works.
- [ ] Reports refresh before they are collected; decisions queue exists.
- [ ] Windows, if promised: CI green, and no date announced before it is.

## 18. What Claude should never do for you

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
