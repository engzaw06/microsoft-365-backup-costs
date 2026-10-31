# Microsoft 365 Backup: What the Native Service Costs, and When a $4/Month Alternative Makes More Sense

A quick answer before the details: Microsoft's native backup service covers Exchange Online, OneDrive, and SharePoint at **$0.15 per GB per month**, with free restores — and only tenant admins can operate it. For protecting tenant data at scale, that's a reasonable deal. For small teams whose data mostly lives on servers, PCs, and VMs, the per-GB bill climbs fast, and a managed backup route like Sharktech's Acronis Cyber Protect plan (from **$4/month with 200GB included**) costs dramatically less.

One clarification first, because search results blur it: "microsoft 365 backup" covers two different needs. One is Microsoft's own backup service for M365 tenant data. The other is the broader question — "how do I stop losing my company's data" — where servers, desktops, and archives matter more than tenant workloads. This guide deals with both, because the cheapest right answer depends on which one you actually meant.

> **快速选购速览**
> - Most small teams: Sharktech's Acronis Cyber Protect at $4/month (200GB included) covers Windows, Linux, macOS, and VMs with ransomware protection. 👉 [configure the Acronis backup plan and see your exact price](https://portal.sharktech.net/cart.php?a=add&pid=648&configoption%5B1862%5D=200&configoption%5B1863%5D=0&billingcycle=monthly&aff=1626)
> - Already running Veeam, Restic, or rclone: Sharktech's S3-compatible storage at $4.90/TB/month with no API request fees.
> - Protecting M365 tenant data specifically: the native service at $0.15/GB/month is the direct option.

## What Microsoft 365 Backup actually covers

The native service protects exactly three workload types: Exchange Online mailboxes (per user), OneDrive accounts, and SharePoint sites. Backup granularity follows that structure — a whole OneDrive account, a whole SharePoint site, a whole user mailbox. You can protect everything or select specific sites, accounts, and mailboxes by policy.

Recovery points are frequent. Exchange Online keeps one every 10 minutes across the full 52-week window. OneDrive and SharePoint keep a point every 10 minutes for the trailing two weeks, then weekly snapshots from 2 to 52 weeks back. Recovery windows are configurable at 3, 6, 12, or 24 months, defaulting to 1 year.

Microsoft's own FAQ is worth quoting on what the 10-minute figure means: *"We're not taking snapshots every 10 minutes."* Every 10 minutes, all changes in that window get stored as a new version. If ransomware encrypts one email per minute, you'd accumulate six backup copies per hour — and every deletion gets backed up regardless of schedule. The upshot for RPO: with Exchange, a data destruction event costs you roughly 10 minutes of data.

Restore speed is the main technical argument for the service. Microsoft quotes up to 1,000 average-sized objects at a median rate of 1–3 TB per hour, with Exchange restores running 100–500 items per mailbox per minute. The catch: that speed applies to express restore points. Ad-hoc restore points don't carry the guarantee.

Two limits matter before you commit:

1. **File-version restore for OneDrive and SharePoint is marked "coming soon"** in Microsoft's documentation. Today, restoring a OneDrive account or SharePoint site means rolling the whole thing back to a prior point — which overwrites all content and metadata since that point. Item-level restore exists only for Exchange (mail, contacts, calendar, tasks).
2. **Only tenant-level admins can enable backup or run restores.** End users have no ability to restore their own mailbox or OneDrive. In a five-person company where the admin is also doing payroll, every "I deleted my folder" ticket lands on one person.

## What it actually costs: the $0.15/GB math

The pricing model is $0.15 per GB per month on all protected data, restores free. The rate looks small until you multiply it out:

| Protected data | Monthly | Annual |
| --- | --- | --- |
| 100 GB | $15 | $180 |
| 200 GB | $30 | $360 |
| 500 GB | $75 | $900 |
| 1 TB | $150 | $1,800 |

A 10-person team where each person carries 50 GB of mail and files is already at the 500 GB row — $900 a year, recurring, for as long as the policy runs. There's no free tier and no included capacity. Most articles about the service stop at the per-GB rate; the annual totals are what actually decide small-business purchases.

