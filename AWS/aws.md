## 10 AWS Questions

---

### 1️⃣ Explain IAM Service (AWS IAM)

1️⃣ **WHAT**  
- **Definition**: IAM is the AWS service that controls who can access your AWS account and what they can do.  
- **One-line**: **IAM is AWS’s identity and permissions system that manages users, roles, and their access to AWS resources.**

2️⃣ **TYPES**  
- **IAM Users**  
  - Individual identities (person/app) with long-term credentials (password, access keys).  
  - **Use when** a specific human or app directly needs an AWS identity.  
- **IAM Groups**  
  - Logical collection of users that share the same permissions.  
  - **Use when** you want to manage many users with the same access (e.g., “Developers”, “Admins”).  
- **IAM Roles**  
  - Identities that are assumed to get temporary credentials.  
  - Used by AWS services (EC2, Lambda), other AWS accounts, or SSO/federated users.  
  - **Use when** you want access without long-lived keys.  
- **IAM Policies**  
  - Permission documents defining **Allow/Deny** for actions on resources.  
  - **Use when** you want to describe “who can do what on which resources”.  
- **Identity Providers / Federation**  
  - Connects external IdPs (Okta, Azure AD, Google, corporate AD) to AWS.  
  - **Use when** users log in with corporate credentials instead of separate IAM users.  

3️⃣ **WHY**  
- **Central control** of access across many services and accounts.  
- Enables **least privilege**, separating dev/qa/prod access and reducing blast radius in production.  
- Removes the need to **hardcode credentials** by using roles and temporary credentials, which is critical for security and compliance.  

4️⃣ **HOW**  
- **Conceptually**  
  - You define **identities** (users, roles, groups), then attach **policies** that specify allowed actions on AWS resources.  
  - When someone logs in or a service assumes a role, AWS checks their **effective policies** before allowing each API call.  
- **In real projects**  
  - Create groups (e.g., `DevReadOnly`, `DevAdmin`), attach policies, and add users to groups.  
  - Create roles for EC2/Lambda/EKS etc. to access S3, DynamoDB, RDS, etc., instead of embedding keys.  
- **Console flow (typical pattern)**  
  - Create **user** → put user into a **group** → attach **policy** to group.  
  - Create **role** for a service (e.g., EC2 role to read from S3) → attach policy → assign role to the service during resource creation or via modification.  

---

### 2️⃣ Difference between IAM Roles and Policies

1️⃣ **WHAT**  
- **IAM Role**: An AWS identity that can be assumed to get temporary credentials.  
- **IAM Policy**: A permission document that defines what actions are allowed/denied on which resources.  
- **One-line**: **Roles are “who you are temporarily”; policies are “what you’re allowed to do”.**  

2️⃣ **TYPES**  
- **Roles (identity side)**  
  - **Service roles**: Assumed by AWS services (e.g., EC2 role, Lambda execution role).  
  - **Cross-account roles**: Assumed from another AWS account.  
  - **Federated/SSO roles**: Assumed by external IdP users.  
- **Policies (permissions side)**  
  - **Identity policies**: Attached to users, groups, or roles.  
  - **Resource policies**: Attached directly to resources (e.g., S3 bucket policy, KMS key policy).  
  - **AWS managed policies**: Predefined by AWS.  
  - **Customer managed policies**: Custom policies you define and reuse.  

3️⃣ **WHY**  
- Roles exist so that **no long-term credentials** need to live on servers, code, or laptops.  
- Policies exist so that **permissions are explicit and reusable**, and can be centrally managed, audited, and updated.  
- Separating **identity** (role/user) from **permissions** (policy) gives flexibility: same policy can be attached to many identities; same identity can have multiple policies.  

4️⃣ **HOW**  
- **Conceptually**  
  - A role is **empty by itself**; it becomes powerful only when **policies** are attached.  
  - A principal (user/service) **assumes a role** → gets temporary credentials that include permissions from the role’s attached policies.  
- **In real projects**  
  - You create a role for each workload (e.g., `app-ec2-role`, `lambda-orders-role`) and attach policies that grant minimal permissions required.  
  - You create reusable customer-managed policies (e.g., `ReadOnlyS3LogsPolicy`) and attach them to multiple roles.  
- **Console thinking**  
  - When you **Create Role**, you choose **who will assume it** (EC2, another account, SSO) and then attach policies.  
  - When you **Create Policy**, you design what that policy **allows/denies**, then later attach it to users, groups, or roles as needed.  

