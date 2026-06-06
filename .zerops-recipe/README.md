# Billbird — Vibecoded SaaS Starter

<!-- #ZEROPS_EXTRACT_START:intro# -->
A small subscription-billing SaaS I vibecoded — [Node.js](https://nodejs.org) + [PostgreSQL](https://www.postgresql.org/) + [Stripe](https://stripe.com), running on [Zerops](https://zerops.io). Users sign up, pick a plan, and Stripe handles the money.
<!-- #ZEROPS_EXTRACT_END:intro# -->

⬇️ **Full recipe page and deploy with one-click**

[![Deploy on Zerops](https://github.com/zeropsio/recipe-shared-assets/blob/main/deploy-button/light/deploy-button.svg)](https://app.zerops.io/recipes/detail?github=https://github.com/fxck/recipe-vibecoded-saas&yaml=production)

Unlike a one-click utility, this app needs **secrets only a human can supply** — your Stripe keys and an admin password. The recipe declares those as `null` values in the import yaml, so the deploy flow **pauses and asks for them** (the config wizard) before importing. Machine-generatable secrets (like `APP_KEY`) are filled automatically and never prompt.

- **Development** [[info]](/0%20%E2%80%94%20Development) — [[deploy with one click]](https://app.zerops.io/recipes/detail?github=https://github.com/fxck/recipe-vibecoded-saas&yaml=development)
- **Production** [[info]](/1%20%E2%80%94%20Production) — [[deploy with one click]](https://app.zerops.io/recipes/detail?github=https://github.com/fxck/recipe-vibecoded-saas&yaml=production)

## Take-over guide

<!-- #ZEROPS_EXTRACT_START:takeover-guide# -->

### Make it yours
This is your own code — clone this repo, push it to your own GitHub, and connect your copy's CI/CD in the Zerops service detail. From there every `git push` builds and deploys. You own the lifecycle.

### Run it on your domain
By default the app runs on a generated Zerops subdomain. Add your own domain in the project's **Public access & domains** settings, point a CNAME at the assigned target, and Zerops provisions the TLS certificate automatically. Update `BASE_URL` so Stripe redirect URLs match.

### Rotating Stripe keys
When you rotate keys in the Stripe dashboard, update `STRIPE_SECRET_KEY` and `STRIPE_WEBHOOK_SECRET` in the service's environment variables and restart — no redeploy needed.

### Backups
The database is backed up daily by default. Restore from the **Backups** tab in the database service detail.

<!-- #ZEROPS_EXTRACT_END:takeover-guide# -->

## Knowledge Base

<!-- #ZEROPS_EXTRACT_START:knowledge-base# -->

### Stripe webhooks
The app exposes `POST /webhooks/stripe`. After deploy, copy the app's public URL into your Stripe dashboard → **Developers → Webhooks**, subscribe to `checkout.session.completed` and `customer.subscription.*`, and paste the resulting signing secret back as `STRIPE_WEBHOOK_SECRET`.

### Why the admin password is a null secret
`ADMIN_PASSWORD` seeds the first admin user on initial migration. It can't be machine-generated (you need to know it to log in), so it's declared as a `null` value — the recipe wizard prompts for it instead of inventing one you'd never see.

<!-- #ZEROPS_EXTRACT_END:knowledge-base# -->

---

Need help setting your project up? Join the [Zerops Discord community](https://discord.gg/zeropsio).
