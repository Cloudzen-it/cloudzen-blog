---
title: "How We Cut AWS Costs by 60% by Migrating a SaaS Platform to Serverless — With Zero Downtime"
description: "A real-world case study of migrating a B2B SaaS platform from always-on containers to serverless architecture. $20K annual savings, automated security, and faster deployments."
pubDate: "2026-03-10"
heroImage: "../../assets/blog-placeholder-3.jpg"
---

A B2B SaaS startup came to us with a familiar problem: their cloud bill was growing faster than their revenue.

They had a solid product — a logistics management platform serving ~500 daily users across Europe. The engineering team had built it fast and shipped it on time. But the infrastructure underneath was designed for launch day, not for the long run. And it was quietly bleeding money every month.

Six weeks later, we had cut their AWS costs by 60%, reduced deployment time from 25 minutes to under 3, and introduced automated security scanning on every code change — all with zero downtime during the migration.

Here's what we did, why it worked, and what it means for your business.

## The Problem: Paying for Servers That Do Nothing

The platform was running on **AWS ECS Fargate** — essentially always-on containers behind a traditional load balancer. This is a perfectly reasonable setup for many workloads. But for this client, the numbers told a different story:

- **Average CPU utilization was 8%.** They were paying for 100% of capacity but using less than a tenth.
- **80% of traffic came during business hours.** Evenings, nights, and weekends? The servers sat idle, burning cash.
- **The load balancer alone cost $400/month** — a fixed cost regardless of whether anyone was using the platform.
- **The database was 3x bigger than necessary.** It had been provisioned for projected growth that hadn't materialized yet.

The total? **$2,800 per month** — or roughly **$34,000 per year** — for a platform that could have been running on a fraction of that.

This isn't unusual. We see it in almost every startup that scales infrastructure based on projections rather than actual usage data.

## The Solution: Pay Only for What You Use

Instead of running servers around the clock, we migrated the entire platform to a **serverless architecture** — services that automatically scale up when users need them and scale to zero when they don't.

![Architecture comparison: before and after migration](/images/blog/architecture-before-after.svg)

The key change: replacing always-on containers and the load balancer with **AWS Lambda functions** behind **CloudFront** (a global content delivery network) and **API Gateway**. Each microservice — documents, configuration, PDF generation, shipments — became an independent function that only runs when called.

The result? No traffic means no cost. Peak traffic means automatic scaling. No capacity planning required.

## What We Actually Delivered

### 1. Modular Infrastructure-as-Code

The client's original infrastructure was defined in a single 800-line configuration file. Changing anything was risky — a DNS update could accidentally affect the database. No one fully understood what the file did, and only one person knew how to deploy it.

We replaced this with **16 independent, reusable modules** — each managing a specific piece of infrastructure (networking, database, authentication, each microservice). Every module can be turned on or off per environment with a single line change.

What this means for the business:
- **Any engineer can deploy** — not just the one who wrote the original config
- **Changes are isolated** — updating the PDF service can't accidentally break authentication
- **New environments take minutes, not days** — spin up a staging copy for a client demo in under 10 minutes

### 2. Automated Security on Every Change

Before our engagement, the infrastructure had **never been security-audited**. Not because the team didn't care — but because manual audits are expensive and easy to postpone.

We integrated automated security scanning into the deployment pipeline. Every code change is automatically checked against **200+ security rules** — encryption standards, access policies, network exposure, compliance benchmarks.

During the migration alone, the scanner caught:
- An **unencrypted storage bucket** that could have exposed client documents
- An **overly permissive access policy** that granted broader permissions than intended

Both would have reached production undetected without automated scanning.

### 3. CI/CD for Infrastructure

Infrastructure changes now follow the same rigorous process as application code:

1. **An engineer proposes a change** (e.g., increase database capacity)
2. **Automated validation** checks syntax, security, and compliance
3. **A preview shows exactly what will change** — before anything is applied
4. **Approval is required** — no one can accidentally apply changes
5. **The change is applied** with full audit trail

No more deploying from someone's laptop. No more "it works on my machine." The entire infrastructure is version-controlled, reviewed, and auditable.

## The Results

![Key metrics dashboard showing improvements across cost, speed, security, and recovery](/images/blog/results-dashboard.svg)

The numbers speak for themselves:

- **AWS costs dropped from $2,800 to $1,120/month** — a 60% reduction, saving over **$20,000 annually**
- **Deployments went from 25 minutes to 2.5 minutes** — a 90% improvement in time-to-production
- **Security findings went from "unknown" to zero critical issues** — with continuous automated scanning
- **Recovery time dropped from ~45 minutes to ~8 minutes** — the entire stack can be redeployed from code
- **Environment drift: eliminated** — staging and production are guaranteed to match

### Where Did the Savings Come From?

![Detailed cost breakdown before and after migration](/images/blog/cost-breakdown.svg)

Three main areas:

1. **Eliminated idle compute ($1,220 saved).** Lambda functions scale to zero. No traffic at 3 AM means no cost at 3 AM. The always-on containers and the $400/month load balancer were replaced by API Gateway and CloudFront at ~$60/month.

2. **Right-sized the database ($470 saved).** With proper monitoring in place, we identified the database was provisioned for 3x the actual workload and downsized it without any performance impact.

3. **Reduced operational overhead.** While harder to quantify, the modular infrastructure eliminated hours of manual work per month — no more hand-editing configurations, no more "emergency" deploys from a specific laptop.

## What We Kept the Same

Not everything changed — and that's by design. We kept the same:

- **Application code** — the NestJS microservices were containerized and deployed to Lambda without rewriting
- **Database** — same PostgreSQL (Aurora), just right-sized
- **Authentication** — same Cognito setup, no impact on end users
- **API structure** — same endpoints, same behavior. The frontend didn't need a single change

**Zero user-facing impact.** We ran both architectures in parallel during the transition and switched over only after validating identical behavior.

## Honest Trade-offs

We believe in transparency, so here's what we'd want you to know:

**Cold starts exist.** Serverless functions that haven't been called recently take slightly longer to respond on the first request. Worst-case latency increased from 340ms to ~520ms. For a B2B platform where users interact with dashboards and forms, this is imperceptible. For a real-time trading platform, it might not be acceptable.

**More infrastructure code overall.** We went from 800 lines to ~2,200 lines across 16 modules. But those 2,200 lines are organized, documented, and testable — while the original 800 lines were a single fragile file that no one wanted to touch.

**Serverless isn't always cheaper.** If your platform has sustained, consistent traffic 24/7, always-on containers may actually cost less. The savings here came specifically because traffic was bursty and predictable. We always analyze actual usage patterns before recommending a migration.

## Is This Right for Your Business?

This approach works particularly well if:

- Your **cloud costs feel disproportionate** to your user base or revenue
- Your **traffic is variable** — business hours only, seasonal peaks, or event-driven
- **Only one or two people** can deploy infrastructure changes
- You've **never had a security audit** of your cloud setup
- You're spending engineering time on **infrastructure maintenance** instead of product development

It may not be the right fit if:

- You have **consistent, high-volume traffic** around the clock
- Your application requires **persistent connections** (WebSockets, streaming)
- You need **sub-50ms latency** on every request without exception

The right architecture depends on your specific situation. We'd rather tell you serverless isn't the answer than sell you a migration you don't need.

## Conclusion

Cloud costs are a business problem, not just an engineering problem. Every dollar spent on idle servers is a dollar not spent on product development, marketing, or hiring.

The real value of this engagement wasn't just the $20,000 in annual savings — though the CFO certainly appreciated that. It was that the platform went from fragile, person-dependent infrastructure to a system that any engineer can understand, modify, and deploy with confidence.

If your infrastructure feels like a black box that only one person understands, or if your AWS bill makes you wince every month, there's probably a better way. We'd be happy to take a look.

---

<div style="display: flex; align-items: flex-start; gap: 20px; margin-top: 2em; padding: 24px; border: 1px solid #2a2a3a; border-radius: 12px; background: #13131a;">
  <img src="https://cloudzendevops.com/images/back/about__wrap/dan-jeshkov.jpg" alt="Dan Jeshkov" style="width: 80px; height: 80px; border-radius: 50%; object-fit: cover; flex-shrink: 0;" />
  <div>
    <strong style="font-size: 1.05em;">Dan Jeshkov</strong><br/>
    <span style="color: #888; font-size: 0.9em;">CEO at <a href="https://cloudzendevops.com">Cloudzen</a></span>
    <p style="margin-top: 8px; font-size: 0.95em; color: #aaa;">At Cloudzen, we treat your business as our own — building secure, reliable, and cost-efficient cloud infrastructure for companies that want to focus on their product, not their servers. <a href="https://linkedin.com/in/daniel-jeshkov-devops/">Let's talk</a> about your infrastructure challenges.</p>
  </div>
</div>
