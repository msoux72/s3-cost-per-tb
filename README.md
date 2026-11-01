# s3 storage costs: what you actually pay per TB, and how to keep the bill flat for backups and archives

S3 storage costs come in three parts: the storage itself, API request fees, and egress — the charge for pulling your own data back out. Most people searching for this are staring at the first number and getting blindsided by the other two. On AWS S3 Standard, 1TB of storage runs about $23.55/month, but reading that same 5TB back out in one month adds roughly $450 in egress fees alone. That's the real cost structure, and it's why flat-rate S3-compatible providers have become the default answer for backups, archives, and other write-heavy, read-occasionally workloads.

One of those providers is Sharktech, a hosting company that operates its own data centers in Los Angeles, Denver, Chicago, and Amsterdam, and launched its S3 Object Storage line across all of them in March 2023. Its pricing page lists storage at **$4.90/TB/month** with the first 1TB of monthly transfer included. Whether that fits you depends on your read patterns — this article walks through the actual numbers so you can decide in a few minutes.

> **Quick take:** for backups, archives, and CI/CD artifacts where you write a lot and rarely read, a flat-rate plan like Sharktech's 1TB starter at $4.90/month removes both the request fees and most egress risk. If you need high-frequency, low-latency access or deep ecosystem integrations, AWS or a bigger S3 brand still makes sense.
> 👉 [Check the 1TB plan and current pricing at Sharktech](https://portal.sharktech.net/cart.php?a=add&pid=643&carttpl=s3_storage_cart&billingcycle=monthly&configoption%5B1858%5D=13673&configoption%5B1859%5D=1&aff=1626)

## What you actually pay for with S3 storage

Four line items make up an S3 bill, and only one of them is storage. AWS's own pricing page (US East region) breaks down like this:

1. **Storage** — priced per GB per month, and it varies by storage class. S3 Standard is $0.023/GB-month for the first 50TB.
2. **Requests** — every PUT, GET, and LIST costs money. Writes run $0.005 per 1,000 requests; reads $0.0004 per 1,000.
3. **Egress (data transfer out)** — $0.09/GB to the internet after the first 100GB each month. At ~1,000GB per TB, that's about $90/TB.
4. **Data management** — lifecycle rules, replication, inventory, and similar features each carry their own charges.

One detail that trips people up: AWS bills storage in binary gigabytes (GiB), so "1TB" on your invoice is 1,024GB, not 1,000GB. Your bill will be slightly higher than a quick mental estimate.

A concrete example at the 1TB level, priced from public AWS rates: $23.55 for the storage, $9.00 if you transfer 100GB out, $5.00 for a million PUTs, and $4.00 for ten million GETs. Roughly $41 in a month where you touched the data moderately. Do nothing at all and the bucket costs $23.55; AWS charges nothing for an empty bucket, so experimenting is free.

## Why the egress line dominates real-world S3 costs

Storage is the predictable part; egress is the one that ruins budgets. AWS charges about $90/TB to move data out to the internet. If you keep 5TB of backups on S3 and need to restore all of it after an incident, the download alone costs roughly $450 — for data you already paid to store.

Cloudflare R2 made its name by dropping egress to zero while charging roughly $15/TB for storage. Backblaze B2 pairs $6.95/TB storage with free egress through Cloudflare's bandwidth alliance, capped at three times your stored volume per day. Wasabi includes egress free under a fair-use policy capped at your stored volume times three — but enforces a 90-day minimum storage duration per object, which quietly raises the effective price if you delete data sooner.

The pattern across all of these: the sticker price for storage gets the attention, the fine print around egress and retention determines your actual bill. When comparing "s3 storage costs" across providers, always price your worst-case restore month, not your average month. This is also where flat-rate plans change the math entirely. Sharktech's included transfer scales with your plan size, and overage runs $0.90/TB — a figure Sharktech confirms across its own product pages. On a 10TB plan, a full 10TB restore in one month costs nothing extra in transfer; even a restore that doubles your normal traffic adds under $10. The same full restore on AWS S3 Standard would run about $900.

## Sharktech S3 Object Storage: full plans and pricing

Sharktech sells one configurable S3 product rather than named tiers: you pick storage from 1 to 100TB, bandwidth from 1 to 10,000TB, and a location — Los Angeles, Las Vegas, Denver, Chicago, or Amsterdam. Billing is monthly with no contract and no setup fee. The official pricing page anchors the rate at **$4.90/TB/month**, and larger capacities scale linearly from there.

| Plan | Storage | Included transfer | Monthly price | Billing | Order |
| --- | --- | --- | --- | --- | --- |
| Starter | 1 TB | 1 TB | $4.90 | Monthly | [ Order the 1TB plan](https://portal.sharktech.net/cart.php?a=add&pid=643&carttpl=s3_storage_cart&billingcycle=monthly&configoption%5B1858%5D=13673&configoption%5B1859%5D=1&aff=1626) |
| Small | 5 TB | 5 TB | $24.50 | Monthly | [ Order the 5TB plan](https://portal.sharktech.net/cart.php?a=add&pid=643&carttpl=s3_storage_cart&billingcycle=monthly&configoption%5B1858%5D=13673&configoption%5B1859%5D=5&aff=1626) |
| Medium | 10 TB | 10 TB | $49.00 | Monthly | [ Order the 10TB plan](https://portal.sharktech.net/cart.php?a=add&pid=643&carttpl=s3_storage_cart&billingcycle=monthly&configoption%5B1858%5D=13673&configoption%5B1859%5D=10&aff=1626) |
| Growth | 25 TB | 25 TB | $122.50 | Monthly | [ Order the 25TB plan](https://portal.sharktech.net/cart.php?a=add&pid=643&carttpl=s3_storage_cart&billingcycle=monthly&configoption%5B1858%5D=13673&configoption%5B1859%5D=25&aff=1626) |
| Scale | 50 TB | 50 TB | $245.00 | Monthly | [ Order the 50TB plan](https://portal.sharktech.net/cart.php?a=add&pid=643&carttpl=s3_storage_cart&billingcycle=monthly&configoption%5B1858%5D=13673&configoption%5B1859%5D=50&aff=1626) |
| Large | 100 TB | 100 TB | $490.00 | Monthly | [ Order the 100TB plan](https://portal.sharktech.net/cart.php?a=add&pid=643&carttpl=s3_storage_cart&billingcycle=monthly&configoption%5B1858%5D=13673&configoption%5B1859%5D=100&aff=1626) |
| Custom | 300TB+ storage / 300TB+ transfer | Negotiated | Contact sales | Custom | [ Request a custom quote](https://bit.ly/SharKtech) |

Two footnotes worth reading before you order. The official price list directly confirms three anchor points — 1TB at $4.90, 10TB at $49, and 100TB at $490 — while the 5TB and 25TB rows follow the same linear $4.90/TB model, with Sharktech confirming final pricing at checkout. Storage pricing excludes outbound transfer beyond the included allocation. Second, extra transfer past your included amount is **$0.90/TB**, which matters less than it sounds: at 10TB stored with 10TB included, a routine month of backup traffic plus a full restore stays inside the included transfer. You'd need to push well beyond a full-cycle read before overage even starts.

Beyond the headline rate, what's absent from the invoice is the selling point: no API request charges, no retrieval-class fees, no minimum storage duration, and no commitment tiers where the good rate only kicks in at some petabyte threshold. The rate you pay at 10TB is the rate you'd pay at 100TB. During checkout you'll also see an optional Acronis Cloud Backup add-on at $4.00/month — skippable if you already run your own backup tooling.

## Side-by-side: what 1TB of S3 storage costs across providers

Here's how the major options compare on published rates, as reported in third-party comparisons — treat these as each provider's listed price and check current terms before committing, since the "catch" column is where bills actually diverge.

| Provider | Storage ($/TB/mo) | Egress | The catch |
| --- | --- | --- | --- |
| Sharktech S3 | $4.90 | First 1TB included, then $0.90/TB | Smaller ecosystem; fewer one-click integrations |
| Backblaze B2 | $6.95 | Free via Cloudflare, otherwise ~$10/TB | Daily egress capped at 3× stored volume |
| Wasabi | $7.99 | Free under fair use (3× storage) | 90-day minimum storage duration |
| Cloudflare R2 | ~$15 | $0 | Class A operation fees |
| AWS S3 Standard | ~$23.55 (first 50TB) | ~$90/TB after 100GB free | Per-GB request fees plus storage-class management |

Put another way, Sharktech's raw rate lands roughly 30% under Backblaze B2 and about 38% under Wasabi. Over a year, 10TB of backups costs $588 at Sharktech, $834 at B2, and $2,826 at AWS before a single request or egress fee. The gap compounds further for read-heavy workloads since Sharktech doesn't meter API calls at all.

To be fair to the bigger names: Wasabi and Backblaze have larger communities, more tutorials, and one-click integrations in popular backup tools. If your stack depends on a vendor's official integration and you don't want to configure an S3 endpoint manually, that convenience has value. Sharktech's endpoint works with anything that speaks standard S3 — rclone, Cyberduck, Restic, Nextcloud, FileZilla, MinIO clients, Terraform pipelines — but you'll be pointing your existing tool at a new endpoint yourself, usually a two-line config change.

## What Sharktech's storage actually is (and when it's the wrong tool)

Sharktech built this service around HDD capacity economics with an NVMe cache layer in front, exposed through a standard S3-compatible API. The company states the positioning plainly: this is a capacity-first object storage service, not an all-flash block-storage replacement. Storage sits in their own data centers, each connected at 40G inbound and outbound, with network-level DDoS protection included by default.

Feature-wise it covers what backup and archive workflows need: bucket and object ACLs with bucket policies, versioning and object-lock retention, lifecycle rules for expiration, presigned URLs for time-limited sharing, multipart uploads with integrity checks, CORS, bucket notifications, object tags, access logging, and static site hosting. Two things require talking to their team to arrange: storage classes and cross-region replication.

The official guidance on fit is refreshingly direct. Use it when you need economical, scalable capacity for objects accessed through S3-compatible software — backups, archives, media libraries, logs, application assets, build artifacts. Pick something else when your application needs a boot disk, filesystem semantics, or consistently low-latency block storage. If you're hosting a database or anything that serves latency-sensitive reads all day, this pricing model isn't built for you, and pretending otherwise would just end in a support ticket.

## Questions people ask before switching off AWS

**Does an empty S3 bucket cost anything?** On AWS, no — you pay only for what you store, so testing is free. Sharktech's entry plan is a flat $4.90/month from day one, which is cheaper than storing nothing on AWS Standard but assumes you'll actually use the capacity. If you just want to try the workflow before committing capacity, that's what the starter tier is for: 👉 [start with 1TB at $4.90/month and see how your tools connect](https://portal.sharktech.net/cart.php?a=add&pid=643&carttpl=s3_storage_cart&billingcycle=monthly&configoption%5B1858%5D=13673&configoption%5B1859%5D=1&aff=1626).

**Is S3-compatible the same as S3?** Compatibility here means the API: buckets, objects, presigned URLs, multipart uploads. Anything speaking the S3 protocol works. What you don't get on flat-rate providers is AWS's surrounding ecosystem — Lambda triggers with the same depth, the full storage-class ladder, or integrations hardwired to AWS specifically. For rclone-driven backups or Restic repositories, the difference is invisible.

**What happens if I read my data a lot?** This is the question that should drive your choice. Sharktech's included transfer scales with your plan, and overage is $0.90/TB — so even a full restore costs pocket change. On Wasabi and Backblaze, heavy egress eventually hits fair-use caps tied to your stored volume. On AWS, there's no cap, just the $0.09/GB meter running.

**Is there a minimum commitment or retention period?** Sharktech has none: monthly billing, no minimum storage duration, cancel by not renewing. Wasabi enforces a 90-day minimum per object, which penalizes short-lived data like rolling backup sets.

## Choosing based on your actual workload

If your month looks like "write 3TB of backups, read almost nothing" — the most common case — the math is unambiguous. Ten terabytes of storage with typical backup traffic runs $49/month at Sharktech versus $235.50 in storage alone on AWS Standard, before requests and any restore.

A few last checks before ordering: confirm your restore volume stays realistic against the included transfer (though at $0.90/TB overage, even a bad month won't hurt), pick the data center closest to where your backup jobs run, and skip the Acronis add-on if you already have a backup tool you like. Anything beyond 100TB of storage or 300TB of monthly transfer goes through their sales team for custom pricing, as does quarterly or annual billing if you prefer it.

If you're migrating from another S3 provider, plan on a same-week move: reconfigure your tool's endpoint and keys, then let your backup software re-sync. Most growing backup sets outgrow 1TB within a year or two, and the per-TB rate doesn't change when you scale up — 👉 [the 10TB plan at $49/month](https://portal.sharktech.net/cart.php?a=add&pid=643&carttpl=s3_storage_cart&billingcycle=monthly&configoption%5B1858%5D=13673&configoption%5B1859%5D=10&aff=1626) is where those workloads usually land, with enough headroom that you won't think about capacity again for a while.