---

### 3️⃣ Explain different storage classes of S3

1️⃣ **WHAT**  
- **Definition**: S3 storage classes are cost-performance tiers for S3 objects that control durability, availability, access speed, and price.  
- **One-line**: **S3 storage classes let you choose the right cost vs. access pattern for each object.**  

2️⃣ **TYPES**  
- **S3 Standard**  
  - Frequent-access, multi-AZ, high durability, low latency.  
  - **Use for**: Hot data—web assets, active application data, frequently accessed files.  
- **S3 Intelligent-Tiering**  
  - Automatically moves objects between frequent/infrequent/archival tiers based on actual access.  
  - **Use for**: Data with **unknown or changing** access patterns; you want savings without manually tuning classes.  
- **S3 Standard-IA (Infrequent Access)**  
  - Multi-AZ; lower storage cost but retrieval fee. Millisecond access when needed.  
  - **Use for**: Long-lived but rarely read data (backups, older reports) that still needs quick retrieval when accessed.  
- **S3 One Zone-IA**  
  - Like Standard-IA but stored in **one AZ only**.  
  - **Use for**: Re-creatable data or non-critical backups where you accept AZ failure risk to save cost.  
- **S3 Express One Zone**  
  - Single-AZ, ultra-low latency and high throughput.  
  - **Use for**: Latency-sensitive workloads (high-QPS applications) co-located in the same AZ.  
- **S3 Glacier Instant Retrieval**  
  - Archive tier with instant retrieval, lower cost than Standard-IA.  
  - **Use for**: Archive data that is rarely accessed but must be available immediately when needed.  
- **S3 Glacier Flexible Retrieval**  
  - Archive storage with different retrieval speeds (minutes to hours).  
  - **Use for**: Long-term backups and archives where retrieval time is not critical.  
- **S3 Glacier Deep Archive**  
  - Cheapest archival class, retrieval takes hours.  
  - **Use for**: Compliance/long-term retention where you almost never access data.  
- **S3 on Outposts / Local options**  
  - S3-like storage on AWS Outposts hardware on-prem.  
  - **Use for**: Data residency or low-latency on-prem workloads.  

3️⃣ **WHY**  
- Different workloads have very different **access patterns**; storing everything in Standard is convenient but expensive at scale.  
- Storage classes let you **minimize cost** while still meeting RTO/RPO, performance, and durability needs.  
- In production, this is how you make **terabytes/petabytes** of data economically sustainable.  

4️⃣ **HOW**  
- **Conceptually**  
  - Each object in a bucket has a **storage class attribute**; AWS bills based on that class and the object’s lifecycle.  
  - Lifecycle rules can automatically transition objects between classes over time (e.g., Standard → IA → Glacier).  
- **In real projects**  
  - During application design, you classify data (hot, warm, cold, archive) and map each to a storage class.  
  - You often start in Standard or Intelligent-Tiering and use lifecycle policies for long-term optimization.  
- **Console flow examples**  
  - When uploading an object via console, you select the **Storage class** in the upload options.  
  - In `S3 → bucket → Management → Lifecycle rules`, you create rules like: “After 30 days move to Standard-IA, after 90 days move to Glacier Deep Archive”.  

---

### 4️⃣ Explain different EC2 Instance Types

1️⃣ **WHAT**  
- **Definition**: EC2 instance types are hardware templates that define vCPUs, RAM, storage options, network bandwidth, and accelerators for your virtual servers.  
- **One-line**: **An instance type is the “size and shape” of the VM you run your workloads on.**  

2️⃣ **TYPES**  
- **General Purpose (e.g., T, M)**  
  - Balanced CPU, memory, and networking.  
  - **Use for**: Web servers, microservices, small/medium databases, dev/test. T is burstable; M is steady.  
- **Compute Optimized (e.g., C)**  
  - More CPU relative to memory.  
  - **Use for**: CPU-heavy workloads—batch processing, game servers, high-performance web servers, analytics engines.  
- **Memory Optimized (e.g., R, X, z)**  
  - More RAM relative to CPU.  
  - **Use for**: In-memory databases/caches (Redis, SAP HANA), big in-memory analytics, heavy JVM apps.  
- **Storage Optimized (e.g., I, D, H)**  
  - High IOPS and throughput; often NVMe SSD instance store.  
  - **Use for**: High-performance databases, search clusters, log analytics, file systems requiring fast local disk.  
