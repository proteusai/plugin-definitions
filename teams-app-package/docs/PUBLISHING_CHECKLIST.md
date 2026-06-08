# Microsoft Teams Store Publishing Checklist — OnBuddy

This checklist tracks Microsoft's requirements for publishing OnBuddy to the Microsoft Teams Store (via Partner Center).

Reference: https://learn.microsoft.com/en-us/microsoftteams/platform/concepts/deploy-and-publish/appsource/publish

---

## 1. App Package Files

- [x] `manifest.json` present at the root of the zip
- [x] Color icon `color.png` (192x192 PNG, RGBA)
- [x] Outline icon `outline.png` is 32x32 PNG (transparent background, white/monochrome)
- [x] Files zipped at archive root (no subfolder)
- [x] Package size under 5 MB

## 2. Manifest Schema & Identity

- [x] `$schema` points to a supported Teams schema (v1.16)
- [x] `manifestVersion` matches schema (`1.16`)
- [x] `version` follows semver (`1.0.0`)
- [x] `id` is a valid GUID (`1fd2e369-22d9-46d3-a6e1-499b9860193a`)
- [x] `packageName` uses reverse-domain notation (`ai.onbuddy.bot`)

## 3. Developer Information

- [x] `developer.name` set (`OnBuddy`)
- [x] `developer.websiteUrl` provided (`https://onbuddy.ai`)
- [x] `developer.privacyUrl` provided (`https://onbuddy.ai/privacy-policy`)
- [x] `developer.termsOfUseUrl` provided (`https://onbuddy.ai/terms-of-use`)
- [x] Privacy policy URL is publicly accessible
- [x] Terms of use URL is publicly accessible
- [x] Website URL is publicly accessible
- [x] Privacy policy and Terms of use specifically cover the Teams integration (not just a generic site policy) — verify wording
- [ ] Developer/publisher name in manifest matches the verified publisher name in Partner Center — **publisher name will be `Wale Ayandiran`; manifest currently shows `OnBuddy`, so either update the manifest's `developer.name` to `Wale Ayandiran` or register Partner Center under `OnBuddy`**

## 4. Name & Description

- [x] `name.short` is ≤ 30 characters (`OnBuddy` — 7 chars)
- [x] `name.full` is ≤ 100 characters (`OnBuddy AI Assistant` — 20 chars)
- [x] `description.short` is ≤ 80 characters (44 chars)
- [x] `description.full` is ≤ 4000 characters (~400 chars)
- [x] Description does not mention competing platforms (Slack, Google Chat, etc.)
- [x] Description clearly states what value the app delivers and any prerequisites (added "Requires an OnBuddy account" to `description.full`)
- [x] No marketing fluff, no all-caps, no unnecessary emojis

## 5. Bot Configuration

- [x] `bots[].botId` registered with Azure Bot Service / Microsoft Bot Framework
- [x] `bots[].scopes` declared (`personal`, `team`, `groupchat`)
- [x] `commandLists` provided per scope
- [x] `help` command implemented
- [x] Bot replies to unrecognized commands with a helpful fallback — `teams.service.ts:528+` intercepts declared commands, falls through to Proteus AI for free-text, sends friendly fallback strings when AI is unconfigured
- [x] Welcome message sent on first install — `teams.service.ts:645` handles `installationUpdate` with `action === 'add'` and greets the user
- [x] Bot handles signout / disconnect — N/A for in-chat: connection is tenant-level via admin OAuth in the web app (`teams.controller.ts:73` `teamsDisconnectHandler`), no per-user bot OAuth to sign out of. **Add a note for Microsoft reviewers explaining this in the Partner Center test instructions.**
- [x] Bot does not spam users with unsolicited proactive messages — `sendTeamsNotification` only fires on real events (task assignment, status change); bot responds conversationally otherwise
- [x] Messaging extensions are declared only if implemented (none declared — OK)

## 6. Permissions, Domains, Security

