# Billbird — Vibecoded SaaS Starter

<!-- #ZEROPS_EXTRACT_START:intro# -->
**Billbird** is a small subscription-billing SaaS I vibecoded — [Node.js](https://nodejs.org) + [PostgreSQL](https://www.postgresql.org/) + [Stripe](https://stripe.com), running on [Zerops](https://zerops.io). Users sign up, pick a plan, and Stripe handles the money. Unlike a one-click utility it needs **secrets only a human can supply**, so deploying walks you through a short config step first.
<!-- #ZEROPS_EXTRACT_END:intro# -->

⬇️ **Full recipe page and deploy with one-click**

[![Deploy on Zerops](https://github.com/zeropsio/recipe-shared-assets/blob/main/deploy-button/light/deploy-button.svg)](https://app.zerops.io/recipes/detail?github=https://github.com/fxck/recipe-sim-vibecoded-saas&yaml=production)

- **Development** [[info]](/0%20%E2%80%94%20Development) — [[deploy]](https://app.zerops.io/recipes/detail?github=https://github.com/fxck/recipe-sim-vibecoded-saas&yaml=development)
- **Production** [[info]](/1%20%E2%80%94%20Production) — [[deploy]](https://app.zerops.io/recipes/detail?github=https://github.com/fxck/recipe-sim-vibecoded-saas&yaml=production)

## Take-over guide

<!-- #ZEROPS_EXTRACT_START:takeover-guide# -->
This is your own code. Deploying gives you a running copy; these steps make it production-ready under your account and domain.

### 1. Make it your own
```bash
git clone https://github.com/fxck/recipe-sim-vibecoded-saas my-billbird
cd my-billbird
git remote set-url origin git@github.com:<you>/my-billbird.git
git push -u origin main
```
Connect your fork under **Build & deploy pipeline → connect repository** and every push deploys.

### 2. Provide your secrets
The recipe declares the human-only secrets as `null` in the import yaml, so the deploy wizard prompts for them and **blocks the deploy until the required ones are filled**:

| Secret | Required? | Where to get it |
|--------|-----------|-----------------|
| `STRIPE_SECRET_KEY` | yes | Stripe dashboard → Developers → API keys |
| `STRIPE_WEBHOOK_SECRET` | yes (prod) | Created in step 3, below |
| `ADMIN_PASSWORD` | yes | You choose it — seeds the first admin user |
| `STRIPE_PUBLISHABLE_KEY` | optional | Safe to leave blank; the checkout widget reads it client-side |

`APP_KEY` is machine-generated and never prompts.

> [!NOTE]
> The yaml stays portable: a `null` value is valid YAML, so you can also copy the import and run it yourself with `zcli`. The wizard is a convenience, not a lock-in.

### 3. Wire the Stripe webhook
After the app is up, copy its public URL and in **Stripe → Developers → Webhooks** add an endpoint at `POST /webhooks/stripe` subscribed to `checkout.session.completed` and `customer.subscription.*`. Paste the signing secret back as `STRIPE_WEBHOOK_SECRET`.

> [!WARNING]
> Until the webhook secret is set, payments succeed in Stripe but the app never hears about them — subscriptions won't activate. Set it before taking real money.

### 4. Run it on your domain
Add your domain under **Public access & domains**, point the CNAME, and update `BASE_URL` so Stripe redirect URLs match.

### 5. Back up
The database is backed up daily; restore from the **Backups** tab.
<!-- #ZEROPS_EXTRACT_END:takeover-guide# -->

## Knowledge Base

<!-- #ZEROPS_EXTRACT_START:knowledge-base# -->
### The secret taxonomy
Billbird is a good example of how Zerops recipes classify configuration:

- **generated** — `APP_KEY: <@generateRandomString(32)>`: the backend resolves it at import. No human ever sees or types it.
- **required** — a bare `null` value (`STRIPE_SECRET_KEY:`): a human must supply it; the wizard asks and blocks deploy until it's filled.
- **optional** — an explicit empty string (`STRIPE_PUBLISHABLE_KEY: ""`): intentionally blank, never prompted.

### Why the admin password can't be generated
`ADMIN_PASSWORD` seeds the first admin account on the initial migration. A generated value would be invisible to you, so you could never log in — hence it's a required null the wizard collects.

### Idempotent Stripe webhooks
Stripe retries deliveries, so the `/webhooks/stripe` handler must be idempotent: it keys on the Stripe event id and ignores duplicates. If you fork and extend it, preserve that — double-processing a `checkout.session.completed` would grant a plan twice.
<!-- #ZEROPS_EXTRACT_END:knowledge-base# -->

---

Need help? Join the [Zerops Discord community](https://discord.gg/zeropsio).
