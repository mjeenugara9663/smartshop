# SmartShop production identity & tenant-isolation plan

The v0.3.1 browser preview is not a secure identity system and contains no real OTP. Production implementation should be an independently reviewed server-side project, not a patch to localStorage.

## Architecture

- Frontend: public catalog pages display only approved public `shops`, `products`, and unexpired `offers`. `GET /api/public/shops/{slug}` must not serialize merchant phone/password records, pending changes, admin events or private shops.
- Identity: send OTP to merchant phone number using a verified SMS service. Limit start/verify per phone, IP and device. OTP must be random, short lived, single use, stored hashed server-side; never return OTP in API payloads or logs. Account creation occurs only after the server verifies phone possession.
- Sessions: issue a revocable, secure, HttpOnly, SameSite cookie, CSRF-protect writes, and use HTTPS throughout. Never trust client-supplied ownerId or role.
- Data: `merchants(id, phone_verified, phone_hash/encrypted, status)`, `shops(id, owner_id, slug UNIQUE, status, visibility)`, `products(id, shop_id, ... approval_status)`, `offers(id, shop_id, expires_at)`, `change_requests(id, owner_id, shop_id, payload, status)`, `owner_daily_quota(owner_id, date_ist, published_count)` and `audit_events`. Access must be scoped by authenticated subject on EVERY read/write.
- Enforce `WHERE owner_id = authenticated_user.id` and `shop_id` ownership checks in the API, not by filtering a database response in the browser. Prevent IDOR on one shop against another, including detail endpoints, image assets, proposal approvals, exports and share URLs.
- Admin: separate server-authorized admin role, review actions, immutable audit records and step-up authentication. No admin secrets in the browser.
- Quota: transactionally enforce one successful approved publish per merchant per Asia/Kolkata date, including store visibility changes; previews, rejections and automatic offer expiry do not count. Concurrent approvals must not bypass it.
- Moderation: structured catalog fields only, restrict file types by actual content and scan uploaded product photos, screen multilingual names and pricing, prevent sensitive contact strings and prohibit scripted free-form outgoing messages. Human review/reporting/suspension remains needed.
- WhatsApp: server-approved shop phone destination only; generate click-to-chat URL with product/quantity and customer presses Send. Do not store customer phone, chats, messages or order history in SmartShop.
- Subscription: inactive flag until a future billing implementation; server-level entitlement checks, never a browser-only toggle.

## Security acceptance tests before merchant onboarding

1. Logged-out requests to any owner/admin endpoint return 401; attempts to enumerate other shop owners return 403/404 without leaked data.
2. Merchant A cannot read/write Merchant B draft listings, settings, store visibility or pending moderation requests, even with forged identifiers and a modified UI.
3. A private shop returns no catalog data on public API calls; public shop returns approved products only.
4. OTP resend throttles, expiration, single-use and lockout work on the server, independently of browser storage; sessions rotate and revoke on logout.
5. Two simultaneous approvals only consume one publish slot per merchant per IST day.
6. No customer PII, chat transcripts or purchase history is collected by the application.
7. Independent security review and negative tests pass before enabling merchant registration or real ordering.