- [x] `permissions` declared (`identity`, `messageTeamMembers`)
- [x] `validDomains` lists only required domains
- [x] No localhost, ngrok, or dev URLs anywhere in the manifest — `validDomains` now uses publisher-owned hosts: `api-prd.onbuddy.ai` (API), `workspace.onbuddy.ai` (dashboard), `onbuddy.ai` (website), plus the required Microsoft hosts (`token.botframework.com`, `login.microsoftonline.com`)
- [x] All declared domains are owned/controlled by the publisher — `*.onbuddy.ai` is owned by Wale Ayandiran
- [x] Bot endpoint uses HTTPS with a valid certificate — `https://api-prd.onbuddy.ai` has a valid TLS certificate
- [x] Tokens (Bot Framework, Graph) are stored securely server-side, never logged — verified: `teams.service.ts:259-260` encrypts access/refresh tokens via `encrypt()` before DB write; decrypted only on use (`teams.service.ts:573`); no logger calls include token values

## 7. Icons (Detailed)

- [x] Color icon: 192x192 PNG, full color, transparent background (purple disc with toucan mascot)
- [x] Outline icon: 32x32 PNG, transparent background, white symbol only
- [x] Both icons render correctly on dark and light Teams themes — color icon's purple disc gives self-contrast on any background; outline icon is white-on-transparent, visible in Teams' dark app rail
- [x] No copyrighted/trademarked imagery used without permission — confirm the OnBuddy toucan mascot is original artwork (or licensed for commercial use)

## 8. Partner Center Store Listing Assets

(These are uploaded to Partner Center separately from the manifest zip.)

- [ ] Store logo (large): 300x300 PNG
- [ ] Store logo (small): 44x44 PNG
- [ ] At least 1 screenshot (max 5): 1366x768 PNG showing real product UI
- [ ] Hero image (optional but recommended): 1920x1080 PNG
- [ ] Video link (optional): YouTube or Vimeo demo
- [ ] Short summary (≤ 100 chars)
- [ ] Long description (≤ 4000 chars, can use light formatting)
- [ ] Search keywords (up to 3)
- [ ] App categories chosen (e.g., "Productivity", "Human resources")
- [ ] Supported languages declared
- [ ] Support contact: email and/or URL
- [ ] Pricing tier set (free / paid / trial)

## 9. Functional / UX Requirements

- [ ] App works in every scope it declares (personal, team, groupchat)
- [ ] First-run experience explains what the app does and how to connect an account
- [ ] Sign-in flow uses OAuth (Microsoft sign-in) — no passwords entered in chat
- [ ] Sign-out / disconnect option available from the bot
- [ ] Errors surface as friendly bot messages, never raw stack traces or HTTP codes
- [ ] No broken links inside cards or replies
- [ ] App functions without requiring additional downloads or external tools

## 10. Testing

- [ ] Tested on Teams desktop (Windows + macOS)
- [ ] Tested on Teams web client
- [ ] Tested on Teams mobile (iOS + Android)
- [ ] Tested in personal scope
- [ ] Tested in team scope (added to a channel)
- [ ] Tested in group chat scope
- [ ] Tested install + uninstall + reinstall flow
- [ ] Tested with a brand-new tenant (no pre-existing OnBuddy data)
- [ ] Passes the Teams App Validator: `npx @microsoft/teams-manifest-validator <manifest.json>` or via Developer Portal
- [ ] Passes the Teams Toolkit "Validate" step (if using Toolkit)

## 11. Compliance & Policies

- [ ] Reviewed Microsoft Teams Store validation guidelines: https://learn.microsoft.com/en-us/microsoftteams/platform/concepts/deploy-and-publish/appsource/prepare/teams-store-validation-guidelines
- [ ] Reviewed Microsoft commercial marketplace policies: https://learn.microsoft.com/en-us/legal/marketplace/certification-policies
- [ ] Privacy policy covers data collection, retention, and third-party sharing
- [ ] GDPR compliance documented if serving EU users
- [ ] Data handling disclosed (where user messages are stored, AI training, etc.)
- [ ] Accessibility: cards/messages readable by screen readers, sufficient color contrast
- [ ] No PII logged in plaintext