- **Accelerated Computing (e.g., P, G, F, Inf)**  
  - GPUs, FPGAs, or special chips.  
  - **Use for**: ML training (P), inference (Inf), graphics rendering (G), or custom hardware acceleration (F).  

3️⃣ **WHY**  
- Different applications need different **resource mixes** (CPU vs RAM vs IO vs GPU).  
- Instance families allow you to **right-size cost and performance** instead of overpaying for unnecessary resources.  
- In production, this drives both **performance reliability** and **cost optimization**.  

4️⃣ **HOW**  
- **Conceptually**  
  - You choose a **family** (what it’s optimized for) + **generation** (e.g., `m5`, `c7g`) + **size** (e.g., `large`, `xlarge`) to match workload needs.  
- **In real projects**  
  - Start with General Purpose for most apps, then monitor CPU/RAM/IO and adjust to compute-, memory-, or storage-optimized families as needed.  
- **Console flow**  
  - In **EC2 → Launch instance**, you pick an instance type from the list with visible vCPU/RAM → select disk & networking → launch and test, then iterate based on CloudWatch metrics.  

---

### 5️⃣ Explain Load Balancers in AWS

1️⃣ **WHAT**  
- **Definition**: An AWS load balancer distributes incoming traffic across multiple targets (EC2, containers, IPs) to improve availability and scalability.  
- **One-line**: **AWS load balancers spread traffic across multiple instances so your application is highly available and scalable.**  

2️⃣ **TYPES**  
- **Application Load Balancer (ALB)**  
  - Layer 7 (HTTP/HTTPS), supports path-based, host-based routing, WebSockets.  
  - **Use for**: Modern HTTP/HTTPS microservices, APIs, routing `/api` vs `/app`, blue-green/canary routing.  
- **Network Load Balancer (NLB)**  
  - Layer 4 (TCP/UDP), ultra-high performance, static IP support.  
  - **Use for**: High-throughput, low-latency, non-HTTP protocols, or when you need static IP per AZ.  
- **Gateway Load Balancer (GWLB)**  
  - For deploying and scaling third-party virtual appliances (firewalls, IDS/IPS).  
  - **Use for**: Inserting security or inspection appliances transparently in network paths.  
- **Classic Load Balancer (CLB)**  
  - Older generation; basic L4/L7, mostly legacy.  
  - **Use for**: Existing older workloads; generally avoid for new designs—prefer ALB/NLB.  

3️⃣ **WHY**  
- You don’t want a **single EC2 instance** to be a single point of failure.  
- Load balancers handle **health checks, failover, and scaling**, allowing you to add/remove instances without changing DNS/clients.  
- They integrate with **SSL termination, WAF, and Auto Scaling Groups** to build resilient production architectures.  

4️⃣ **HOW**  
- **Conceptually**  
  - Clients send requests to the load balancer → LB distributes to **healthy targets** in one or more target groups based on routing rules.  
  - Health checks continuously validate if targets are healthy; unhealthy ones are automatically skipped.  
- **In real projects**  
  - You place an ALB in front of app servers in **multiple AZs**, connect it to an Auto Scaling Group, and let it handle scaling and failover.  
  - For high-performance TCP services (e.g., game servers), you use NLB for better performance and static IPs.  
- **Console flow**  
  - In **EC2 → Load Balancers**, create ALB/NLB → define listeners (e.g., HTTP:80, HTTPS:443) → create target groups and register EC2/targets → configure health checks → optionally attach to an ASG.  

---

### 6️⃣ Explain Auto Scaling Group in AWS

1️⃣ **WHAT**  
- **Definition**: An Auto Scaling Group (ASG) automatically adjusts the number of EC2 instances based on demand and health.  
- **One-line**: **An ASG is a group of EC2 instances that scales out and in automatically to match load and maintain availability.**  

2️⃣ **TYPES** (Scaling approaches)  
- **Manual scaling**: You adjust desired capacity yourself.  
- **Scheduled scaling**: Scale based on time (e.g., weekday vs weekend traffic).  
- **Dynamic scaling**: Scale based on metrics (CPU, requests, custom CloudWatch metrics).  
- **Predictive scaling**: Uses ML to forecast and pre-scale capacity.  

3️⃣ **WHY**  
- Traffic patterns change; using fixed instance counts means either **overpaying** or **risking outages**.  
- ASG keeps the number of instances within **min/max limits** and automatically replaces unhealthy instances, improving resilience.  
- It is central to building **elastic, cost-efficient** architectures.  

