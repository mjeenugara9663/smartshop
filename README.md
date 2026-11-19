# Vrisama SmartShop — Interactive prototype

**Version:** 0.2 / browser-only proof of concept  
**Intended future domain:** `smartshop.vrisama.in` (not yet deployed)  
**GitHub repository:** `mjeenugara9663/smartshop` (created)

## Run

Open `index.html` in a modern browser. There is no build step, external dependency or API key. To serve locally:

```bash
python3 -m http.server 8080
```

Then visit `http://localhost:8080`.

## Try the demo

1. Open **Explore stores** to see Raju General Stores and Lakshmi Fashion Boutique. Each has a distinct hash URL (`#/shop/raju-stores` and `#/shop/lakshmi-fashion`).
2. Add different product quantities and select **Review WhatsApp order**. The exact message contains **only catalog product names and quantities**. Without a configured destination number it is only a preview.
3. Open **My Websites** and switch between demo owners to see their unique proposed URLs and shop IDs. Preview their sites, copy an example address, or, on a publicly hosted demo, share the exact demo preview URL. **Example addresses are not live.**
4. Use **Request another website** to submit a structured business name and category. Approve it in Admin control. The newly approved store receives a unique Shop ID and appears under the same owner. This consumes the same one-approved-change-per-day quota.
5. Open **Shopkeeper**, select an owned store, and submit a product or expiry-based offer.
6. Open **Admin control**. Approve the submission. The product or offer becomes visible in that shop, and one successful publication consumes the shopkeeper's **one-change-per-IST-calendar-day** quota.
7. Submit a second change for the same shopkeeper and try approving it; the quota blocks the publication.
8. Look at the **Admin activity audit**. The view contains application events, not customers' private WhatsApp chats.
9. **Future plans** shows billing-ready ideas; no charge is collected.

## Explicit limitations — NOT FOR LIVE CUSTOMER USE

This is a **frontend simulation** using browser `localStorage`. It does **not** provide real authentication/OTP, multi-user concurrency, a database, durable centralized logging, image moderation, AI generation, server-side validation, or legally robust abuse prevention. The role selector, review queue and daily quota can be manipulated by a browser user. Product images uploaded for testing stay in that browser's local storage, and there is a small size limit. Avoid storing sensitive or real customer data.

The application **does not send WhatsApp messages itself**. A WhatsApp click-to-chat URL is available only after a demo business number is submitted and approved; the buyer must still press **Send** in WhatsApp. WhatsApp conversations are not visible to Vrisama. The displayed total is only an estimate; no payment is accepted.

The GitHub repository exists and contains the public demo source. No Cloudflare deployment, DNS record, account-level API credentials or live commercial application has been created. `smartshop.vrisama.in` is a *proposed* URL, not a live claim.

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

The separate `smartshop` repository now contains this frontend demonstration. Deploy with a hosting provider compatible with commercial use only after testing and completing the necessary safety controls. Connect `smartshop.vrisama.in` through the DNS provider only after security review. Do not publish as a real commercial platform yet.

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