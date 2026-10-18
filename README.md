# best vps for node js: how much RAM you really need, where to deploy, and which cheap plans are worth the money

Pick a Node.js server in this order and the "best" choice mostly makes itself: how much RAM your app actually needs, where your users live, and whether you want month-to-month billing or a discounted prepay. Most Express APIs, bots, and small services run comfortably on the $6–10 entry tiers from the major clouds. Next.js server-side rendering and full stacks with a database are a different story — they want 4GB, and that single fact changes which provider is right for you.

This guide walks through the actual decision: RAM sizing with real worker numbers, the providers that keep ranking for this search, Vultr's current plans and where its 32 locations genuinely pay off, plus the billing math that most comparison tables leave out.

> **Quick pick:** if your users are spread across Asia, South America, or Africa — or you just want one-click Node.js deployments in 32 locations — Vultr's Cloud Compute starts at **$6/mo** (1 vCPU, 1GB RAM, 25GB NVMe), billed monthly. 👉 [Check Vultr's current plans and pricing](https://www.vultr.com/pricing/?ref=7268283). If your audience is almost entirely in Europe or North America and you're comfortable managing Linux yourself, Hetzner's CX22 (2 vCPU, 4GB, ~€4.35/mo) is the stronger raw value right now.

## How much RAM does a Node.js VPS actually need?

1GB is enough for an API-only box. 2GB is the realistic floor once a build step or a database joins the party. 4GB is where a production stack stops feeling tight.

The reason is per-process memory. Node runs your app as a single-threaded process, so a PM2 cluster spawns one worker per core, and each worker carries its own heap. Recent hands-on reviews of production Node stacks put a typical Express API worker at 80–130MB, and a Next.js SSR worker at 200–400MB. Add the OS, Nginx, and any databases, and a common full stack adds up roughly like this:

| Component | Approx. RAM |
| --- | --- |
| Node.js (2 cluster workers) | ~260MB |
| PostgreSQL | ~300MB |
| Redis | ~50MB |
| Nginx | ~15MB |
| OS overhead | ~200MB |
| **Total** | **~825MB** |

That total sits right at the edge of a 1GB plan, which is why 1GB boxes with a database attached fall over under load, and why 4GB is the realistic starting point for a full stack. Next.js has its own catch: the build step. `next build` can spike 1–2GB on its own, and a 4GB box builds and serves a typical Next.js app plus a database without swapping.

If you just want a checklist, the practical minimums look like this:

| Spec | Minimum | Recommended |
| --- | --- | --- |
| vCPU | 1 | 2+ |
| RAM | 1GB | 2GB+ (4GB for SSR or full stacks) |
| Storage | 20GB SSD | 40GB+ NVMe |
| Bandwidth | 1TB | 2TB+ |
| Node.js | 18 LTS | 22 LTS |

One caveat on CPU: extra cores only help if you actually run cluster mode. A 16-core VPS runs a single Express process on one core while the other fifteen sit idle.

## The main contenders at a glance

There's no single winner here — the providers that dominate this search each own a different trade-off:

| Provider | Entry plan | Price | Clear strength |
| --- | --- | --- | --- |
| Vultr | Cloud Compute (1 vCPU, 1GB, 25GB NVMe) | $6/mo | 32 locations; deploy near users anywhere |
| Hetzner | CX22 (2 vCPU, 4GB) | ~€4.35/mo | Best raw value for EU/US audiences, 20TB transfer |
| DigitalOcean | Basic (1 vCPU, 1GB, 25GB) | $6/mo | Docs, managed databases, live chat |
| Contabo | Cloud VPS S (4 vCPU, 8GB) | ~$7/mo | Most RAM per dollar for hobby workloads |
| Linode/Akamai | Shared 2GB | $12/mo | Two decades of track record, very stable network |
| Hostinger | KVM 1 (1 vCPU, 4GB, 50GB NVMe) | $4.99/mo promo | Guided setup — but see the billing math below |

On raw throughput, an autocannon benchmark run from May 2026 (10 concurrent connections held for 60 seconds against an Express API on Node v25) is instructive: Hetzner's CX22 sustained about 4,250 requests per second, while DigitalOcean's Premium 4GB tier managed 4,100 at roughly five times the price — a ~4% performance gap for a very different invoice. Vultr wasn't part of that particular run, but its $6 tier carries the same 1 vCPU / 1GB / 25GB NVMe spec sheet as DigitalOcean's basic box, so the real difference shows up in locations and tooling rather than in the spec table.

## Vultr's plans for Node.js

Vultr's Cloud Compute line starts at **$6/mo for 1GB**, with the 2GB tier at **$10/mo**. Both are billed monthly, and you pick the plan size at deploy time.

| Plan | Config | Price | What it can carry | Deploy |
| --- | --- | --- | --- | --- |
| Cloud Compute 1GB | 1 vCPU · 1GB RAM · 25GB NVMe | $6/mo | APIs, Discord/Telegram bots, queue workers, small services | [Get the 1GB plan](https://www.vultr.com/pricing/?ref=7268283) |
| Cloud Compute 2GB | 1 vCPU · 2GB RAM | $10/mo | App + small database, light SSR (builds get tight) | [Get the 2GB plan](https://www.vultr.com/pricing/?ref=7268283) |

Both tiers are billed monthly. Mapped against the RAM math above, the 1GB box is genuinely fine for API-heavy workloads and bots, which idle at 65–240MB per worker. The 2GB tier handles a single app with a small database, but a Next.js SSR build on it will be tight — if SSR is your workload, step up a tier on whatever provider you choose. Larger Cloud Compute tiers continue up from there with more vCPU, RAM, and storage, and Vultr also offers a high-frequency compute option when you want faster clocks behind the event loop.

Two practical details worth knowing:

- There's a one-click Node.js image at deploy time, so you can skip the nvm-and-version-picking dance entirely.
- 👉 [Vultr's current Cloud Compute pricing](https://www.vultr.com/pricing/?ref=7268283) lists every tier on one page — worth scanning before you settle on a plan size, since scaling later means a resize or redeploy.

Honest note: at 2GB for $10, Vultr isn't chasing the price-per-GB crown — Hetzner sells 4GB for about €4.35. What the extra cost buys is location choice, and for a lot of Node apps that matters more than specs.

## Where Vultr wins: region coverage

For a Node.js app, location usually beats spec bumps. Your event loop resolves in microseconds; the network round-trip to your users and your database is measured in tens or hundreds of milliseconds. A cheap server on the wrong continent produces a slower app than a pricier one next door.

Vultr runs 32 locations, with solid coverage in Asia, South America, and Africa — the regions where the value leaders are weakest. Hetzner, the price-performance pick, lists EU and US regions only. OVHcloud extends to Canada and Australia alongside US/EU/Asia from around $4.54/mo at 4GB, which is relevant if Oceania matters to you.

The practical rule: pick the provider with a region near your users first, then choose a plan size. For real-time apps on Socket.io this compounds — WebSockets hold connections open, and every ping costs a round-trip.

👉 [See Vultr's locations and current pricing](https://bit.ly/VulTR) — deploying close to your users often makes a $6 box feel faster than a bigger one across an ocean.

## Prepay vs month-to-month: the math comparison tables skip

Promo pricing usually has a catch, and it's almost always the billing term.

Hostinger is the clearest example because its specs are genuinely good: KVM 1 gets you 1 vCPU, 4GB RAM, and 50GB NVMe for $4.99/mo. That rate requires 48 months paid upfront — about $240 before you've deployed anything. Monthly billing runs $9.99, and the renewal price lands at $9.99/mo regardless of the term you originally bought. So the same 4GB that Hetzner sells at ~€4.35 month-to-month costs $9.99/mo at Hostinger unless you prepay for four years.

That doesn't make Hostinger a bad deal — if you're certain the server will still be running in four years, the effective rate is excellent. It makes prepay a bet on your own future plans. A few rules of thumb:

- Confident the project will live for years and want the lowest effective rate → a prepaid promo can work out cheap. Check the renewal price first.
- Learning, prototyping, or unsure you'll keep the box → month-to-month, no lock-in. Vultr, Hetzner, and DigitalOcean all bill this way.
- Short-lived build or CI boxes → hourly billing is the feature to hunt for; Kamatera offers it alongside a 30-day trial with $100 credit.

Renewal price is the number to check before checkout on every provider, not just the headline price.

## Getting a Node.js app live on a VPS

Manual setup takes about 20 minutes and gives you full control. On Ubuntu 24.04 LTS:

1. Install Node 22 LTS through nvm, then `nvm install 22` — nvm makes version switches painless later.
2. `npm install -g pm2`, then `pm2 start npm --name "myapp" -- start`, followed by `pm2 save` and `pm2 startup` so the app survives reboots.
3. Put Nginx in front as a reverse proxy to `localhost:3000`. Using Socket.io? Include the WebSocket upgrade headers in the proxy config.
4. `certbot --nginx -d yourdomain.com` for free HTTPS.

On Vultr, the one-click Node.js image preinstalls the runtime and web server, which collapses steps 1–3 into the deploy form.

### Tuning that doesn't require a bigger server

- Run PM2 cluster mode (`pm2 start app.js -i max`) to use every core.
- Set an automatic memory-limit restart: `pm2 start app.js --max-memory-restart 500M`. Node leaks happen; a self-healing process beats a 3am outage.
- Set NODE_ENV to production and pool your database connections (for example, `max: 20` on a PostgreSQL pool).
- Enable gzip, either in Express or at the Nginx level.

And run one diagnostic before paying for an upgrade: monitor your event loop delay with `perf_hooks`. If it's over 100ms, a bigger server won't help — something in your code is blocking, and a single synchronous `fs.readFileSync()` inside a request handler produces exactly that symptom.

## Node.js VPS FAQ

**Is a $6 VPS really enough for production?**
For API-heavy services, yes. A well-written Express API can handle 3,000–5,000 concurrent connections on a single core — the event loop is built for I/O multiplexing. The limits you'll hit first are RAM and network, not CPU.

**Can I run Next.js on a VPS?**
Yes. Build the app, keep it alive with PM2 or a systemd service, and put Nginx or Caddy in front for TLS. Compared with per-request platform pricing you get a predictable monthly bill and full control, at the cost of doing deploys yourself.

**Do I need a separate database server?**
Not at small scale. PostgreSQL or MySQL runs fine next to your app on a 4GB box. Split them out once the database starts competing with the app for RAM.

**VPS or PaaS like Vercel or Railway?**
A VPS is a fixed $5–20/mo with full root access, unrestricted WebSockets, and a persistent filesystem. PaaS platforms deploy easier and scale automatically, but costs can spike with traffic and long-running processes are often limited. If "no surprise bill" matters more than "zero ops," that's your answer.

**What about Discord bots and background workers?**
Bots and queue workers (Bull, Agenda) idle at very low memory — a 65MB webhook receiver or a 120MB queue processor leaves a 1GB box plenty of headroom. This is the case where the cheapest plan is genuinely the right one.

## Which one should you pick?

- Audience in Europe or North America, comfortable with Linux → Hetzner's CX22 at ~€4.35/mo is the value benchmark right now.
- Want managed databases, live chat, and tutorials that hold your hand → DigitalOcean's ecosystem earns its premium.
- RAM-hungry hobby stack (containers, n8n, self-hosted everything) → Contabo's 8GB for ~$7 is the spec-per-dollar play.
- Users across multiple continents, or you want one-click Node deploys close to them → Vultr. Start at $6 for a 1GB API box and move to the $10/2GB tier when a build step or database joins.

Two things to confirm before checkout: pick the region nearest your users first, since adding a second region later is easy; and check the renewal rate, because the promo price and the renewal price are rarely the same number.

👉 [Open Vultr's pricing page, pick your region, and start at $6/mo](https://www.vultr.com/pricing/?ref=7268283)
