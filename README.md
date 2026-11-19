# Vrisama SmartShop — Interactive prototype

**Version:** 0.3.1 — reviewed integration (browser-only development proof of concept)  
**Intended future domain:** `smartshop.vrisama.in` (not yet deployed)  
**Development repository:** `mjeenugara9663/smartshop` (keep changes on development branch until backend security)

## Run

Open `index.html` in a modern browser. There is no build step, external dependency or API key. To serve locally:

```bash
python3 -m http.server 8080
```

Then visit `http://localhost:8080`.

## Try the demo

1. Open **Explore stores** to see Raju General Stores and Lakshmi Fashion Boutique. Each has a distinct hash URL (`#/shop/raju-stores` and `#/shop/lakshmi-fashion`).
2. Add different product quantities and select **Review WhatsApp order**. The exact message contains **only catalog product names and quantities**. Without a configured destination number it is only a preview.
3. Open **My Websites**. You are asked to sign in with a mobile number and password (demo: Raju `9000000001` / `Raju@2026`, Lakshmi `9000000002` / `Lakshmi@2026`). See your proposed URLs and shop IDs, make a store public or private, preview it, or copy an example address. **Example addresses are not live.**
4. Use **Request another website** to submit a structured business name and category. Approve it in Admin control. The newly approved store receives a unique Shop ID and appears under the same owner. This consumes the same one-approved-change-per-day quota.
5. Open **Shopkeeper** (signed in), select one of your stores, and submit a product or expiry-based offer.
6. Open **Admin control**. Approve the submission. The product or offer becomes visible in that shop, and one successful publication consumes the shopkeeper's **one-change-per-IST-calendar-day** quota.
7. Submit a second change for the same shopkeeper and try approving it; the quota blocks the publication.
8. Look at the **Admin activity audit**. The view contains application events, not customers' private WhatsApp chats.
9. **Future plans** shows billing-ready ideas; no charge is collected.

## Explicit limitations — NOT FOR LIVE CUSTOMER USE

This is a **frontend simulation** using browser `localStorage`. It does **not** provide real authentication/OTP, multi-user concurrency, a database, durable centralized logging, image moderation, AI generation, server-side validation, or legally robust abuse prevention. The role selector, review queue and daily quota can be manipulated by a browser user. Product images uploaded for testing stay in that browser's local storage, and there is a small size limit. Avoid storing sensitive or real customer data.

The application **does not send WhatsApp messages itself**. A WhatsApp click-to-chat URL is available only after a demo business number is submitted and approved; the buyer must still press **Send** in WhatsApp. WhatsApp conversations are not visible to Vrisama. The displayed total is only an estimate; no payment is accepted.

The GitHub `smartshop` repository exists; a Cloudflare Workers demo was deployed earlier. This reviewed package is NOT a secure, verified OTP system and must not replace a production application. `smartshop.vrisama.in` is a *proposed* URL, not a live claim.

## Production architecture and controls

- Shared, centrally hosted storefront template; each merchant has a stable user ID and each store has its own ID and slug.
- Real merchant OTP/passwordless login via managed authentication; server-side store ownership checks.
- D1/another managed database for shop/catalog/offer metadata; object storage for product images; deployment on commercial-friendly hosting.
- Server-authoritative 1 approved change / owner / IST day using transaction or atomic counter. Preview and failed submissions don't count. Automatic expiration doesn't count.
- Moderation of text, images and prohibited categories, plus admin-only approval and rate limits. Restrict product names, media types, destination changes and generated outbound messages. A simple keyword filter is **not** enough to detect coded communications.
- Append-only server audit trail with retention/permissions; record who submitted, approved, and published, and what was changed; avoid collecting WhatsApp chat content.
- Explicit privacy and acceptable-use policies; human review, reporting, suspension and removal processes. The tool must not be represented as guaranteed abuse-proof.
- Restrict AI to bounded catalog and offer-generation functions. Do not execute model-generated arbitrary code or merchant-supplied HTML. Store AI credentials only on the server and track usage.
- Subscription and quota schema can be added later, with billing inactive until pilot validation.
- For WhatsApp, construct a predefined product-and-quantity-only message. Merchant-controlled catalog names still require moderation because they could encode messages.

## Repo and deployment

Maintain this source in the existing `mjeenugara9663/smartshop` repository. Keep v0.3.1 on the development branch; only promote after backend access control and OTP integration have been verified. Connect `smartshop.vrisama.in` through the DNS provider only after security review. Do not publish as a real commercial platform yet.

### Mobile prototype

A manifest, offline app-shell service worker and install icons are included. A supported browser can install this as a PWA when served over HTTPS or `localhost`. Installing from a local `file://` URL will not enable the service worker. PWA installation behavior varies by platform.

## iPhone and local-file preview

Some iPhone HTML attachment previews block JavaScript. When this happens, the original `index.html` displays only the header because its storefront cards are added by JavaScript. We added a pre-rendered fallback to `index.html` and a **mobile-preview.html** file that shows both shops, 12 demo products, sample promotional banners and the WhatsApp ordering concept without needing JavaScript.

- Open `mobile-preview.html` for a reliable static, mobile-friendly design preview in iPhone Files or ChatGPT.
- Open `index.html` from an HTTP/HTTPS server in Safari or Chrome for the actual interactive local prototype (shop selection, cart, shopkeeper forms and admin review).
- The static preview does not place real orders and the demo is not a production deployment.

## My Websites — v0.2 additions