4️⃣ **HOW**  
- **Conceptually**  
  - ASG is defined by a **launch template/configuration** (AMI, instance type, security groups, etc.), **min/max/desired capacity**, and **scaling policies**.  
  - It monitors metrics/health checks and adjusts the number of instances accordingly.  
- **In real projects**  
  - You define ASG across **multiple AZs**, attach it behind an ALB, and set target tracking (e.g., keep average CPU at 50%).  
- **Console flow**  
  - In **EC2 → Auto Scaling Groups**, create ASG → select launch template, VPC & subnets → attach to a load balancer if needed → set min/max/desired capacity → configure scaling policies (target tracking, step, scheduled) → review and create.  

---

### 7️⃣ What is VPC Peering?

1️⃣ **WHAT**  
- **Definition**: VPC Peering is a network connectivity option that connects two VPCs so that their resources can communicate privately using private IPs.  
- **One-line**: **VPC Peering creates a private network link between two VPCs, as if they are on the same network.**  

2️⃣ **TYPES**  
- **Intra-account peering**: Between VPCs in the same AWS account.  
  - **Use for**: Separating environments (dev/prod) but still needing private communication.  
- **Cross-account peering**: Between VPCs in different AWS accounts.  
  - **Use for**: Shared services, central networking/account isolation patterns.  
- **Inter-region peering**: Between VPCs in different regions.  
  - **Use for**: Global architectures needing cross-region private connectivity.  

3️⃣ **WHY**  
- You often split workloads across multiple VPCs (security, org structure, regions), but they still need to **talk privately**.  
- VPC Peering eliminates the need for exposed public endpoints or complex VPNs just for VPC-to-VPC communication.  
- It provides **low-latency, high-bandwidth** connectivity on AWS’s internal network.  

4️⃣ **HOW**  
- **Conceptually**  
  - You create a **peering connection** between two VPCs and **update route tables** so that traffic destined for the other VPC’s CIDR goes via the peering link.  
  - It is **non-transitive**: VPC A–B and B–C peering does not automatically enable A–C.  
- **In real projects**  
  - Use VPC Peering to connect application VPCs to a **shared services VPC** (e.g., central logging, CI/CD tools).  
- **Console flow**  
  - In **VPC → Peering connections**, create peering → specify requester and accepter VPCs (and accounts/regions) → accept request from the other side → modify each VPC’s route tables to add routes to the other VPC’s CIDR via the peering connection.  

---

### 8️⃣ Explain NAT Gateway in detail

1️⃣ **WHAT**  
- **Definition**: A NAT Gateway is a managed AWS service that allows private subnet instances to access the internet (or other public services) outbound, while blocking unsolicited inbound connections.  
- **One-line**: **NAT Gateway lets private instances go out to the internet without exposing them to direct inbound access.**  

2️⃣ **TYPES**  
- **Public NAT Gateway**  
  - Has an Elastic IP and lives in a **public subnet**.  
  - **Use for**: Allowing private subnet resources to reach the internet (software updates, external APIs).  
- **Private NAT Gateway** (for some advanced patterns / hybrid)  
  - Used in more complex architectures (e.g., accessing other VPCs or on-prem over private connectivity in certain patterns).  
  - In interviews, focus mainly on the **standard public NAT Gateway** use case.  

3️⃣ **WHY**  
- You want application servers/databases in **private subnets** (no public IP) for security, but they still need outbound access (patching, downloading packages, calling external APIs).  
- NAT Gateway provides **outbound-only connectivity** with **simple management** (fully managed, highly available per AZ) compared to running your own NAT instance.  

4️⃣ **HOW**  
- **Conceptually**  
  - Instances in a private subnet send outbound traffic; their route table forwards `0.0.0.0/0` to the NAT Gateway in a **public subnet**.  
  - NAT Gateway translates the private IPs to its Elastic IP, sends traffic out, and handles responses back to the original private instances.  
- **In real projects**  
  - For each AZ, you usually deploy **one NAT Gateway per AZ** and route that AZ’s private subnets through the local NAT to avoid cross-AZ dependency.  
- **Console flow**  
  - In **VPC → NAT Gateways**, create NAT Gateway → pick a public subnet and Elastic IP → create.  
  - Update the **route table** for private subnets: set default route `0.0.0.0/0` target to the NAT Gateway instead of an Internet Gateway.  