## 12. Microsoft 365 Certification (Optional, Strongly Recommended)

- [ ] Enrolled in the Microsoft 365 App Compliance Program (Publisher Attestation → Microsoft 365 Certification)
- [ ] Security questionnaire completed
- [ ] Penetration test report submitted (for full certification)

## 13. Partner Center Submission

Teams apps are published through the **Microsoft commercial marketplace** in Partner Center. End-to-end flow: create & verify a Partner Center account → complete tax/payout → create the offer → upload the app package → fill the marketplace listing → set availability → add reviewer notes → submit for certification. Enrolling and listing a **free** app costs nothing.

> Do these in order — later steps are blocked until account verification and the tax/payout profiles are complete.

### 13.1 Create the Partner Center account

- [x] Sign in at https://partner.microsoft.com/dashboard with the Microsoft account that should **own the publisher** (a work/Entra ID account is recommended over a personal one)
- [x] Enroll in the marketplace program: Partner Center → **Settings → Account settings → Programs** → *Commercial marketplace* → **Get started** (free)
- [x] Set the **publisher display name** — ⚠️ buyers see this and it must match `manifest.json` → `developer.name`. See **Blocker #2**: choose `OnBuddy` *or* `Wale Ayandiran` and make the manifest and the Partner Center publisher identical **before** submitting

### 13.2 Verify the publisher account

- [x] Complete the account-vetting form Microsoft sends after enrollment (legal entity vs. individual, registered address, contact)
- [x] Provide the verification identifier:
  - **Company / registered business:** a **D-U-N-S number** — request free at https://www.dnb.com/duns-number.html (allow up to ~5 business days)
  - **Individual / sole trader:** government-ID identity verification instead of D-U-N-S (Partner Center prompts the correct path based on the account type you picked)
- [x] Wait for **Verified** status under Account settings (vetting typically takes a few business days)

### 13.3 Complete tax & payout profiles

- [x] Partner Center → Settings → Account settings → **Payout account** — add bank details (required for transactable offers; complete it even for a free app to avoid a publish block)
- [x] Partner Center → Settings → Account settings → **Tax profile** — submit the tax form for your country (W-8/W-9 equivalent). This is **mandatory even for free apps**

### 13.4 Create the offer

- [x] Partner Center → **Marketplace offers** → **+ New offer** → **Microsoft 365 and Copilot apps** (this is the offer type that covers Teams Store apps; older UI may label it "Office Store / Microsoft 365")
- [x] Set the **Offer ID** (internal, permanent) and **Offer alias** (internal name only)
- [x] In Offer setup, choose **upload the Teams app package** and upload the validated `teams-app-package.zip` — Partner Center reads identity, bot ID, and scopes straight from the manifest

### 13.5 Properties & marketplace listing

- [x] **Categories:** pick up to 3 (recommended: *Productivity*, *Human resources*)
- [x] **Legal:** privacy `https://onbuddy.ai/privacy-policy` and terms `https://onbuddy.ai/terms-of-use` — must be live and match the manifest
- [ ] **Listing copy & assets** (from Section 8):
  - Name, **short summary ≤ 100 chars**, **long description ≤ 4000 chars** (light formatting allowed)
  - **Search keywords** — up to 3 (e.g. `onboarding`, `HR`, `assistant`)
  - **Logos:** 300×300 and 44×44 PNG
  - **Screenshots:** 1–5 at 1366×768 PNG — use the three designed banners (*answers back → a step ahead → just say it*)
  - **Hero image:** 1920×1080 PNG (optional, recommended)
  - **Video:** YouTube/Vimeo demo link (optional)
  - **Support contact:** support email and/or URL; **supported languages**

### 13.6 Availability & pricing

- [ ] **Markets:** select the regions to list in (or all)
- [ ] **Pricing:** set the plan — for OnBuddy choose **Free** (no transactability setup needed)
- [ ] **Preview audience** (optional): add tenant IDs allowed to see the offer pre-public for a final internal sign-off