Two details from the FAQ that rarely make it into comparison posts:

- **Retention and deletion policies don't flow through to the backups.** Your backup recovery window is governed solely by the backup policy, regardless of what your compliance retention says.
- **Removed or deleted users keep costing you money.** When a user is removed from the backup policy or deleted from Microsoft Entra ID, their OneDrive/Exchange backup is retained for one year from backup creation (SharePoint sites: 52 weeks from restore point creation). You're billed for that protected data during the retention period unless you actively manage it.

## How to enable it, and the deleted-user trap

Setup happens in the Microsoft 365 admin center, and only tenant-level admins can create and manage backup policies — end users can't toggle anything for their own accounts. PowerShell is supported through the Microsoft 365 Backup Storage Graph APIs. The service is also open to GCC government cloud organizations, and as of October 13, 2026, Microsoft added in-product Backup Activity Logs for monitoring backup policies and restore operations.

The deleted-user scenario is where things get fiddly. If a user was deleted within the past 30 days, they're soft-deleted: restore the user through the admin center first, then run normal restores. If you see the error *"The ExternalDirectoryObjectID of this inactive mailbox still exists,"* it just means the 30 days haven't passed — restore the user, don't touch the inactive mailbox flow. For permanently deleted users, only the "Recover an inactive mailbox" process preserves backup data; the "Restore an inactive mailbox" process does not. None of this is obvious until you're mid-incident.

Mailbox coverage has edges too: user mailboxes and shared mailboxes are supported, but room mailboxes and group mailboxes are not.

## When a third-party backup route makes more sense

The native service protects tenant workloads and does it well. What it doesn't do is protect your servers, office PCs, VMs, or archives — and at $0.15/GB/month it's priced for organizations where whole-tenant restore speed justifies a premium. If your actual situation is "I can't afford to lose my files and I don't want a growing monthly bill," other routes cost far less for the machines-and-files problem.

Sharktech — a hosting provider around 20 years old with data centers in Los Angeles, Chicago, Denver, and Amsterdam — sells backup through two separate products, and it helps to keep them straight.

### Acronis Cyber Protect: managed backup from $4/month

Sharktech has a long-standing partnership with Acronis and resells Cyber Protect as a hosted service. It covers physical servers, virtual machines, and cloud workloads across supported Windows, Linux, and macOS environments, with ransomware protection (real-time threat detection and automated response), anti-malware, patch management, encryption, deduplication, and compression built in. You manage schedules, retention, and monitoring from the Acronis web console, and Sharktech's support team is available 24/7.

Restores are granular — a single file, a set of folders, or an entire machine. Sharktech's own product page recommends making test restores part of routine operations, and deciding in advance what gets restored first, how much history you keep, and who owns the recovery process. That advice applies no matter who you buy backup from.

The full plan lineup, with the billing-cycle tradeoffs most reviews skip:

| 套餐 | 容量 | 价格 | 超量费 | 计费周期 |
| --- | --- | --- | --- | --- |
| Acronis Cyber Protect 月付 | 200GB | $4.00/月 | $0.02/GB | 月付 |
| Acronis Cyber Protect 季付 | 200GB | $8.00/3 个月 | $0.04/GB | 季付 |
| Acronis Cyber Protect 半年付 | 200GB | $12.00/6 个月 | $0.06/GB | 半年付 |
| Acronis Cyber Protect 年付 | 200GB | $24.00/年 | $0.12/GB | 年付 |
| S3 Object Storage | 1TB 起 | $4.90/TB/月 | 含 1TB 流量 | 月付 |
| File Sync & Share（附加） | — | $0.03/GB/月（基础档 $0） | — | 月付 |

The counterintuitive part: the **monthly plan has the cheapest overage at $0.02/GB**, while the annual plan has the lowest base (about $2/month equivalent) but the steepest overage at $0.12/GB. If you're likely to blow past 200GB, monthly billing actually keeps your marginal cost down. Annual only wins if you stay close to the included 200GB. Your final recurring price depends on the capacity and options you pick at checkout — the portal shows the complete price before you confirm, so nothing gets tacked on afterward.

