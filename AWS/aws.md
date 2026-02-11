# TOP 10 AWS INTERVIEW QUESTIONS

1. Explain IAM Service.
```
Answer.

# AWS IAM — Identity and Access Management

# What is IAM?

**IAM (Identity and Access Management)** is the AWS service used to securely **manage who** can access AWS, **what** they can access, and **what actions** they can perform on AWS resources.

# Components of IAM

* **Users** — Individual identities (people or applications) with credentials for AWS access.
* **Groups** — Collections of users; permissions attached to a group are inherited by its members.
* **Roles** — Assumable identities that provide temporary credentials (used by AWS services or for cross-account/federated access).
* **Policies** — Permission documents (Allow/Deny) that specify actions, resources, and optional conditions; attached to users, groups, or roles.
* **Identity Providers / Federation** — External IdPs (SAML/OIDC) that allow federated or single-sign-on access without creating IAM users.

# WHY we use IAM

* **Secure access control:** Ensures only authorized identities can access resources.
* **Fine-grained permissions:** Grant precise actions on specific resources.
* **Multi-user management:** Safely manage and separate access for teams.
* **Service-to-service access without long-term credentials:** Roles provide temporary credentials for AWS services.
* **Auditability:** Enables tracking of who did what when (integrates with AWS auditing services).

# Console steps (create user, create group, attach policy to group, add user to group, console & programmatic access)

## 1. Create a user

1. Sign in to the AWS Console and open **IAM**.
2. Go to **Users** → **Add users**.
3. Enter the **User name**.
4. Select access type(s): **AWS Management Console access** (console) and/or **Programmatic access** (API/CLI).
5. Click **Next** and complete creation.

## 2. Create a group

1. In IAM, go to **User groups** (or **Groups**) → **Create group**.
2. Enter **Group name**.
3. Continue to attach permissions in the next step or finish and attach later.
4. Click **Create group**.

## 3. Attach policy to the group

1. IAM → **User groups** → select the group.
2. Choose **Permissions** → **Add permissions** → **Attach policies directly** (or use a managed policy).
3. Select the desired policy (e.g., read-only or specific service access).
4. Click **Add permissions** (or **Attach policy**).

## 4. Add user to the group

1. IAM → **Users** → select the user.
2. Open the **Groups** or **Add user to groups** option.
3. Select the target group(s).
4. Click **Add to group** (or **Save**).

* The user immediately inherits the group’s permissions.

## 5. Console access vs Programmatic access (what each provides)

* **Console access:** Web-based login using username and password (optionally with MFA). Used for GUI operations in the AWS Management Console.
* **Programmatic access:** Access keys (Access Key ID and Secret Access Key) used by SDKs, APIs, and CLI for automated or scripted interactions.


```
2. Difference between IAM Roles and Policies

```
Answer.

IAM Role — What it is

An IAM Role is an AWS identity you assume to get temporary security credentials. Roles themselves don’t hold long-term passwords or access keys — they provide a short-lived identity that an AWS service, another account, or a federated user can use to act with specific permissions.

When to use a Role

When an AWS service (for example an EC2 instance or a Lambda function) needs to access other AWS resources without storing credentials.

When you want cross-account or federated users to perform actions temporarily.

When you need temporary, limited-lifetime credentials to reduce risk from long-lived keys.

How to create a Role (Console steps)

Sign in to the AWS Console and open IAM.

Go to Roles → Create role.

Choose the trusted entity that will assume the role (e.g., an AWS service, another AWS account, or a federated identity).

(Optional) Select a use case if suggested.

Attach permission policies that define what the role can do (choose existing managed policy or a custom one you created).

Add tags if needed, give the role a Name and Description, then click Create role.

After creation, attach the role to the consuming resource (for example: assign it to an EC2 instance via “Modify IAM role” on that instance, or select it when configuring a Lambda function).

IAM Policy — What it is

An IAM Policy is a permission document that explicitly states which actions are allowed or denied on which resources and under what conditions. Policies are the rules — they do not by themselves act; they must be attached to an identity (user, group, or role) or to a resource.

When to use a Policy

Whenever you need to define access rules (what actions are permitted and on which resources).

When you want a reusable permission set that can be attached to multiple users, groups, or roles.

When you need to attach access control directly to a resource (resource policies).

How to create a Policy (Console steps)

Sign in to the AWS Console and open IAM.

Go to Policies → Create policy.

Use the Visual editor to select the Service, pick Actions, specify Resources, and add Conditions as required.

Click Next: Tags (optional) → Next: Review.

Enter a Name and Description for the policy → Create policy.

The policy appears under Policies and can now be attached to users, groups, or roles.

Attaching a Policy (how to bind rules to identities) — Console steps

Open IAM → choose Users, User groups, or Roles depending where you want the permission.

Select the target identity → go to the Permissions tab → Add permissions (or Attach policies).

Choose Attach policies directly, select the policy you created (or an AWS managed policy), then Add permissions / Attach policy.

If you attach the policy to a group, all users in that group inherit the permissions immediately.

If you attach to a role, any principal that assumes that role will receive the permissions in their temporary credentials.
```

