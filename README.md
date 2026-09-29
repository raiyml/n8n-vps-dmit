# n8n VPS: The RAM, CPU, Storage and DMIT Plans That Actually Matter

When people search for **n8n VPS**, they are usually not looking for the biggest server they can afford. They are trying to answer a much more practical question: how much VPS do they actually need to keep n8n running reliably, receive webhooks, store execution data, and leave enough headroom for PostgreSQL, Redis, browsers, AI nodes, or other containers?

The current n8n documentation makes one important point clearer than many older VPS guides: its current Docker Compose setup requires **at least 4 GB of RAM and 2 vCPUs**. The same guide says SQLite is fine for trying things out, while PostgreSQL is the more appropriate choice for a production instance serving more than a handful of users or workflows continuously.

That gives us a useful starting line. A $5 VPS with 1 GB of RAM may technically boot some lightweight automation software, but it is not the same thing as having enough resources for a current, production-oriented n8n stack.

For DMIT, that distinction matters because the company does not sell an n8n-specific VPS. Its affiliate link resolves to DMIT's general cloud infrastructure service, where n8n runs as your own self-hosted workload. DMIT currently offers KVM cloud instances across Los Angeles, Hong Kong and Tokyo, with Premium, Eyeball and Tier 1 network options and multiple AMD EPYC hardware generations.

## What an n8n VPS actually needs

The first mistake is sizing the server by workflow count.

Ten tiny scheduled workflows can use fewer resources than one automation that downloads large files, transforms data, invokes browser automation, calls several APIs and keeps a lot of execution history. Current n8n guidance also makes it clear that binary data, execution history, database design and scaling architecture can become resource issues long before the number displayed in your workflow list does.

For a normal self-hosted deployment, think about the VPS in layers.

**2 GB RAM / 1–2 vCPU** is the territory of lightweight experiments and very small deployments. It can look attractive on a price table, but it leaves little room for a database, reverse proxy, monitoring, additional Docker services or resource spikes.

**4 GB RAM / 2 vCPU** is the more relevant baseline for the current n8n Docker Compose documentation. That is the first tier worth considering when the goal is a real self-hosted instance rather than a disposable test box.

**8 GB RAM or more** becomes more interesting when n8n shares the server with PostgreSQL, Redis, browser-based nodes, heavier JavaScript/Python processing, AI-related services or additional applications. The extra RAM is useful because these workloads do not politely take turns using memory.

Storage also deserves more attention than it usually gets. n8n stores execution information, database data and, depending on your workflows, potentially sizable binary files. n8n's own documentation specifically discusses managing execution data and handling binary data as part of scaling a self-hosted deployment.

So a sensible starting specification for a single production-oriented instance is:

| Workload | Practical starting point |
| --- | --- |
| Testing / very light automation | 2 GB RAM, 1–2 vCPU |
| Normal self-hosted n8n | **4 GB RAM, 2 vCPU** |
| n8n + PostgreSQL + more integrations | 4–8 GB RAM, 2–4 vCPU |
| n8n + Redis/queue workers/browser automation | 8 GB RAM or more |
| Multiple applications on the same VPS | Size for the total stack, not n8n alone |

These are sizing guidelines rather than hard n8n limits. The current official Docker Compose requirement is the strongest concrete reference point: **4 GB RAM and 2 vCPUs minimum for that setup**.

## n8n Cloud versus an n8n VPS

Self-hosting changes the cost model.

n8n Cloud charges according to monthly workflow executions. Its current public pricing lists Starter at €20/month when billed annually for 2,500 executions, Pro at €50/month for 10,000 executions, Business at €667/month for 40,000 executions, and Enterprise on custom pricing. Cloud plans also include managed hosting, while Business is currently self-hosted only.

A VPS works differently. You pay for the server rather than every workflow execution, but you also become responsible for the server itself: Docker, updates, TLS, backups, database maintenance, firewall rules and recovery.

That means the cheaper headline VPS price is not automatically the lower total cost.

For a small personal setup with occasional scheduled workflows, managed n8n may be easier to justify. A self-hosted VPS becomes more attractive when you want your own server environment, need unrestricted self-hosted workflows, or already know how to operate Docker and Linux.

n8n itself confirms that its standard self-hosted Community Edition is available, while its Cloud service uses execution-based plans.

## Where DMIT fits into the n8n VPS picture

DMIT is a particularly unusual fit compared with the mass-market VPS hosts that dominate generic “best VPS” articles.

