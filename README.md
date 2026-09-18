# VELMORA — GitHub Pages Ready Storefront

A lightweight, mobile-first single-page e-commerce prototype built with HTML, CSS and vanilla JavaScript.

## Files

- `index.html` — storefront shell and metadata
- `style.css` — responsive premium design system
- `data.js` — default data + localStorage data layer
- `app.js` — storefront, product detail, cart and checkout adapter
- `admin.js` — PIN dashboard and management tools
- `assets/favicon.svg` — starter favicon

## Run locally

Open `index.html` in a browser. For best results, use a simple local HTTP server.

## GitHub Pages deployment

1. Create a new GitHub repository.
2. Upload **all files and the `assets` folder**, keeping the same structure.
3. Open **Settings → Pages**.
4. Under Build and deployment, select **Deploy from a branch**.
5. Select your main branch and `/ (root)`.
6. Save and open the generated Pages URL.

No build command is required.

## Admin

The storefront contains a small `Store admin` control in the footer. The prototype PIN is:

`2468`

Change it from **Admin → Settings**.

**Security warning:** this is deliberately a prototype. The PIN is stored client-side, so it is NOT secure authentication. Before real commerce, replace it with a proper authentication service/backend.

## Prototype storage

Products, settings, FAQs, cart and prototype orders are stored in the browser's localStorage.

That means:
- Data is local to the browser/device.
- It is not a shared cloud database.
- It is not secure production storage.
- Clearing browser storage can remove the data.

Use **Admin → Backup → Export Store Data** regularly while prototyping.

## Real payments

The frontend does not contain secret payment keys.

The dashboard's Payment section accepts:
- Razorpay
- PayPal
- Stripe
- Custom gateway
- Public/key identifier where appropriate
- Secure checkout endpoint
- Online payment ON/OFF
- COD ON/OFF

For actual payment processing, connect a secure serverless/backend endpoint. The frontend sends the order to that endpoint; the backend should create/verify the payment session using the gateway's secret credentials.

For GitHub Pages, suitable backend choices include a separate serverless/API service such as Cloudflare Workers, Netlify Functions, Vercel Functions, Supabase Edge Functions, or another secure backend. The static GitHub Pages site should never contain secret keys.

## COD

COD can be toggled in Admin → Payment. The UI explicitly states that COD availability depends on destination and fulfillment partner. Enabling the toggle alone does not create international COD fulfillment.

## Supplier privacy

Supplier fields exist in the admin data structure and are not rendered in public product cards or product detail views. Remember that localStorage itself is not secure; production supplier data should live on a server/backend.

## Product catalogue

There is no hardcoded 10/15 product limit. The dashboard adds products to the `products` array, so the prototype can hold many products subject to browser storage and practical performance limits.

For 500+ products in production, move catalogue data to a real database/API and paginate or progressively load the public catalogue.

## Themes

Ready-made themes:
1. Minimal Premium
2. Midnight Luxe
3. Soft Sage
4. Warm Sand
5. Modern Rose

Manual controls are available for colours, card radius, button style and font.

## Image licensing

Demo images are remote Unsplash URLs used as generic placeholders. Replace them with product images you have permission to use before commercial launch.

## Production checklist

- Replace client-side PIN with secure authentication.
- Move products/orders/customers to a secure database.
- Move supplier/private fields to the backend.
- Add a secure payment endpoint and gateway verification/webhooks.
- Validate prices and inventory server-side.
- Add server-side order IDs and fraud/abuse controls.
- Add a real shipping/fulfillment integration.
- Add proper privacy/terms/returns content for the countries you sell into.
- Configure a custom domain and production SEO/social image.