3. Explain different storage classes of S3
```
Answer.

WHAT it is

S3 storage classes are the different cost-and-performance tiers inside Amazon S3 that determine how your objects are stored, how quickly they can be retrieved, and how much they cost.
Pick a storage class to match an object’s access pattern (hot, cool, archival), resiliency needs, and cost tolerance.

TYPES & WHY we use each

S3 Standard — What: default, multi-AZ, low-latency storage for frequently accessed data.
Why: use for hot data (web content, active workloads) where availability and immediate access matter.

S3 Intelligent-Tiering — What: automatic tiering between frequent/infrequent (and archive tiers) based on access patterns.
Why: use when access patterns are unknown or change over time — it optimizes cost automatically.

S3 Standard-IA (Infrequent Access) — What: multi-AZ, lower storage cost, retrieval fees apply.
Why: use for long-lived but infrequently accessed data (backups, older logs) where you still need rapid retrieval.

S3 One Zone-IA — What: like Standard-IA but stored in a single AZ.
Why: use to save cost for infrequently accessed data where AZ-level resiliency is acceptable (recreatable data).

S3 Express One Zone — What: single-AZ, ultra-low latency/high throughput single-digit ms access.
Why: use for latency-sensitive workloads co-located with compute in same AZ where single-AZ risk is acceptable.

S3 Glacier Instant Retrieval — What: archive-class with millisecond retrieval.
Why: use for archive data that occasionally needs immediate access (e.g., reference datasets).

S3 Glacier Flexible Retrieval — What: archival storage with configurable retrieval speeds (expedited/standard/bulk).
Why: use for long-term archives where retrieval can tolerate minutes–hours and you want lower storage cost.

S3 Glacier Deep Archive — What: lowest-cost archival tier with hours-level retrieval.
Why: use for rarely accessed regulatory or preservation archives where retrieval speed is not important.

S3 on Outposts / Local classes — What: S3-compatible storage for on-premises Outposts.
Why: use when data must remain on-premises or meet locality constraints.
```
4. Explain different EC2 Instance Type
```
Answer.

WHAT it is

Amazon EC2 (Elastic Compute Cloud) provides resizable compute capacity in the cloud. An EC2 instance type is a template that defines the vCPU, memory, storage options, network performance, and accelerator capability available to an instance — i.e., the shape of the virtual machine you run your workloads on.

One-line interview answer:

Instance types define the hardware profile (CPU, RAM, disk, network, accelerators) you choose to match your workload’s CPU, memory, storage, or GPU needs.

TYPES & WHY WE USE EACH

Below are the main instance families you’ll see in interviews and real designs, with why/when to pick them.

1. General Purpose (e.g., T, M families)

What: Balanced CPU / memory / network.

Why: Default for most applications — web servers, small databases, dev/test, microservices.

When to use: When workload has moderate, balanced resource needs or unpredictable patterns (T is burstable for very spiky loads).

2. Compute-Optimized (e.g., C family)

What: High CPU-to-memory ratio.

Why: CPU-bound workloads (batch processing, high-performance web servers, gaming servers, compute-heavy microservices).

When to use: High single-thread or multi-thread compute demand.

3. Memory-Optimized (e.g., R, X, z families)

What: Large memory footprint per vCPU.

Why: In-memory caches, real-time big-data analytics, high-performance databases (Redis, SAP HANA), JVM-based apps.

When to use: Workloads needing lots of RAM relative to CPU.

4. Storage-Optimized (e.g., I, D, H families)

What: High IOPS, high throughput, local NVMe or HDD instance store options.

Why: Transactional databases, data-warehousing, log processing, file systems requiring low-latency local storage.

When to use: When you need very high disk I/O or ephemeral local storage.

5. Accelerated Computing (GPU / FPGA) (e.g., P, G, F, Inf families)

What: GPUs, FPGAs, or Inferentia chips for ML training/inference, video encoding, or specialized acceleration.

Why: Machine learning training (P/G), real-time inference (Inf), or hardware acceleration for specific workloads.

When to use: Deep learning, HPC, graphics rendering, hardware-accelerated workloads.
```
5. Explain LoadBalancers in AWS
6. Explain AutoSclaing Group in AWS
7. What is VPC Peering?
8. Explain NAT Gateway in details
9. Explain NAT Gateway vs NAT Instance
10. Difference between NACL vs Security Group