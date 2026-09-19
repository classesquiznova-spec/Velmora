# VELMORA secure production setup

This build changes the prototype architecture: the public site no longer contains the admin PIN, supplier/private order data, or payment secrets. Production persistence/auth/payment are server-side.

## Required hosting
Deploy on **Netlify** (or another host that supports the included serverless functions). Do not deploy only as GitHub Pages if you need the secure backend/payment functions.

## 1. Supabase
Create a Supabase project and run `supabase/schema.sql` in SQL Editor.

The database tables are private. Do not create anon/public policies for these tables.

## 2. Environment variables in Netlify
Set:
- `SUPABASE_URL` = your Supabase project URL
- `SUPABASE_SERVICE_ROLE_KEY` = Supabase service-role key (server only; never put this in HTML/JS)
- `AUTH_SECRET` = random 32+ byte secret
- `ADMIN_INITIAL_PASSWORD` = a strong temporary admin password (12+ characters). The first successful login creates the server-side admin record; change it immediately in Admin → Settings.
- `APP_ENCRYPTION_KEY` = base64 encoding of a random 32-byte key
- `RAZORPAY_KEY_ID` = optional fallback; dashboard public key is preferred
- `RAZORPAY_KEY_SECRET` = optional fallback; dashboard encrypted secret is preferred
- `RAZORPAY_WEBHOOK_SECRET` = optional fallback; dashboard encrypted webhook secret is preferred

Generate strong values locally, e.g. with Node:
`node -e "const c=require('crypto'); console.log('AUTH_SECRET='+c.randomBytes(32).toString('base64url')); console.log('APP_ENCRYPTION_KEY='+c.randomBytes(32).toString('base64')); console.log('ADMIN_PASSWORD_HASH='+c.scryptSync('CHANGE_ME',c.randomBytes(16),32,{N:16384,r:8,p:1,maxmem:67108864}).toString('base64'))"`

You do not need to manually insert the admin hash if `ADMIN_INITIAL_PASSWORD` is configured. The first successful login creates the admin record. The included `tools/setup-admin.mjs` is available if you prefer to create it manually.

## 3. Store data
The original demo products are still available in `data.js` only as safe public defaults. For production, load your catalogue into the database via the admin dashboard or a one-time migration. Supplier fields must only be entered through the authenticated admin dashboard.

## 4. Admin URL
The admin UI is intentionally not linked from the public site. Current private path:
`/velmora-control-7x4k9/`

This path is not the security boundary. The HttpOnly session cookie + server-side password verification is the security boundary.

## 5. Change password immediately
Log in using the initial admin password you created in Supabase. Admin → Settings now changes the server-side password; the browser never stores the password.

## 6. Razorpay
In Admin → Payment enter the Razorpay key ID, secret key and webhook secret. Secrets are encrypted before database storage and never returned to the browser. Checkout creates the Razorpay order on the server. The webhook verifies Razorpay's signature before marking an order paid.

Configure your Razorpay webhook URL to:
`https://YOUR-DOMAIN/.netlify/functions/razorpay-webhook`

Subscribe to the payment/order events you use, especially captured and failed payment events.

## 7. Security behavior
- Admin session: HttpOnly + Secure + SameSite=Strict cookie, 1-hour expiry.
- Password: scrypt hash, not plaintext.
- Secrets: AES-256-GCM encrypted at rest using `APP_ENCRYPTION_KEY`.
- Database: Supabase service role is server-only.
- Public API strips admin, profit, orders, payment secrets and supplier/private fields.
- Checkout validates product IDs, published status and server-side prices before creating a payment order.
- Order IDs are server-generated UUID-derived IDs.
- Razorpay webhook signature is verified server-side.
- Public admin trigger is removed.
- Security headers and CSP are included in `netlify.toml`.

## Important
No software can promise absolute security. Keep Netlify, Supabase and Razorpay accounts protected with strong passwords and 2FA, rotate secrets if exposed, and keep dependencies/platforms updated.