- Permanent **My Websites** navigation item and shortcut inside the Shopkeeper dashboard.
- Multiple storefronts can belong to the same User ID; switch the active shop before editing.
- Each storefront has a stable Shop ID and unique proposed URL (`smartshop.vrisama.in/<shop-slug>`). In the browser prototype these addresses are examples, **not live public pages**.
- A shopkeeper can request a new store using a constrained name and business category. An administrator must approve it; approval consumes the daily change allowance.
- **Preview** always works within the interactive demo. **Copy example URL** copies a future URL while clearly warning it isn't live; **Share demo** is only offered when the app is served from a publicly hosted HTTPS origin, and shares the actual hash-based demo URL.
- The iPhone static `mobile-preview.html` also includes a non-interactive My Websites section.
- Production needs server-side authentication, URL uniqueness enforcement, verified ownership, moderation, DNS/routing and actual deployment. The demo is *not* a working public storefront generator.

## v0.2.1 fixes

- Product and shop names in Hindi, Kannada, Tamil, Telugu and Malayalam are now accepted (vowel signs were previously rejected).
- A shop name with no English letters gets a fallback URL (`shop`, `shop-2`, ...) instead of being refused.
- Restricted-item filter expanded: common Indian and English names for narcotics, alcohol, tobacco, weapons, replicas/"first copy", plus local-script terms and spaced spellings such as "G a n j a".
- Phone numbers written with spaces (8+ digits anywhere in a name) are blocked.
- Knife, pesticide and poison are no longer blocked, so hardware, kitchen and farm-supply shops can list them.
- This is still a keyword list in the browser. Production still needs server-side moderation, AI/human review and reporting.

## v0.3 — Shop owner sign-in and private stores

- **Phone + password sign-in** replaces the "Act as" demo switch. My Websites and Shopkeeper require sign-in; the sidebar shows **Shop owner sign in** / **Log out**.
- **Create account:** name, 10-digit Indian mobile number (+91 or leading 0 accepted), password (8–64 characters with a letter and a number), and two different security questions. One account per mobile number.
- **Forgot password:** enter the mobile number, answer both security questions (not case-sensitive, extra spaces ignored), choose a new password.
- **Lockout:** 5 wrong passwords, or 5 wrong security-answer attempts, lock the account for 15 minutes.
- **Hashing:** passwords and security answers are salted and hashed with PBKDF2-SHA256 (150,000 rounds, Web Crypto) before saving. Plain passwords are never stored.
- **Store visibility:** each store is **Public** or **Private**. A private store is hidden from Explore and its link shows "This store is private" to everyone except its signed-in owner. Owners switch it from My Websites. Newly approved stores start private.
- Demo accounts: Raju `9000000001` / `Raju@2026` (answers: Govt School, Tommy); Lakshmi `9000000002` / `Lakshmi@2026` (answers: Mysuru, Dosa).
- Data key changed to `vrisama.smartshop.demo.v3`, so earlier demo data in the browser is not reused.

**Still not real security.** Accounts, hashes and the session live in this browser's storage, so anyone with the device or browser dev tools can read, reset or bypass them. Admin control is still open to anyone for evaluation. Production needs server-side accounts, OTP verification of the phone number, server-enforced lockout and rate limits, and HTTPS. Security questions are a weak recovery method on their own; OTP to the registered number should replace or back them.

## v0.3.1 — merged review and safety constraints

**Important: v0.3 does NOT have an OTP login.** It uses phone number + password and security questions entirely inside browser localStorage. PBKDF2 hashing does not authenticate possession of the phone number, and localStorage/sessionStorage can be tampered with. It is not safe for real customers or shopkeepers.

Carry-forward corrections from v0.2.2:

- Hindi, Kannada, Tamil, Telugu and Malayalam input retained; restricted terms now use token-aware matching to reduce innocent false positives such as `மதுரை கடை`, and catch spaced-out or hyphenated restricted terms.
- Ordinary names such as Kitchen Knife, Pesticide spray and Rat Poison remain accepted by demo name validation. Other retail or regulatory constraints are not enforced.
- Product duplicate checks apply to the **selected** shop both on submission and approval, with locale-aware name comparisons.
- Offer product references are checked against the currently selected shop.
- Offer expiry scheduling rechecks at most once every 24 hours, avoiding browser timer overflow for long-running offers.

Additional demo-only safeguards:

- Hosted HTTPS preview is **read-only**: real merchant registration/recovery and mutation handlers are disabled, the admin console is unavailable and approval queues cannot be published. Demo login accepts only the two designated, publicly known fake phone numbers.
- Visibility changes now become approval requests, and each approved visibility change consumes the owner-wide one-change-per-IST-day allowance. Newly created stores still begin private.
- Approval validates request-owner/shop ownership and rejects duplicate products again.
- Private demo stores cannot produce a shareable link or WhatsApp order, and nonowners do not see their store screens in the ordinary UI. Client-side controls are **not a real privacy boundary**.
- Updated service-worker cache for v0.3.1 and refreshed the prototype warning labels.

**Production gate / not yet implemented:** SMS OTP challenge and verification from a trusted backend provider; owner identities and store records in a server-side database (e.g. Cloudflare D1); httpOnly secure cookies or verified server sessions; authorization on every API call; admin role verified server-side; cross-tenant isolation tests, image moderation, transaction-safe quotas, account recovery, audit logging and rate limits. Users and merchant phone numbers must never be trusted merely because they appear in frontend JavaScript. Do not enable merchant creation or billing until these are complete.

**Privacy:** No customer accounts, orders database, payment gateway or direct WhatsApp Cloud API is introduced. Only approved product name and quantity should be placed into the prefilled click-to-chat message, then the customer chooses to send. Client-side validation cannot guarantee avoidance of coded messages; merchant content must be moderated on the server.