Its current infrastructure is organized around **location, network series and hardware platform**. The company lists Los Angeles, Hong Kong and Tokyo locations, with Premium, Eyeball and Tier 1 networking, and AS3, AN4 and AN5 hardware platforms. The home page describes KVM cloud instances, AMD EPYC CPUs, NVMe storage and deployment through its own control panel.

For n8n, the network story matters less than it does for a latency-sensitive game server or China-facing website. n8n spends most of its time talking to APIs and waiting for external services, so the exact network route should be chosen according to where your users and integrations live rather than because a bandwidth number looks impressive.

DMIT's own network descriptions make the distinction fairly clear. Premium is designed around China Mainland and Asia-Pacific routing, Eyeball aims for a middle ground, while Tier 1 focuses on general connectivity across APAC and the Americas without the China-specific routing enhancements.

That can make the cheaper Tier 1 products perfectly reasonable for a US-based n8n instance serving normal SaaS APIs.

There is also a current caveat that should not be buried in the fine print: DMIT says its **LAX AS3 platform is still being built out and optimized**, and warns that disk performance may be lower and the SLA may be lower than mature platforms during that period.

For a production automation server, that is relevant. If n8n is handling business-critical webhooks, the cheapest available combination is not necessarily the one you want simply because the CPU and RAM fit the spreadsheet.

## DMIT pricing: the current plans that are relevant

DMIT's live pricing page contains a much larger matrix than a typical VPS pricing page because the combinations depend on location, network series and hardware. The page also warns that displayed pricing can lag behind adjustments, so stock and checkout should be checked before payment.

The current verified catalog snapshot based on DMIT's official Pricing / Cloud Instance pages identifies 14 directly listed, purchasable representative plans across LAX, HKG and TYO. That snapshot was checked on August 14, 2026, and records the official product IDs used for direct ordering. It also explicitly excludes out-of-stock variants from its purchasable catalog.

### Full current representative plan comparison

