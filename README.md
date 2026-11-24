# Evoxt Hong Kong: Which of the 9 VPS Plans Fits You, How CN2 Routing Behaves per Carrier, and Why Bandwidth Is Halved (Full Price Table)

Evoxt's Hong Kong location lists nine plans from **$2.99 to $47.99 per month**, and for most readers the sensible starting point is **VM-1 at $5.99**: 1 vCore, 2 GB RAM, 20 GB storage. Hong Kong sits in what Evoxt calls its premium network tier, which means the same money buys roughly half the monthly transfer you would get in Los Angeles or Frankfurt, and that trade is the single thing worth understanding before you order.

This page covers the full plan list with current prices, what CN2 actually does for Telecom, Unicom and Mobile users, where the cheap plans stop being enough, and who should skip the Hong Kong node entirely.

> **Quick verdict:** VM-1 at $5.99/month covers a WordPress site with a database, a small Laravel app, or a handful of bots on one box. If you only need a Discord bot, a VPN endpoint or a dev sandbox, VM-0.5 at $2.99 or VM-0.75 at $4.99 is enough and the extra money buys nothing you will use. 👉 [See the full Hong Kong plan list and current prices](https://bit.ly/EvoXt)

## All nine Hong Kong plans

Prices below are the ones published on Evoxt's Hong Kong page, in USD, billed monthly. Every plan includes weekly offsite backup, IPv6, KVM virtualization, full root access and a 1 Gbps port.

| Plan | vCPU + RAM | Storage | Price (USD, monthly) | Deploy |
| --- | --- | --- | --- | --- |
| VM-0.5 | 1 vCore / 512 MB | 5 GB | $2.99 | [Deploy VM-0.5](https://bit.ly/EvoXt) |
| VM-0.75 | 1 vCore / 1 GB | 10 GB | $4.99 | [Deploy VM-0.75](https://bit.ly/EvoXt) |
| VM-1 | 1 vCore / 2 GB | 20 GB | $5.99 | [Deploy VM-1](https://bit.ly/EvoXt) |
| VM-1.5 | 2 vCores / 2 GB | 20 GB | $6.95 | [Deploy VM-1.5](https://bit.ly/EvoXt) |
| VM-2 | 2 vCores / 4 GB | 30 GB | $11.99 | [Deploy VM-2](https://bit.ly/EvoXt) |
| VM-3 | 4 vCores / 4 GB | 30 GB | $14.99 | [Deploy VM-3](https://bit.ly/EvoXt) |
| VM-4 | 4 vCores / 8 GB | 60 GB | $23.99 | [Deploy VM-4](https://bit.ly/EvoXt) |
| VM-6 | 8 vCores / 8 GB | 60 GB | $29.99 | [Deploy VM-6](https://bit.ly/EvoXt) |
| VM-8 | 8 vCores / 16 GB | 80 GB | $47.99 | [Deploy VM-8](https://bit.ly/EvoXt) |

Evoxt's Hong Kong page does not print a transfer allowance next to these plans. Third-party trackers that maintain Evoxt plan sheets estimate the Hong Kong monthly allowance at roughly 250 GB on VM-0.5, about 500 GB from VM-0.75 through VM-3, about 1 TB on VM-4 and VM-6, and about 2 TB on VM-8, and they flag those figures as proportional estimates rather than published specs. One independent plan database lists the Hong Kong VM-1 specification as 500 GB per month on a 1 Gbps port at $5.99, which matches the 500 GB estimate. Treat the numbers as a planning guide and confirm the actual allowance on the order page before you pay.

## Why Hong Kong is priced the same but metered differently

CPU, RAM, storage and monthly price on the Hong Kong node match Evoxt's standard regions exactly. The difference is the network tier. Hong Kong, Osaka and Evoxt's Malaysia premium location are grouped as premium regions, and the characteristic of that group is that monthly transfer is roughly half of what the same plan gets in a standard region. Transit into Hong Kong and onward to mainland China costs the provider more, and the allowance is where that cost shows up.

Three practical consequences follow. You get 1 Gbps ports across the board rather than a capped 100 Mbps link. The IPs are native Hong Kong addresses rather than ranges announced from another country, which matters if you run anything that depends on IP geolocation. And Evoxt holds its Hong Kong address space under its own registered "Evoxt (HK)" organisation, with the most recent /24 block reallocated to it on 29 October 2025 and routed through xTom's Hong Kong and Australian networks, so this is a locally registered presence rather than a reseller arrangement.

If your workload pulls a few hundred gigabytes a month, the halved allowance is irrelevant. If it pulls several terabytes, Hong Kong is the wrong region and no amount of routing quality fixes that.

## What CN2 does, and which carrier benefits

Evoxt describes the Hong Kong node as using CN2 for optimized routing into China. CN2 is China Telecom's premium backbone, and it is generally the lowest-latency path available for traffic terminating in mainland China.

The routing is not uniform across carriers. Telecom and Unicom traffic from this node typically returns via Japan, while China Mobile gets a more direct path. That pattern shows up in third-party measurement pages for the Hong Kong VM-1 plan, where median latency differs sharply between carriers and between provinces, with Telecom generally holding the tighter numbers. Community feedback collected in review roundups tends to rate Mobile routing as the best of the three on this node, which is the opposite of what people often assume when they see "CN2" in a product description and expect it to apply evenly to every Chinese ISP.

Latency to southern China is the strongest case. Early traceroute tests put Guangdong and Shenzhen in the 13–25 ms range. Those tests were a small sample taken at one point in time, not a nationwide measurement, so do not expect identical figures from Beijing or Chengdu, and expect variance by province, carrier and time of day.

The practical read: if your users are in Guangdong, Hong Kong or wider Southeast Asia, this node is well positioned. If your users are mostly on Unicom in northern provinces and you are choosing Hong Kong specifically for them, the Japan return path means you are paying a premium-tier price for a route that may not beat a Tokyo or Osaka node.

## What 6.0 GHz buys you, and what it doesn't

Evoxt's headline claim is single-core performance, with its comparison graphic citing 6.0 GHz against roughly 2.4 GHz for AWS, 2.3 GHz for Azure and 2.2 GHz for DigitalOcean. The page itself notes those comparison figures were collected in 2020 and may change. The point of the number is that most budget VPS plans run at a lower clock, and per-core speed is what determines how fast a single PHP request, a database query or a compile step completes.

Workloads that benefit: WordPress and PHP execution, MySQL and PostgreSQL queries that are not already parallelized, code compilation and CI jobs, Discord bots and other latency-sensitive small services.

Workloads that don't: video transcoding, large-scale model training, and anything that scales cleanly across dozens of threads. A high clock on one core does little for a batch encoder.

Evoxt's own Geekbench 5 multi-core figures show where the step changes are. VM-0.75 scores 1073 and VM-1 scores 1098, so doubling RAM from 1 GB to 2 GB barely moves the multi-core number. VM-2, with two cores, scores 1940, and VM-4 with four cores scores 3306. The single-core baseline stays roughly flat across plans; what you buy with the larger plans is more of the same fast cores. That is why the 1-core plans are fine for a single site and why a busy multi-tenant box needs VM-2 or above.

## Picking a plan from your workload

The jump from VM-1 to VM-1.5 is the most useful line in the table. For **$0.96 more per month** you get a second vCore with RAM unchanged at 2 GB. If your site runs PHP workers, cron jobs and a database on the same box and you keep hitting memory limits while one core sits idle, that is the upgrade; if you are short on RAM rather than CPU, it isn't, and you want VM-2 at $11.99 for 4 GB.

Working rules:

- **Discord bot, VPN endpoint, dev sandbox, single small site.** VM-0.5 at $2.99. 512 MB is genuinely enough for these.
- **One site that needs a real runtime.** VM-0.75 at $4.99 if memory is the binding constraint and traffic is light.
- **A WordPress install with a database, a small Laravel or Node app, a few bots on one box.** VM-1 at $5.99. This is the plan most people should buy.
- **The same workload with concurrent PHP workers or a heavier database, on a tight budget.** VM-1.5 at $6.95.
- **Multiple sites, a small team, staging plus production.** VM-2 at $11.99 for 4 GB and a second core.
- **Beyond that.** VM-3 and VM-4 make sense when you need four cores and more disk, but check the transfer allowance first, because 500 GB goes quickly on a busy multi-site box. VM-6 and VM-8 are for consolidating several workloads onto one machine; at $29.99 and up, compare against a dedicated server, keeping in mind that Evoxt's dedicated server offering is limited to Malaysia.

## What the monthly price actually includes

Billing is month-to-month by default, with prepayment options from 1 month up to 3 years, and account credit applied to invoices automatically. Evoxt states plainly that a $2.99 order costs $2.99, with no bandwidth overage charge and no CPU burst fee, so the listed price is the recurring price rather than an introductory rate that doubles on renewal.

Included on every plan, including the $2.99 tier: weekly offsite backups that survive a failure of Evoxt's own infrastructure, a 99.99% uptime SLA, IPv6 on every VM, private networking between your VMs without bandwidth charges, one-click rescue mode for a VM that won't boot, plus cloning, sub-accounts, VNC and floating IP management in the control panel.

Deployment speed has two published figures. Evoxt says a server is ready within 2.5 minutes of payment; third-party reviews put the fully automated process at under 5 minutes. Either way, you are not waiting on manual provisioning.

On refunds, review roundups report a 14-day money-back guarantee. Evoxt's own product pages don't state it in the material reviewed here, so confirm the current terms on the order page before you rely on it. If it holds, 14 days is long enough to run a real latency test from your own users' locations, which is the only way to know whether the Japan return path affects the carriers you care about.

Discount codes circulate widely for this brand. Third-party reviews in 2026 reported a recurring 40% code applicable to VM-1 and above, and the entry VM-0.5 plan is excluded from percentage discounts. No code appears on Evoxt's own site, so treat anything you find as unverified until the checkout page accepts it.

👉 [Start with VM-1 at $5.99/month and upgrade later in a few clicks](https://bit.ly/EvoXt)

## Who should not buy the Hong Kong node

Three cases where the honest answer is no.

**You push terabytes per month.** The premium tier allowance is roughly half a standard region's, and Hong Kong transit is the reason. A standard-region node will serve bulk transfer better at the same price.

**Your load is purely multi-threaded.** Video encoding, batch rendering and large-scale ML training want core count and memory bandwidth. Evoxt's emphasis on clock speed does not help there, and the per-core advantage is wasted.

**You need a dedicated server in Hong Kong.** Evoxt's dedicated offering is limited to Malaysia as of early 2026, so this product line cannot serve that requirement at any plan level.

## Before you check out

Four things to confirm on the order page, in this order:

1. **The transfer allowance for your specific plan.** It is not printed on the Hong Kong plan page, and the estimates above come from third-party sources.
2. **Whether your audience's carriers route well from Hong Kong.** If most of your traffic is Telecom or Mobile, the CN2 path is doing useful work. If it is Unicom-heavy in the north, test before committing to a year of prepayment.
3. **Your billing term.** Monthly keeps you flexible; long prepayment locks the price but also locks you in if the routing doesn't suit your users.
4. **The refund window and its conditions,** if you plan to rely on it.

The selection rule, in one line: a single site or app serving Chinese or Asian visitors goes on VM-1 at $5.99; a bot, tunnel or sandbox goes on VM-0.5 at $2.99; concurrency on a small budget goes on VM-1.5 at $6.95 for the second core.

👉 [Compare all nine Evoxt Hong Kong plans and deploy one](https://bit.ly/EvoXt)
