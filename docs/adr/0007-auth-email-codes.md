# ADR-0007: Auth with email login codes, sessions in our own Postgres

Status: Accepted · 2026-10-12

## Context
Multi-user app (ADR-0001), hosted on Cloudflare Workers free plan (ADR-0009). Free Workers get 10 ms of CPU per request. Password hashing is designed to be CPU-heavy and can exceed that. The app is an installed PWA, where email links often open in a different browser than the app.

## Decision
- **Better Auth** library running inside our API. Users and sessions are stored in our Postgres.
- **Login with a 6-digit email code.** No passwords. Google login can be added later.
- Sessions use **httpOnly, Secure, SameSite cookies** on the same domain as the app. Long-lived, so users rarely log in.
- Emails are sent through a transactional email provider with a free tier (Resend). Limits to be checked in step 7.

## Options rejected
- **Passwords:** CPU risk on free Workers, plus password resets to build and passwords to protect.
- **Magic links:** break in installed PWAs (link opens in the browser, not the app).
- **Hosted auth (Clerk, Supabase Auth):** user data outside our DB and per-user pricing later.

## Consequences
- No password storage means no password leaks and no reset flow.
- Login depends on email delivery. If email is down, new logins fail (existing sessions keep working).
- We own auth security updates (library upgrades).
- **Risk to verify in step 7:** Better Auth running on Workers with our DB driver. Fallback: a small own email-code flow using the same tables.