| Plan | vCPU | RAM | SSD | Transfer | Port | Price | Billing | Purchase |
| --- | ---: | ---: | ---: | ---: | ---: | ---: | --- | --- |
| LAX.AN5.T1.V2C2G | 2 | 2 GB | 40 GB | 5,000 GB | 10 Gbps | $14.90 | Monthly | [ View V2C2G](https://www.dmit.io/aff.php?aff=18446&pid=169) |
| LAX.AN5.T1.V2C4G | 2 | **4 GB** | 80 GB | 10,000 GB | 10 Gbps | $23.90 | Monthly | [ View V2C4G](https://www.dmit.io/aff.php?aff=18446&pid=170) |
| LAX.AS3.Pro.TINY | 1 | 2 GB | 20 GB | 1,000 GB | 1 Gbps | $10.90 | Monthly | [ View Pro TINY](https://www.dmit.io/aff.php?aff=18446&pid=253) |
| LAX.AS3.Pro.Pocket | 2 | 2 GB | 40 GB | 1,500 GB | 4 Gbps | $16.90 | Monthly | [ View Pro Pocket](https://www.dmit.io/aff.php?aff=18446&pid=254) |
| LAX.AS3.Pro.STARTER | 2 | 2 GB | 80 GB | 3,000 GB | 10 Gbps | $34.90 | Monthly | [ View Pro STARTER](https://www.dmit.io/aff.php?aff=18446&pid=255) |
| HKG.AS3.T1.TINY | 1 | 1 GB | 20 GB | 2,000 GB max | — | $6.90 | Monthly | [ View HKG T1 TINY](https://www.dmit.io/aff.php?aff=18446&pid=198) |
| HKG.AS3.T1.STARTER | 1 | 2 GB | 40 GB | 4,000 GB max | — | $12.90 | Monthly | [ View HKG T1 STARTER](https://www.dmit.io/aff.php?aff=18446&pid=199) |
| HKG.AS3.EB.TINYv2 | 1 | 1 GB | 20 GB | 1,000 GB | 1 Gbps | $29.90 | Monthly | [ View HKG EB TINYv2](https://www.dmit.io/aff.php?aff=18446&pid=210) |
| HKG.AS3.EB.STARTERv2 | 1 | 2 GB | 40 GB | 2,000 GB | 2 Gbps | $59.90 | Monthly | [ View HKG EB STARTERv2](https://www.dmit.io/aff.php?aff=18446&pid=211) |
| HKG.AS3.Pro.STARTER | 1 | 2 GB | 40 GB | 1,000 GB | 1 Gbps | $79.90 | Monthly | [ View HKG Pro STARTER](https://www.dmit.io/aff.php?aff=18446&pid=266) |
| TYO.AS3.T1.TINY | 1 | 1 GB | 20 GB | 2,000 GB max | — | $6.90 | Monthly | [ View Tokyo T1 TINY](https://www.dmit.io/aff.php?aff=18446&pid=131) |
| TYO.AS3.T1.STARTER | 1 | 2 GB | 40 GB | 4,000 GB max | — | $12.90 | Monthly | [ View Tokyo T1 STARTER](https://www.dmit.io/aff.php?aff=18446&pid=132) |
| TYO.AS3.Pro.TINY | 1 | 1 GB | 20 GB | 500 GB | 1 Gbps | $21.90 | Monthly | [ View Tokyo Pro TINY](https://www.dmit.io/aff.php?aff=18446&pid=138) |
| TYO.AS3.Pro.STARTER | 1 | 2 GB | 40 GB | 1,000 GB | 1 Gbps | $45.90 | Monthly | [ View Tokyo Pro STARTER](https://www.dmit.io/aff.php?aff=18446&pid=139) |

The product IDs and the pricing/specification combinations above come from the current verified DMIT plan snapshot, which states that the order links use the referral link plus the official product PID.

DMIT's live pricing page also exposes additional AN4/AN5 and location/network variants, including out-of-stock combinations. For example, several LAX AN4 rows are currently shown as **Out of Stock**, so they should not be treated as available n8n purchase options merely because the row remains visible on the pricing page.

## Which DMIT specification makes sense for n8n?

The easiest way to filter the table is to start with memory.

The $6.90–$16.90 plans are tempting, but most of them sit below the current 4 GB / 2 vCPU baseline for the full Docker Compose deployment. That does not make them useless. They can still be useful for small utility services, testing or a stripped-down workload, but buying one specifically because it says “VPS for $6.90” does not answer the n8n sizing problem.

The **LAX.AN5.T1.V2C4G** is much closer to the specification I would start evaluating for a standard n8n deployment: 2 vCPUs, 4 GB RAM, 80 GB SSD and 10,000 GB maximum transfer at $23.90/month. The product is explicitly listed on the current DMIT catalog as a Tier 1 LAX plan.

For a user whose main requirement is optimized China/APAC routing rather than ordinary global API connectivity, the LAX AS3 Premium family is a different proposition. The cheapest listed Pro plan is $10.90/month, but its 1 vCPU and 2 GB RAM are below the current full-Compose recommendation. The Pro Pocket and STARTER still have 2 GB RAM, so the pricing is better understood as a network purchase rather than an n8n resource recommendation.

That distinction can save money in the long run: **buy RAM and CPU for n8n first, then pay extra for specialized routing only when the workload actually benefits from it.**

## What about storage?

80 GB may sound excessive for a simple automation server, but storage consumption depends heavily on what your workflows do.

A text-only workflow that triggers a few APIs every day has a very different footprint from one that downloads PDFs, processes images, manipulates files or retains large amounts of execution data.

n8n's current documentation recommends thinking about execution-data management and binary data handling as the workload grows. Its Docker Compose guide also recommends PostgreSQL for production installations that handle more than a handful of users or workflows continuously.

That means the storage figure on the VPS card should not be treated as “space available for anything.” Some of it will be consumed by the operating system, Docker images, logs, database files and backups.

A practical setup looks more like:

text
VPS
├── Linux
├── Docker
├── n8n
├── PostgreSQL
├── reverse proxy / TLS
├── logs
└── backup space


Once you add image processing, browser automation or other containers, that list grows.

## Docker is the sensible deployment path

n8n's current official self-hosting documentation supports several installation methods, but Docker Compose is specifically documented for people who want full control over the configuration or want to integrate n8n into an existing Compose project. The current guide requires Docker Engine, Docker Compose v2, 4 GB RAM and 2 vCPUs for the described stack.

For a serious VPS deployment, I would also keep the n8n data directory and database persistent, use environment variables for secrets, and make sure the public-facing setup has TLS.

The current n8n security checklist is refreshingly concrete: keep the privileged sandbox components away from the public internet, only expose the required application port through the cloud firewall, and protect the sandbox and encryption credentials.

That is the part many “cheap n8n VPS” articles gloss over. Getting n8n to show its login screen is easy. Keeping the server secure six months later is the actual job.

## Queue mode and scaling change the calculation

Once one n8n process stops being enough, the server specification is no longer just “how much RAM does n8n use?”

n8n supports scaling architectures that introduce additional workers and related infrastructure. The current pricing documentation also explicitly lists queue mode as part of enterprise scaling capabilities, while the documentation index includes dedicated material for queue mode, concurrency control, performance measurement and execution-data management.

For that reason, an 8 GB VPS is often easier to live with than a 4 GB box once n8n becomes part of a broader automation stack.

You do not necessarily need to jump straight to a giant VPS. The better strategy is to monitor actual CPU, memory and disk usage and scale when the workload demonstrates that you need it.

## DMIT's network choice matters more than it first appears

DMIT's three network categories are not just different names for the same connection.

Its Premium Network is built around specialized China Mainland and APAC routing. Its Eyeball Network takes a lower-cost approach to China-facing connectivity. Tier 1 is intended as the cost-conscious general network without the specialized China-routing enhancements.

For an n8n user in the United States whose workflows mostly call OpenAI, Google, Slack, Stripe, GitHub, AWS or other mainstream services, the existence of CN2 GIA is not by itself a reason to pay more.

For a workload whose webhook callers, APIs or users are concentrated in China or nearby APAC markets, network topology can become part of the application design. In that case, DMIT's location and routing options become more relevant than their headline CPU price.

DMIT currently advertises Los Angeles as its major North American node, while Hong Kong and Tokyo are positioned around APAC connectivity. Its own location pages describe Hong Kong as a major China/APAC interconnection point and Tokyo as an East-Asia node with optimized China and intra-Asia routing.

## One important DMIT caveat for production n8n

There is a difference between “available” and “mature.”

DMIT currently warns that the LAX AS3 series is still being built out and optimized, with potentially reduced disk performance and a lower SLA than mature platforms.

For a hobby automation server, that may be an acceptable trade-off. For a business workflow that receives customer webhooks and triggers revenue-critical processes, the warning deserves more weight.

There is a similar issue with the HKG Eyeball network: DMIT currently labels it as **Beta** and says its routing is still being tuned, explicitly noting that it is not yet recommended for production workloads requiring high stability.

Those are the kinds of limitations that matter much more than a “10 Gbps” label.

## What do users say about DMIT?

The third-party feedback is not clean enough to turn into a simplistic score.

Trustpilot currently shows only **four reviews** for DMIT, with a 2.6 TrustScore. Three of the four reviews are from 2026, and the displayed ratings are all one star. Trustpilot also notes that the profile is unclaimed and that the sample may not be representative.

The complaints shown there focus on support experiences, outages and connectivity issues. Those are real published customer reports, but the tiny sample size means they should not be treated as a statistical description of every DMIT customer.

Other independent pages describe DMIT's network performance more positively, particularly around Los Angeles-to-Asia connectivity, but these are individual experiences rather than controlled service-level measurements. That is exactly why it is useful to separate **documented product characteristics** from anecdotal reviews.

For an n8n buyer, the practical takeaway is simple: evaluate DMIT's actual region, routing profile, stock status, refund window and support expectations rather than assuming that one aggregate review score tells the whole story.

## Is there a current DMIT coupon?

I would not build the purchase decision around an old DMIT coupon code.

The current pricing/plan snapshot checked the official Pricing and Cloud Instance pages and found **no universal official coupon published there**. It also specifically distinguishes an affiliate referral parameter from a discount code.

There are many older third-party pages circulating with historical DMIT discount codes, including codes from past years. That is not enough evidence that the code still works in September 2026.

For the current purchase flow, the safer approach is to use the verified affiliate deeplink for the actual product and inspect the checkout total before paying.

[👉 Check the current DMIT VPS options](https://bit.ly/DmiT)

## A practical n8n VPS setup on DMIT

A straightforward production-oriented architecture can stay surprisingly simple:

text
Internet
   │
   ▼
Domain + HTTPS
   │
   ▼
Reverse Proxy
   │
   ▼
n8n container
   │
   ├── PostgreSQL
   └── optional Redis / workers


Start with a server that meets the current baseline, keep your domain and TLS configuration simple, and avoid adding Redis or multiple workers until the workload actually requires them.

For a single-user or small-team n8n deployment, 4 GB RAM and 2 vCPUs is the important line to watch. Once you add browser automation, large files, several other containers or queue workers, 8 GB becomes a more comfortable operating margin.

The other thing worth deciding early is where your backups live. A VPS is not a backup strategy. Database dumps, workflow exports and server snapshots should have a recovery plan that does not depend entirely on the same machine that could fail.

## So, is DMIT suitable for n8n?

It can be, but the reason to choose it should be specific.

DMIT makes more sense when you care about one or more of these things:

* A KVM VPS where you control the Docker environment.
* Los Angeles, Hong Kong or Tokyo deployment options.
* Specialized APAC or China-facing routing.
* AMD EPYC infrastructure and high-bandwidth network options.
* A cloud instance you can use for n8n plus other services.

It makes less sense to pay for specialized network routing when your n8n instance mostly talks to ordinary US-based APIs and your main requirement is simply low-cost compute.

For a conventional n8n installation, the most useful comparison point in DMIT's current catalog is a **2 vCPU / 4 GB RAM** configuration rather than the cheapest 1 GB or 2 GB plan. The LAX.AN5.T1.V2C4G is currently listed at $23.90/month with 80 GB SSD and 10,000 GB maximum transfer, making it a straightforward reference point for a general-purpose deployment.

[👉 View the LAX.AN5.T1.V2C4G plan](https://www.dmit.io/aff.php?aff=18446&pid=170)

If your workload specifically depends on premium China/APAC routes, the decision changes. In that case, DMIT's Premium family deserves a separate look, but the n8n resource requirements still come first.

## FAQ

### Can I run n8n on a 1 GB VPS?

A 1 GB VPS can be suitable for very light server tasks, but it is below the current **4 GB RAM / 2 vCPU** requirement documented for n8n's current Docker Compose setup. I would not use a 1 GB plan as the baseline for a new production n8n deployment.

### Is 2 GB enough for n8n?

It can be enough for some lightweight or simplified deployments, but the current official Docker Compose guide recommends at least 4 GB RAM and 2 vCPUs. Once PostgreSQL, the current Assistant sandbox stack or other services are involved, the extra memory becomes much more useful.

### Should PostgreSQL run on the same VPS?

For a small deployment, it can. n8n's current Docker Compose documentation explicitly provides a PostgreSQL configuration and recommends PostgreSQL rather than SQLite for production instances serving more than a handful of users or workflows around the clock.

### Do I need Redis for n8n?

Not for every deployment. Redis becomes relevant when you move into architectures that need queue-based workers and more deliberate horizontal scaling. There is no reason to add the complexity to a small single-instance setup merely because a larger n8n architecture can use it.

### Is 80 GB SSD enough?

For ordinary API-based automation, 80 GB can be a reasonable starting capacity. Workflows that process large files or retain substantial execution data can consume storage much faster, so monitoring and retention management matter more than the number printed in the plan card.

### Does DMIT provide an n8n-specific one-click VPS?

The current DMIT site presents its cloud instances as general-purpose KVM virtual machines rather than an n8n-branded hosting product. In other words, you are choosing the VPS and deploying n8n yourself. DMIT does advertise instant deployment, one-click system installation and SSH-key access for its cloud instances.

### What is the main thing to check before ordering?

Check **the exact location, network series, stock status, CPU/RAM combination and checkout price**. DMIT's pricing page explicitly warns that displayed product and price information may not update instantly, so the live checkout should be treated as the final availability check.

## Bottom line

For **n8n VPS**, the useful starting point is not “the cheapest VPS.” It is the smallest configuration that leaves enough room for the actual n8n stack.

Today, that means treating **2 vCPU and 4 GB RAM** as the important baseline for the current official Docker Compose setup, then increasing RAM when you add PostgreSQL-heavy workloads, browser automation, Redis, additional containers or higher concurrency.

DMIT gives you several ways to make that deployment more specialized. Its Los Angeles, Hong Kong and Tokyo locations, plus Premium, Eyeball and Tier 1 networks, make the provider more interesting for cross-Pacific or APAC-oriented workloads than a generic “cheap VPS” comparison suggests. At the same time, DMIT's own warnings about the developing LAX AS3 platform and beta HKG Eyeball routing are reasons to match the product to the workload instead of assuming every plan is interchangeable.

For a general-purpose n8n deployment, start by comparing the **4 GB / 2 vCPU class**. For China/APAC-sensitive automation, then compare the routing profiles. And before purchasing, verify the exact plan and stock status at checkout rather than relying on an old coupon page or a cached pricing table.

[👉 Check DMIT's current n8n-capable VPS options](https://bit.ly/DmiT)