---

### 9️⃣ NAT Gateway vs NAT Instance

1️⃣ **WHAT**  
- **Definition**: Both NAT Gateway and NAT Instance provide outbound internet access for private subnet resources, but one is a managed service, the other is a self-managed EC2 instance.  
- **One-line**: **NAT Gateway is managed and scalable; NAT Instance is a DIY EC2-based NAT you manage yourself.**  

2️⃣ **TYPES / COMPARISON**  
- **NAT Gateway**  
  - Fully managed, highly available in an AZ, scales automatically, no OS to manage.  
  - **Use when**: You want simplicity, reliability, and production-grade behavior with minimal ops overhead (most use cases).  
- **NAT Instance**  
  - EC2 instance configured to perform NAT. You manage OS patches, scaling, and HA.  
  - **Use when**: You need special control (custom firewall rules, extra software on NAT, very specific routing behavior) and accept the ops overhead.  

3️⃣ **WHY**  
- NAT Gateway exists to remove the **operational burden** and **single-instance failure** risks of NAT Instances.  
- NAT Instances were the older approach: cheaper at very low throughput, but require manual scaling, failover, and patching.  
- In modern designs, NAT Gateway is preferred unless a specific customization forces NAT Instance.  

4️⃣ **HOW**  
- **Conceptually**  
  - With NAT Gateway, AWS handles **scaling and redundancy** inside the AZ; you just configure routing.  
  - With NAT Instance, you must **size the instance**, perhaps use Auto Scaling or scripting for failover, and secure/patch it.  
- **In real projects**  
  - Default pattern: **NAT Gateway per AZ** for private subnets.  
  - Edge-case pattern: Special NAT Instance if you need, for example, custom deep packet inspection or special routing that NAT Gateway doesn’t support.  
- **Console thinking**  
  - **NAT Gateway**: “Create NAT Gateway, attach EIP, add route” and you’re done.  
  - **NAT Instance**: Launch EC2 from NAT AMI or manually configure IP forwarding, attach IGW-facing route, then manage like any other instance.  

---

### 🔟 Difference between NACL vs Security Group

1️⃣ **WHAT**  
- **Network ACL (NACL)**: Stateless firewall at the **subnet level** that controls traffic in and out of subnets.  
- **Security Group (SG)**: Stateful virtual firewall at the **instance (ENI) level** that controls traffic to/from specific resources.  
- **One-line**: **NACLs protect subnets; Security Groups protect individual resources.**  

2️⃣ **TYPES / COMPARISON**  
- **Scope**  
  - NACL: Applied to **subnets**; all resources in that subnet are affected.  
  - SG: Applied to **ENIs/instances**; different instances in the same subnet can have different SGs.  
- **Statefulness**  
  - NACL: **Stateless**; you must define both inbound and outbound rules explicitly.  
  - SG: **Stateful**; response traffic is automatically allowed, no need for reverse rules.  
- **Rule style**  
  - NACL: Ordered rules with number priorities; evaluated top-down, can explicitly **allow and deny**.  
  - SG: Only **allow** rules; no explicit deny, implicit deny by default.  
- **Use cases**  
  - NACL: Coarse-grained subnet-level guardrails (e.g., block a bad IP range at subnet edge).  
  - SG: Fine-grained application-level control (e.g., “App servers can talk to DB on port 3306 only”).  

3️⃣ **WHY**  
- You need **layered security**: network-level protections and resource-level protections (defense in depth).  
- Security Groups are the main tool for day-to-day access control; NACLs provide an additional subnet-wide filter, especially useful for global blocks or compliance rules.  

4️⃣ **HOW**  
- **Conceptually**  
  - Traffic entering a VPC subnet hits the **NACL** rules first, then the **Security Group** on the specific ENI/instance. Both must allow the traffic.  
- **In real projects**  
  - You define Security Groups per tier: `lb-sg`, `app-sg`, `db-sg`; allow specific ports between them.  
  - You use NACLs to enforce broad rules, like blocking certain external IP ranges or limiting ports across an entire subnet.  
- **Console flow**  
  - **NACL**: In **VPC → Network ACLs**, create/edit NACL, add inbound/outbound rules, then associate it with one or more subnets.  
  - **Security Group**: In **EC2 → Security Groups**, create SG with inbound/outbound rules, then attach it to EC2, ENIs, RDS, ALB, etc.  