### 13.7 Reviewer test instructions & notes

- [ ] Paste the block from *"Reviewer notes to include in the submission"* (below) into the offer's **Notes for certification / test instructions** field
- [ ] Fill the placeholders first: real **demo OnBuddy email/password**, the **connected demo tenant name**, and re-confirm the first-run steps match production
- [ ] Confirm the Azure Bot Service messaging endpoint is `https://api-prd.onbuddy.ai/api/integrations/teams/webhook` (**Blocker #3**) — validators message the bot live during review
- [ ] Provide demo tenant credentials if validators need their own connected tenant

### 13.8 Validate & submit

- [ ] Clear the Teams App Validator (Developer Portal → Apps → *Validate*, or `npx @microsoft/teams-manifest-validator teams-app-package/manifest.json`) — see Section 10
- [ ] In the offer, click **Review and publish** — Partner Center runs automated checks, then a Microsoft certification engineer does a manual review
- [ ] Expected turnaround **~3–10 business days**; track the offer status and the account email, then see Section 14 for post-submission follow-up

### Reviewer notes to include in the submission

Microsoft validators will look for things that, in OnBuddy's design, are intentionally handled outside the bot. Include the following text in the Partner Center "Notes for certification" / "Test instructions" field so the app is not flagged for missing flows:

> **Authentication model**
> OnBuddy uses tenant-level admin OAuth, not per-user bot sign-in. A tenant admin connects Microsoft Teams once from the OnBuddy web app (Settings → Integrations → Microsoft Teams → Connect). Individual users do not authenticate inside the bot — they are recognised automatically by their Teams identity once the tenant is connected.
>
> **Disconnect / signout**
> Because there is no per-user bot OAuth, the bot does not implement an in-chat `signout` or `disconnect` command. To disconnect the entire tenant, an admin goes to the OnBuddy web app → Integrations → Microsoft Teams → Disconnect. This removes the stored tokens and stops all proactive notifications.
>
> **Test account**
> A demo OnBuddy organisation is available at `https://onbuddy.ai` with the following credentials: `<add demo email / password before submitting>`. The tenant `<add demo tenant name>` is already connected to this organisation so validators can install the Teams app and immediately chat with the bot.
>
> **First-run flow**
> 1. Install the Teams app (personal scope).
> 2. The bot sends a welcome message.
> 3. Type `help`, `my tasks`, or `status` — each returns a deterministic Adaptive Card.
> 4. Type any free-text question — the bot forwards to the OnBuddy AI assistant and replies in-channel.
>
> **Proactive notifications**
> Notifications (e.g. "A task has been assigned to you") are sent only in response to real events inside the OnBuddy product, never unsolicited. A reviewer can trigger one by assigning a task to the demo user from the OnBuddy web app.

## 14. Post-Submission

- [ ] Monitor Partner Center for validation feedback (typical turnaround: 3–10 business days)
- [ ] Address any rejection reasons promptly
- [ ] Plan for ongoing support and updates (manifest version bumps require re-submission)

---

## Summary of Blockers

The following items will cause **automatic rejection** and must be fixed first:

1. ~~Outline icon size — currently 192x192, must be 32x32~~ ✅ Fixed (now 32x32).
2. **Publisher name mismatch** — Partner Center will verify the publisher as `Wale Ayandiran`, but the manifest's `developer.name` is `OnBuddy`. Decide which name is canonical and align both.
3. ~~Bot endpoint domain — `onbuddy-api.porty.rest`~~ ✅ Fixed: `validDomains` now uses `api-prd.onbuddy.ai` + `workspace.onbuddy.ai` + `onbuddy.ai`. **Confirm the Azure Bot Service messaging endpoint in the Azure Portal also points to `https://api-prd.onbuddy.ai/api/integrations/teams/webhook`.**
4. **Store listing assets** (screenshots, descriptions, logos at 300x300 + 44x44) not yet prepared.
5. **Partner Center account** must be set up and publisher-verified before submission.

Once those are addressed, the manifest itself is in good shape for review.