👉 [configure the Acronis monthly plan at Sharktech](https://portal.sharktech.net/cart.php?a=add&pid=648&configoption%5B1862%5D=200&configoption%5B1863%5D=0&billingcycle=monthly&aff=1626) — it's the most flexible starting point if you're still sizing your backup needs.

### S3-compatible storage: $4.90/TB/month

If you already run backup software — Veeam, Restic, Borg, Duplicacy, or rclone — Sharktech's S3-compatible object storage is raw capacity with no API request fees, no storage tiers, and no egress charges beyond the included 1TB of monthly traffic. Their line about it: *"Storage and bandwidth are the only two line items on the invoice."* At $4.90/TB it undercuts Backblaze B2 (about $6.95/TB) and Wasabi, with no long-term contract. It works with Jenkins, GitLab, Terraform, MinIO, and anything else that speaks the S3 API, with the storage cluster redundant across Sharktech's data centers.

## Native Microsoft 365 Backup vs. the Sharktech route

Since the two protect different scopes, the useful comparison is by scenario:

- **M365 tenant data** (mailboxes, OneDrive, SharePoint): only the native service covers this directly, at $0.15/GB/month. 200GB of tenant data = $30/month.
- **Servers, PCs, and VMs**: Acronis through Sharktech, from $4/month. The native service doesn't touch this at all.
- **Bulk storage for your own backup tooling**: S3 at $4.90/TB/month. Microsoft's pricing model isn't built for this use case.

A 30x price gap between $150/month (1TB under Microsoft) and $4.90/month (1TB of S3 storage) explains why plenty of small teams skip the native service and handle backup themselves — with the understanding that Acronis and S3 storage protect machines and files, not tenant workloads. Many organizations end up running both: native backup for the tenant, Acronis or S3 for everything else.

## FAQ

**Does Microsoft 365 already include backup?**
No. OneDrive, SharePoint, and Exchange have multiple physically redundant copies of your data for infrastructure resilience, but redundancy protects against hardware failure. It doesn't give you point-in-time recovery after deletion, ransomware encryption, or malicious overwrites — that's what the paid Microsoft 365 Backup service adds at $0.15/GB/month.

**Are the backups immutable?**
Yes. Backups are append-only and immutable — old backup blocks can't be modified, and the backups can't be deleted except by a Backup admin through product offboarding. An attacker who compromises a user account can't rewrite backup history.

**Can users restore their own files?**
No. Only tenant-level admins can create and manage backups and restores. End users have no ability to enable backup or restore their own mailbox, OneDrive, or sites.

**Which mailboxes are supported?**
User mailboxes and shared mailboxes. Room mailboxes, group mailboxes, and other Exchange recipient types aren't supported.

**How fast are restores?**
Microsoft quotes a median rate of up to 1–3 TB per hour for up to 1,000 average-sized objects, and 100–500 items per mailbox per minute for Exchange. Express restore points deliver the high speeds; ad-hoc restore points don't carry the guarantee.

## Which route should you pick

Protecting M365 tenant data and your budget absorbs $0.15/GB/month? Enable the native service in the admin center and set your recovery window — just assign someone to own restores, since users can't self-serve.

Small team, mostly need to stop losing data without a growing bill? Start with Sharktech's Acronis plan at $4/month: 200GB included, ransomware protection in the box, no long-term contract, and checkout shows your full recurring price before you commit.

Already running Veeam, Restic, or rclone? Point them at the S3 storage at $4.90/TB and keep your backup stack exactly as it is.

Before ordering either way, do the two things Sharktech's own documentation recommends: write down what you'd restore first in a real incident, and decide who's responsible for pressing the button. 👉 [set up Acronis Cyber Protect backup at Sharktech](https://portal.sharktech.net/cart.php?a=add&pid=648&configoption%5B1862%5D=200&configoption%5B1863%5D=0&billingcycle=monthly&aff=1626) — the sooner backups exist, the sooner those questions stop being hypothetical.
