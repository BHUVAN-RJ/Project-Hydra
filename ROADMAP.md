# Project Hydra: Product Plan & Curriculum

*Source of truth, replacing the original spec dated April 25, 2026.*
*Last updated: April 27, 2026.*

---

## 1. What this is, in one paragraph

Project Hydra is a hands-on backend scale engineering learning product, built around named projects (Twitter, YouTube, Stripe-style payments, Uber-like real-time, WhatsApp-style chat, URL shortener at extreme scale). Users learn by writing real implementations of scale concepts (rate limiters, queues, caching layers, deployment pipelines), running them under realistic load against real Docker containers, hitting real failure modes, and comparing their results against reference solutions. The platform also includes a mathematically grounded projection engine (built on the Universal Scalability Law and queuing theory) that takes real measurements at achievable laptop-scale load and projects behavior at higher scale, with explicit confidence intervals. The product is distinct from system design interview prep (which teaches you to *talk* about scale) and from clone tutorials (which teach features without scale). It's also distinct from KodeKloud and iximiuz Labs, which teach infrastructure operations without application-layer scale design.

## 2. Why this exists, honestly

Two reasons, in order of importance:

**For the builder (RJ):** Was rejected from an NVIDIA interview for lack of demonstrated scale experience. The traditional path to fixing this gap is "get hired somewhere with scale," which is the chicken-and-egg loop. Building this product creates the conditions to learn scale engineering deeply, generates a credible artifact for interviews, and produces a story ("I was rejected for X, I built this to address X") that lands well in hiring conversations. Goal is to be a meaningfully better backend engineer by September 2026 and to reapply to NVIDIA-tier roles with substantive evidence.

**For others:** People stuck in the same loop benefit from a structured, project-driven path that doesn't require getting hired first. Audience is real but smaller than the original positioning suggested. Macroeconomic data (junior tech hiring down 50%+ since 2022, CS grad unemployment at 6%+) means the audience is large in raw numbers but constrained in willingness/ability to pay. Realistic v1 outcome: a few hundred to a few thousand people get value over time, not a business.

The product is not optimized for becoming a business. It's optimized for: deep learning by the builder, credible interview artifacts, and broad usefulness to others as a side effect.

## 3. Phases and timeline

**Phase 1 (April 26 → September 1, 2026): Curriculum + Blogs.**
RJ designs the curriculum, learns each topic deeply enough to write a blog post on it, and publishes the blogs to bhuvanrj.me/essays. By September 1, all curriculum lessons are designed and the high-priority blogs are published.

**Phase 2 (May → August 2026, parallel to Phase 1): Platform built by Claude.**
Once enough curriculum is written that the platform requirements are stable (estimated mid-May), Claude implements the platform infrastructure: Electron shell, React Flow canvas, Monaco editor, Docker orchestration, load test harness, mathematically grounded projection engine (USL fitting + queuing theory), tiered iteration modes for the load tester, basic UI for navigating lessons. RJ directs and reviews. The platform exists to host the curriculum, not as the product itself.

**Phase 3 (September → December 2026): RJ goes through the curriculum as a user.**
RJ implements every drill and project lesson personally, writing the actual rate limiter, load balancer, sharded database client, deployment pipeline, etc. Claude is used as a tutor and code reviewer in this phase, not as a code writer. This is where the deep coding work happens.

**Phase 4 (October 2026 onwards): Light public release.**
Open-source the platform code, publish the curriculum content, post on HN/r/ExperiencedDevs/relevant Discords, accept GitHub issues but no SLA. Low-burden ongoing maintenance.

The September 1 milestone matters because it's when RJ starts reaching out to companies (NVIDIA reapplication, full-time role conversations). Having published blogs + designed curriculum + built platform by then provides real artifacts to reference. Phase 3 happens in parallel with that outreach.

## 4. Constraints and self-honesty notes

- **20-25 hours/week** committed to this project. Total Phase 1 budget: roughly 360-450 hours.
- **Follow-through risk:** RJ's pattern is to complete projects when there's external response or excitement. The plan should have early milestones that can be shared publicly to generate response (first three blog posts published by end of May).
- **Competing time:** CSCI 544 research has fixed external deadlines that take priority when conflicts arise. Curriculum work is otherwise the top priority.
- **Internship hunt + OPT:** December 2026 OPT start is the hardest deadline. Curriculum work should not crowd out internship interview prep.

## 5. Curriculum scope decision

The full topic list (foundations, reliability, operations, observability, distributed systems, plus database choice, security/auth, data pipelines, capacity planning) is approximately 31 lessons. At one week per deep blog post, that's well over the 18-week Phase 1 window.

**Resolution:** Two-tier blog approach.
- **Deep posts (1 week each, ~3000-5000 words):** for high-leverage topics where deep understanding matters disproportionately for interviews and for the projects.
- **Shorter posts (2-3 days each, ~1000-1500 words):** for topics that are important but where reference quality is more about clarity than depth.

This gets the full curriculum designed and most of it blogged in 18 weeks.

## 6. The curriculum, lesson by lesson

Each lesson has:
- **Concept:** what the lesson teaches
- **Why it matters:** practical justification, not abstract
- **What you'll build:** the drill or project work for this lesson
- **Resources:** specific videos, papers, books, blog posts to learn from
- **Blog depth:** Deep (1 week) or Short (2-3 days)
- **FAANG interview weight:** High / Medium / Low — how often this comes up in scale-focused interviews
- **Prerequisite lessons**

The curriculum is linear with skipping allowed if you already know a topic. Total: 30 lessons, organized into 6 modules. At end of each module is a milestone (drill set complete, or project ready to attempt).

Projects (in order of complexity) are pinned to specific points in the curriculum where the prerequisite concepts are covered. RJ chooses which project to do first when reaching that point.

---

### Module 1: Foundations (Lessons 1-7)

These are the concepts every project requires. Without these, nothing else makes sense.

**Lesson 1 — HTTP and API design at scale**
Concept: REST conventions, idempotency keys, pagination strategies (cursor vs offset), API versioning, error handling, status codes that mean what they say.
Why it matters: Bad API design is the most common reason systems fail to scale gracefully. Idempotency in particular is the foundation for safe retries, which is the foundation for distributed reliability.
What you'll build: A small REST API with idempotent POST endpoints using idempotency keys, cursor-based pagination, and proper error semantics.
Resources: Stripe API design docs, AWS API Gateway documentation, RFC 7231, Roy Fielding's REST dissertation summary, Hussein Nasser videos on idempotency.
Blog depth: Deep
FAANG weight: High
Prereqs: None

**Lesson 2 — Database fundamentals at scale**
Concept: How databases actually work under load. Indexes (B-tree, hash, GIN). Query plans. Connection pooling. The N+1 problem. Why your app slows down before your DB does, and vice versa.
Why it matters: Database is the bottleneck in 80% of backend systems. Senior engineers can read a query plan and predict performance. Junior engineers can't. This is the gap.
What you'll build: A Postgres database with realistic data, intentionally bad queries, and use EXPLAIN ANALYZE to fix them. Configure pgbouncer.
Resources: "Use the Index, Luke" by Markus Winand, Postgres EXPLAIN docs, Designing Data-Intensive Applications (DDIA) chapter 3, CMU Database Group lectures on YouTube (Andy Pavlo).
Blog depth: Deep
FAANG weight: High
Prereqs: 1

**Lesson 3 — Caching patterns**
Concept: Cache-aside, write-through, write-behind. TTL strategies. Cache invalidation (the second hardest problem in CS). Cache stampede prevention with locks or probabilistic early expiration. Hot key handling.
Why it matters: Caching is the single most common performance optimization. Doing it wrong creates inconsistency bugs that are hard to debug.
What you'll build: A Redis cache layer in front of Postgres with cache-aside pattern, deliberate stampede simulation, and a fix using single-flight locks.
Resources: AWS ElastiCache patterns, Hussein Nasser caching videos, Discord engineering blog on cache stampedes, Facebook's "Scaling Memcache at Facebook" paper.
Blog depth: Deep
FAANG weight: High
Prereqs: 2

**Lesson 4 — Rate limiting**
Concept: Token bucket, leaky bucket, fixed window, sliding window log, sliding window counter. Distributed rate limiting (where do you store the counters?). Per-user vs per-endpoint vs global.
Why it matters: Every public API needs rate limiting. Most engineers can name "token bucket" but can't implement it correctly under contention.
What you'll build: A rate limiter middleware implementing all five algorithms, distributed across multiple instances using Redis.
Resources: Cloudflare rate limiting blog posts, Stripe's rate limiting blog, "An alternative approach to rate limiting" by Kong, Hussein Nasser videos on rate limiting algorithms.
Blog depth: Deep
FAANG weight: High
Prereqs: 3

**Lesson 5 — Async processing and queues**
Concept: Why queues exist. Producer-consumer patterns. At-least-once vs exactly-once delivery. Idempotent consumers. Dead letter queues. Backpressure. Message ordering.
Why it matters: Most non-trivial backend work happens asynchronously. Understanding queue semantics is the difference between "it works" and "it works under failure."
What you'll build: A worker pool consuming jobs from Redis or RabbitMQ, with retries, idempotency, and a DLQ for poison messages.
Resources: AWS SQS docs, RabbitMQ tutorials, Sidekiq best practices, "Designing Data-Intensive Applications" chapter 11, Confluent's Kafka documentation on delivery guarantees.
Blog depth: Deep
FAANG weight: High
Prereqs: 4

**Lesson 6 — Database replication and read scaling**
Concept: Primary-replica replication. Synchronous vs async replication. Read-after-write consistency problems. Read replica lag. When read replicas don't help.
Why it matters: First-line scaling answer for most read-heavy systems. Also the source of the most confusing bugs (user writes data, immediately reads from replica, doesn't see it).
What you'll build: Postgres primary with two read replicas. App that writes to primary, reads from replicas. Demonstrate replica lag with measurements. Implement read-your-writes consistency for the user's own data.
Resources: DDIA chapter 5, Postgres streaming replication docs, AWS RDS read replica documentation, GitLab's postmortem of their 2017 database incident.
Blog depth: Deep
FAANG weight: High
Prereqs: 2

**Lesson 7 — Sharding and partitioning**
Concept: Why you shard, when you shard, how you shard. Range-based vs hash-based vs directory-based. Consistent hashing. Hot partition problem. Resharding strategies. Why most teams don't shard until they have to.
Why it matters: The point at which a system stops being trivially scalable. Knowing when not to shard is as important as knowing how to shard.
What you'll build: A hash-based sharded user-data store across three Postgres instances with a routing layer, plus a demonstration of hot partition and how consistent hashing mitigates it.
Resources: DDIA chapter 6, "Consistent Hashing and Random Trees" paper (Karger 1997), Discord's "How Discord Stores Trillions of Messages" blog post, Notion's "Sharding Postgres" blog post, Vitess documentation.
Blog depth: Deep
FAANG weight: High
Prereqs: 6

**Module 1 milestone:** All seven foundation drills complete. RJ now has working implementations of an API, a sharded cached datastore, a rate limiter, a worker queue, and replication. These are the components every project will reuse.

---

### Module 2: Reliability (Lessons 8-12, including 11a)

Foundations make systems fast at the happy path. Reliability makes them survive when things break.

**Lesson 8 — Timeouts, retries, and exponential backoff**
Concept: Why every network call needs a timeout. Why every retry needs backoff. Why every backoff needs jitter. The retry-storm anti-pattern.
Why it matters: Cascading failures often start with retries without backoff hammering an already-degraded service. Senior engineers reach for these primitives reflexively.
What you'll build: An HTTP client with configurable timeouts, exponential backoff with jitter, and retry budgets. Demonstrate retry storms in a multi-service setup.
Resources: AWS Architecture Blog "Exponential Backoff and Jitter," Google SRE Book chapter 22, Kubernetes client-go retry implementation.
Blog depth: Deep
FAANG weight: High
Prereqs: 1, 5

**Lesson 9 — Circuit breakers and bulkheads**
Concept: Circuit breaker states (closed, open, half-open). Bulkheading to isolate failure domains. Fail-fast semantics.
Why it matters: When a downstream service is dead, calling it more is the wrong move. Circuit breakers stop the bleeding.
What you'll build: A circuit breaker library wrapping HTTP calls, with metrics. Demonstrate cascading failure prevention.
Resources: Netflix Hystrix documentation (deprecated but conceptually clear), resilience4j docs, Michael Nygard's "Release It!" book chapter on stability patterns.
Blog depth: Short
FAANG weight: Medium
Prereqs: 8

**Lesson 10 — Health checks: liveness vs readiness**
Concept: The two health checks that mean different things. Why returning 200 from /health is not enough. Startup probes.
Why it matters: Misconfigured health checks cause more outages than failed deployments. Critical for any orchestrated environment.
What you'll build: A service with proper liveness, readiness, and startup probes integrated with Kubernetes.
Resources: Kubernetes documentation on probes, Google SRE Book chapter on health checks.
Blog depth: Short
FAANG weight: Medium
Prereqs: 1

**Lesson 11 — Backpressure and graceful degradation**
Concept: What to do when load exceeds capacity. Reject vs queue vs degrade. Load shedding. Returning cached data on failure.
Why it matters: At scale, you cannot serve every request. The question is how you fail.
What you'll build: A service with explicit load shedding under high CPU, and graceful degradation that returns stale cache on DB failure.
Resources: Netflix's "Adaptive Concurrency Limits" blog, AWS Builders' Library "Using load shedding to avoid overload," Marc Brooker's blog posts.
Blog depth: Deep
FAANG weight: High
Prereqs: 8, 9

**Lesson 11a — Capacity planning and queuing theory**
Concept: Little's Law (L = λW). Universal Scalability Law (USL) — how throughput evolves as you add load, with parameters for contention (α) and coherency (β). M/M/c queuing models for predicting saturation. Amdahl's Law and its limits. Why utilization above 70% causes latency to explode. How to fit a USL curve from 4-6 measurements at different load levels.
Why it matters: This is the math underneath the platform's projection engine. Doing scale work without understanding queuing theory means you're guessing. Senior engineers at AWS, Google, Netflix routinely apply these models for capacity planning. This lesson is also what makes the platform's projection feature credible — users learn the math behind it rather than treating it as a black box. Plus: "I read Gunther's USL papers and applied them to a real system" is itself a strong technical blog post and interview talking point.
What you'll build: A small benchmarking script that runs a target service at multiple load levels (100, 500, 1000, 2000, 4000, 8000 RPS), collects p50/p95/p99 latency at each, fits a USL curve to the data, and projects saturation point with confidence intervals. Visualize the fit in Grafana or matplotlib.
Resources: Neil Gunther's "Guerrilla Capacity Planning" book (primary text — read chapters on USL deeply), Gunther's USL papers (free online), Marc Brooker's blog at brooker.co.za (especially posts on queuing theory in real systems), Brendan Gregg's "Systems Performance" book chapters on capacity planning, Mor Harchol-Balter's "Performance Modeling and Design of Computer Systems" (deeper academic treatment, dip into as needed). Allow 2-3 weeks for this lesson because the theory takes real time to internalize.
Blog depth: Deep (this is one of the most differentiated posts you'll write — most backend engineers have never seen this material applied practically)
FAANG weight: Medium for general roles, High for performance/SRE/platform roles
Prereqs: 2, 11
Note on time: This lesson is intentionally longer than others (2-3 weeks instead of 1) because the theory is genuinely hard and doing it shallowly defeats the purpose. The platform's credibility depends on this being done right. Build buffer for this in the schedule.

**Lesson 12 — Idempotency at scale**
Concept: Idempotency as a foundation. Idempotency keys, request deduplication, idempotent retries. The relationship between idempotency and exactly-once semantics.
Why it matters: Already covered briefly in Lesson 1, but at scale this becomes more nuanced. Stripe-style idempotency tokens, time-bounded windows, storage tradeoffs.
What you'll build: An idempotency middleware with proper key storage, expiration, and concurrent request handling.
Resources: Stripe's "Designing robust and predictable APIs with idempotency" blog post, Square's idempotency engineering blog post.
Blog depth: Short
FAANG weight: High
Prereqs: 1, 5

**Module 2 milestone:** Reliability primitives complete. **First project unlocks here.** At this point, RJ can attempt the URL shortener at extreme scale (the smallest project) or the simpler chat system. Most projects need additional concepts from later modules. The decision of which project to start with happens at this milestone.

---

### Module 3: Operations (Lessons 13-19)

The CI/CD and infrastructure layer that NVIDIA specifically called out as missing.

**Lesson 13 — Containerization deep dive**
Concept: Docker beyond the surface. Multi-stage builds. Image layer caching. Non-root containers. Distroless images. Image size optimization. Build determinism.
Why it matters: Every deployment starts here. Bad images cause slow deploys, security holes, and reproducibility nightmares.
What you'll build: A multi-stage Dockerfile for a Python service that produces a sub-50MB distroless image, with layer caching optimized for CI.
Resources: Docker official documentation, "Best practices for writing Dockerfiles," Google's distroless images repo, iximiuz Labs free tutorials on Linux containers.
Blog depth: Short
FAANG weight: Medium
Prereqs: None (parallel track)

**Lesson 14 — Kubernetes basics for backend engineers**
Concept: Pods, deployments, services, ingresses. ConfigMaps and secrets. Resource requests and limits. HPA basics.
Why it matters: K8s is the standard deployment substrate at most scale-relevant companies. Backend engineers don't need to be K8s experts but need fluency.
What you'll build: Deploy a service to a local kind cluster with proper resource limits, HPA based on CPU, and rolling deploys.
Resources: Kubernetes official tutorials, "Kubernetes the Hard Way" by Kelsey Hightower (read, don't necessarily do), KodeKloud free K8s content, iximiuz K8s playground.
Blog depth: Deep
FAANG weight: Medium
Prereqs: 13

**Lesson 15 — CI pipeline design**
Concept: Build, test, security scan, deploy stages. Caching strategies. Parallelization. Pipeline-as-code. Trunk-based development assumptions.
Why it matters: NVIDIA explicitly called out CI/CD experience. This lesson directly addresses that gap.
What you'll build: A GitHub Actions pipeline for a service: lint, unit test, integration test, security scan, build image, push to registry. With proper caching.
Resources: GitHub Actions docs, "Continuous Delivery" by Humble and Farley (chapters 1-4), GitLab CI/CD examples.
Blog depth: Deep
FAANG weight: High
Prereqs: 13

**Lesson 16 — Deployment strategies**
Concept: Blue-green, canary, rolling, feature flags. When each makes sense. Rollback strategies. Database migrations during deploys.
Why it matters: How code reaches production is as important as the code itself. Bad deploy strategies cause outages even when code is correct.
What you'll build: A canary deployment using K8s and Argo Rollouts (or similar) with automatic rollback on error rate spikes.
Resources: Google SRE Book chapter on release engineering, Argo Rollouts docs, LaunchDarkly's feature flag patterns blog.
Blog depth: Deep
FAANG weight: High
Prereqs: 14, 15

**Lesson 17 — Configuration management and secrets**
Concept: 12-factor config. Environment variables vs config files vs config services. Secrets handling (never in env vars in production, ideally). HashiCorp Vault, AWS Secrets Manager, Kubernetes secrets.
Why it matters: Misconfigured secrets cause the majority of cloud security incidents. Real production-scale work demands fluency here.
What you'll build: A service with configuration loaded from Vault, secrets rotated without restart.
Resources: 12factor.net, Vault documentation, "The Twelve-Factor App" essay.
Blog depth: Short
FAANG weight: Medium
Prereqs: 14

**Lesson 18 — Infrastructure as Code with Terraform**
Concept: Declarative infrastructure. State management. Modules. Plan/apply cycle. Why Terraform over Pulumi vs CDK is a real choice.
Why it matters: Most cloud infrastructure at scale is managed via IaC. Familiarity expected at most senior backend roles.
What you'll build: A Terraform module deploying a small cloud setup (VPC, RDS, ECS or EKS, S3) on AWS or GCP free tier.
Resources: HashiCorp Learn Terraform tutorials, "Terraform: Up & Running" by Yevgeniy Brikman, HashiCorp configuration language docs.
Blog depth: Deep
FAANG weight: Medium
Prereqs: 14

**Lesson 19 — On-call and incident response basics**
Concept: What incident response actually looks like. Severity classification. Runbooks. Postmortems. Blameless culture.
Why it matters: At scale, you will be on call. Knowing what good incident response looks like before your first 3am page is valuable.
What you'll build: A runbook for one of your services. A simulated incident with a postmortem write-up.
Resources: Google SRE Book chapters 13-15 (on-call, incident response, postmortems), PagerDuty's incident response documentation, sample postmortems from GitLab and Cloudflare blogs.
Blog depth: Short
FAANG weight: Medium
Prereqs: None (read-heavy lesson)

**Module 3 milestone:** RJ can take any service from code to deployed-with-CI/CD-and-monitoring on a real cloud. This is the operational gap NVIDIA flagged.

---

### Module 4: Observability (Lessons 20-23)

You can't operate what you can't see.

**Lesson 20 — Structured logging**
Concept: Log levels. Structured (JSON) logs. Log aggregation. What to log and what not to log. Sensitive data in logs.
Why it matters: Logs are your primary debugging tool in production. Bad logs make outages take hours instead of minutes.
What you'll build: Structured logging in a service, shipped to Loki or similar, with proper log levels and request correlation IDs.
Resources: Honeycomb's "Observability Engineering" book chapter 1-2, structured logging patterns documentation, OpenTelemetry logging spec.
Blog depth: Short
FAANG weight: Medium
Prereqs: None

**Lesson 21 — Metrics: RED, USE, and SLI/SLO**
Concept: RED method (Rate, Errors, Duration). USE method (Utilization, Saturation, Errors). Service Level Indicators, Objectives, and Error Budgets.
Why it matters: This is how scale companies talk about reliability. Knowing this vocabulary is table stakes for SRE/platform conversations.
What you'll build: Prometheus metrics in a service following RED method, dashboards in Grafana, an SLO with an error budget burn alert.
Resources: Google SRE Book chapter 4, Tom Wilkie's RED method blog post, Brendan Gregg's USE method page, Prometheus documentation.
Blog depth: Deep
FAANG weight: High
Prereqs: 20

**Lesson 22 — Distributed tracing**
Concept: Spans, traces, context propagation. OpenTelemetry. When tracing helps and when it doesn't.
Why it matters: At multi-service scale, traces are how you find performance issues that span service boundaries.
What you'll build: OpenTelemetry instrumentation across two services, traces shipped to Jaeger or Tempo, find a performance issue using traces.
Resources: OpenTelemetry documentation, "Distributed Tracing in Practice" book, Jaeger documentation.
Blog depth: Short
FAANG weight: Medium
Prereqs: 20

**Lesson 23 — Alerting design**
Concept: Symptom-based vs cause-based alerts. Alert fatigue. PagerDuty rotations. The Google SRE alerting philosophy.
Why it matters: Bad alerts wake people up at 3am for nothing. Good alerts don't.
What you'll build: Alerts on the SLO error budget burn rate from Lesson 21, properly tuned to avoid noise.
Resources: Google SRE Book chapter on alerting, "My Philosophy on Alerting" by Rob Ewaschuk.
Blog depth: Short
FAANG weight: Medium
Prereqs: 21

**Module 4 milestone:** RJ has full observability stack on every service. Can answer "what's slow, what's failing, what's about to break."

---

### Module 5: Distributed Systems (Lessons 24-27)

Only the parts you actually need to operate scale systems. Not academic theory.

**Lesson 24 — Replication and consistency models**
Concept: Strong vs eventual consistency. Read-your-writes, monotonic reads. CAP theorem (and why it's frequently misunderstood). PACELC.
Why it matters: Choosing your consistency model is one of the most consequential architectural decisions. Getting it wrong creates bugs that are nearly impossible to reproduce.
What you'll build: A small key-value store with configurable consistency (strong vs eventual), demonstrating the tradeoffs.
Resources: DDIA chapter 5 and 9, Martin Kleppmann's Cambridge lectures on YouTube, Daniel Abadi's PACELC paper, Aphyr's Jepsen test results blog.
Blog depth: Deep
FAANG weight: High
Prereqs: 6

**Lesson 25 — Consensus and when to reach for etcd/Consul**
Concept: Why consensus is hard (FLP). Raft at the conceptual level. What etcd and Consul give you. Distributed locks (and why Redis Redlock is controversial).
Why it matters: You will not implement Raft. You will use systems built on it. Knowing when and how matters.
What you'll build: Use etcd for leader election in a multi-instance service. Use Consul for service discovery.
Resources: The Raft paper (read for understanding, not implementation), thesecretlivesofdata.com/raft, etcd documentation, Martin Kleppmann's blog post on distributed locks ("How to do distributed locking").
Blog depth: Deep
FAANG weight: Medium
Prereqs: 24

**Lesson 26 — Distributed transactions and the saga pattern**
Concept: Why 2PC blocks. Sagas. Outbox pattern. Eventual consistency for cross-service operations.
Why it matters: Microservice architectures force you to confront this. Most engineers handle it badly.
What you'll build: A saga implementation for a cross-service flow (e.g., order + payment + inventory) with proper compensating actions.
Resources: Chris Richardson's microservices.io saga pattern page, "Designing Data-Intensive Applications" chapter 7, Stripe's engineering blog on idempotent payments.
Blog depth: Deep
FAANG weight: High
Prereqs: 5, 24

**Lesson 27 — Database choice as an explicit topic**
Concept: When to pick Postgres vs DynamoDB vs Cassandra vs ClickHouse vs Redis vs MongoDB. The questions you should ask before choosing. Cost implications. Operational implications.
Why it matters: One of the most consequential architectural decisions a backend engineer makes. Almost no resource teaches it well.
What you'll build: A decision framework with worked examples. A small benchmark of three databases on the same workload.
Resources: Use The Index Luke, AWS database selection guide, blog posts from Discord, Notion, Figma on their database choices, "Designing Data-Intensive Applications" overview chapters.
Blog depth: Deep
FAANG weight: High
Prereqs: 2, 6, 7, 24

**Module 5 milestone:** RJ has the distributed systems vocabulary needed to operate (not implement) scale systems.

---

### Module 6: Advanced topics added per RJ's request (Lessons 28-30)

**Lesson 28 — Security and auth at scale**
Concept: OAuth 2.0 / OIDC flows. JWT (and when not to use it). Session management. CSRF, SSRF, SQL injection, XSS. Rate limiting as a security control. Secret rotation.
Why it matters: Security incidents are a major operational concern at scale. Most backend engineers know auth at the surface level only.
What you'll build: Implement OAuth 2.0 authorization code flow from scratch, session management with proper expiration and rotation, demonstration of common attacks and mitigations.
Resources: OAuth 2.0 RFC 6749, OWASP Top 10, Auth0 documentation, "Web Application Security" by Andrew Hoffman.
Blog depth: Deep
FAANG weight: Medium (high for security-adjacent roles)
Prereqs: 1

**Lesson 29 — Data pipelines: Kafka and stream processing basics**
Concept: Event streaming vs message queues. Kafka basics: topics, partitions, consumer groups. Exactly-once semantics. Stream processing basics (windowing, aggregation).
Why it matters: Modern backend at scale is increasingly data-pipeline-driven. Kafka shows up everywhere.
What you'll build: A Kafka producer/consumer setup with proper partitioning, a small stream processor doing windowed aggregation.
Resources: "Kafka: The Definitive Guide" by Neha Narkhede et al, Confluent's Kafka tutorials, "Designing Data-Intensive Applications" chapter 11.
Blog depth: Deep
FAANG weight: Medium
Prereqs: 5, 24

**Lesson 30 — Cost optimization at scale**
Concept: Why senior engineers think about cost. Compute (reserved/spot/autoscale tradeoffs). Storage classes. Network egress as the silent killer. Database cost (read replicas, IOPS provisioning). Observability data retention costs.
Why it matters: At scale, infrastructure cost decisions become engineering decisions. This is shockingly underrepresented in educational content and is a real differentiator at senior levels.
What you'll build: A cost analysis of one of your earlier projects, identifying optimization opportunities.
Resources: AWS Well-Architected Framework cost optimization pillar, "FinOps" by J.R. Storment, blog posts from companies like Vercel and Railway on their infrastructure cost work.
Blog depth: Short
FAANG weight: Low (but high for senior roles)
Prereqs: 14, 18

**Module 6 milestone:** Curriculum complete.

---

## 7. Projects

Projects are not lessons. They're integration exercises that pull from the lessons. Each project pulls from a different subset of concepts.

The plan is: complete the curriculum drills first, then choose one project to do as the capstone for Phase 3. Other projects can be added later. Project order is by complexity, smallest to largest.

**Project A — URL shortener at extreme scale.**
Concepts pulled: 1 (API), 2 (DB), 3 (cache), 4 (rate limiting), 6 (replication), 7 (sharding), 13 (containers), 15 (CI/CD), 21 (metrics).
Why it's a good first project: Small surface area, but every component must scale. No frontend complexity. Easy to load-test. Hits a wide range of foundation concepts cleanly.
Estimated time: 3-4 weeks.

**Project B — Twitter timeline service.**
Concepts pulled: 1, 2, 3, 5 (queues), 6, 7, 9 (circuit breakers), 13, 15, 21, 22 (tracing), 27 (DB choice).
Why interesting: The "celebrity fanout" problem (Justin Bieber problem) is a famous scale challenge. Push vs pull timelines is a classic tradeoff.
Estimated time: 5-6 weeks.

**Project C — Real-time chat (WhatsApp-like or YouTube Live chat).**
Concepts pulled: 1, 4, 5, 7, 11 (backpressure), 13, 14 (K8s), 15, 21, 22, 24 (consistency), 28 (security).
Why interesting: WebSocket fan-out, message ordering, presence, hot-key handling for popular streams.
Estimated time: 5-6 weeks.

**Project D — Stripe-like payment processor.**
Concepts pulled: 1, 2, 5, 8 (retries), 12 (idempotency), 14, 15, 16 (deploy strategies), 19 (incident response), 21, 24, 26 (sagas), 28.
Why interesting: Idempotency and reliability are the core challenge. Mistakes are visible (lost money). Webhooks and async processing are central.
Estimated time: 6-7 weeks.

**Project E — Uber-like real-time matching.**
Concepts pulled: 1, 2, 3, 5, 7 (sharding by geography), 11, 13, 14, 21, 22, 26, 29 (Kafka).
Why interesting: Geospatial sharding is a different sharding problem than user-id-based. Real-time matching has interesting consistency tradeoffs.
Estimated time: 6-7 weeks.

**Project F — YouTube Live (capstone-tier).**
Concepts pulled: All of the above plus storage at scale, CDN strategies, transcoding pipelines.
Why interesting: Most ambitious. Probably not v1 material; a stretch goal for Phase 3 if time permits.
Estimated time: 8-10 weeks.

## 8. Phase 1 weekly schedule

This is what the next 18 weeks look like for RJ specifically. Each week assumes 20-25 hours.

**Weeks 1-3 (April 28 - May 18):** Foundations module 1, lessons 1-3.
- Lesson 1 (API design): research, build, blog. Deep post.
- Lesson 2 (DB fundamentals): research, build, blog. Deep post.
- Lesson 3 (caching): research, build, blog. Deep post.
- End of week 3: three deep blog posts published on bhuvanrj.me/essays. **First public visibility moment.** Post to HN/Twitter/LinkedIn. This is also the audience-building soft launch (see Section 8a).

**Weeks 4-6 (May 19 - June 8):** Foundations lessons 4-7.
- Rate limiting (deep), queues (deep), replication (deep), sharding (deep).
- End of week 6: foundations module complete. Curriculum design for module 1 complete.

**Weeks 7-8 (June 9 - June 22):** Reliability module first half.
- Lessons 8 (timeouts/retries), 9 (circuit breakers), 10 (health checks), 11 (backpressure).
- End of week 8: ready for the queuing theory deep dive.

**Weeks 9-10 (June 23 - July 6):** Lesson 11a — Capacity planning and queuing theory.
- This is the longest single lesson and deserves dedicated time. Read Gunther's "Guerrilla Capacity Planning" thoroughly. Read Marc Brooker's blog. Implement USL fitting against synthetic data. Write a deep blog post.
- End of week 10: theory grounded enough to direct Claude precisely on the projection engine in Phase 2.

**Week 11 (July 7 - July 13):** Reliability module wrap-up.
- Lesson 12 (idempotency at scale).
- End of week 11: reliability module complete. **Decision point: Phase 2 platform build can start in parallel here** because the curriculum structure is stable enough AND the queuing theory needed for the projection engine is now grounded.

**Weeks 12-14 (July 14 - August 3):** Operations module.
- Lessons 13-19. The CI/CD-heavy material that NVIDIA called out. Some lessons compressible since several are short posts.

**Weeks 15-16 (August 4 - August 17):** Observability + Distributed systems modules.
- Lessons 20-23 (observability), then start Lessons 24-27 (distributed systems). Aggressive compression here is fine because several lessons are short posts.

**Week 17 (August 18 - August 24):** Distributed systems wrap-up + advanced topics.
- Finish Lessons 24-27. Start Lessons 28-30.

**Week 18 (August 25 - August 31):** Buffer and final polish.
- Catch up on slipped content. Final review. Prepare for Phase 3 launch.

**September 1 milestone:** Curriculum 100% designed. Roughly 18-22 deep blog posts published, plus 8-12 shorter posts. Platform under construction (started around week 11). Audience-building cadence in motion (see Section 8a). At least one project's worth of curriculum content fully written and tested.

## 8a. Audience-building strategy (parallel with curriculum work)

The original plan had distribution as "post on HN/Reddit at launch." That's not enough. The platform launches quietly without an audience. This section corrects that by integrating audience growth into Phase 1.

**Why this matters.** If September 1 arrives and the only people who know about the curriculum are personal contacts, the launch lands silently. The blogs themselves are also less effective for interviews if no one has read them. By contrast, if RJ has built a small but real audience over 18 weeks (a few hundred newsletter subscribers, recognized name in scale-engineering Twitter/Bluesky circles, occasional HN front-page hit), the launch has momentum, the artifacts have credibility, and the NVIDIA reapplication carries more weight.

**Tactical commitments.**

1. **Each deep blog post is written to be shareable, not just educational.** Concretely: every deep post needs a clear thesis stated upfront, a memorable diagram or visualization, and at least one counterintuitive or pointed claim that gives readers a reason to share. "10 things about caching" doesn't work. "Why your cache invalidation strategy is creating data inconsistency bugs you haven't noticed yet" works.

2. **Post each blog to multiple channels with channel-specific framing.**
   - Personal site (bhuvanrj.me/essays): canonical version
   - Hacker News: technical headline, posted at peak hours (Tuesday-Thursday morning Pacific time)
   - LinkedIn: with a personal hook, often referencing the NVIDIA story or learning journey
   - Twitter/Bluesky: thread version with key insights
   - r/ExperiencedDevs and r/programming: when relevant
   - dev.to and Hashnode: cross-post for SEO and discoverability

3. **Two audience-building posts in addition to lesson posts.** These are different from curriculum content:
   - **Week 5: "I was rejected from NVIDIA for not knowing scale. Here's what I'm doing about it."** Personal narrative, not a technical post. Sets up the project, generates empathy, drives subscribers.
   - **Week 12: "The math behind the Universal Scalability Law, applied to a real backend."** Coming out of Lesson 11a, this is the differentiated technical post that establishes credibility on the rigor angle. Targeted at HN front page.

4. **Target metrics for Phase 1.** Realistic targets:
   - 100-300 newsletter or RSS subscribers by September 1
   - One HN front-page hit (top 30) somewhere in Phase 1
   - 50-200 Twitter/Bluesky followers in scale-engineering circles
   - First emailed feedback from a stranger by week 6 (signal that posts are landing beyond network)
   - At least one inbound contact from a senior engineer offering feedback or asking questions by week 12

5. **Time cost.** Approximately 2-3 hours per blog post for cross-posting, channel-specific framing, and engagement (responding to comments, replying on threads). On top of the writing time. Adds maybe 30-50 hours total over Phase 1. Real but manageable.

6. **Consistency cadence.** A short post or thread (not a deep blog) every 7-10 days even when not publishing a deep post. Could be: a take on a recent engineering blog post you read, a clarification of something from a previous deep post, a small experimental finding from your own work, a quote-thread on a paper. Keeps the feed warm without burning writing time.

7. **Specific people to follow and engage with.** The audience you want to reach reads/posts from: Marc Brooker (AWS), Brendan Gregg (Netflix/Intel), Tanya Reilly, Charity Majors (Honeycomb), Aphyr (Jepsen), Gergely Orosz (Pragmatic Engineer), Hussein Nasser. Follow them, engage thoughtfully on their posts, occasionally have a technical exchange in public. Not as influencer chasing — as participation in the community whose attention you want.

8. **Don't fake it.** Engagement bait, ghostwriting, paid promotion are all counterproductive here. The audience you need is hiring managers and senior engineers; they spot inauthenticity instantly. Slow real growth is much more valuable than fast fake growth.

This strategy is conservative. It assumes most posts don't go viral and most weeks don't produce big wins. The compounding effect of consistent quality posting over 18 weeks is what produces results, not any single piece. By September 1, RJ has a small but real audience, a track record of technical writing, and a launchpad for the platform.

## 9. Phase 2: Platform construction (parallel, May-August)

Starting around week 9 (mid-June), Claude builds the platform alongside curriculum work. Stack as originally specified:
- Electron shell with React Flow canvas, Monaco editor, xterm.js terminals
- Docker orchestration via Docker API
- k6 or Locust integration for load testing
- Prometheus + Grafana for metrics display (real, not simulated)
- Chaos injection via Pumba (Docker) or Chaos Mesh (Kubernetes)
- Lesson navigation UI
- Persistent storage of user progress

**Projection engine (the platform's unique technical differentiator).**
Built on real queuing theory (covered in Lesson 11a). Implementation:
- USL (Universal Scalability Law) curve fitting from 4-6 measurement points
- Multi-component modeling (DB, cache, API server, network) — the projection identifies which component saturates first at each load level
- Three load regimes with explicit visual distinction:
  - Measured zone (1K-10K RPS depending on hardware): real measurements from real load tests against real containers. Solid colors, sharp edges.
  - Near projection (up to 10x measured): mathematically projected with USL/M/M/c models. Slightly translucent visual treatment. Decent confidence.
  - Far projection (10x to 100x): same models with explicit confidence indicators (error bars, color desaturation, "low confidence" labels).
  - Beyond 100x: platform refuses specific numbers, instead shows architectural recommendations.
- Confidence intervals computed from measurement variance and fit quality, displayed alongside every projection.

**Tiered iteration modes for the load tester.**
Users have three modes for different stages of their workflow:
- **Smoke test (60-90 seconds):** single data point at representative load. Just "did your code work, what was p99, what was the error rate." Used during active coding after small changes.
- **Fast projection (3-5 minutes):** three data points, 60s each, simple curve fit. Rough numbers (±30% accuracy). Used for "is this design heading in the right direction."
- **Rigorous projection (25-30 minutes):** six or more data points, full duration, full USL fit with confidence intervals, multi-component analysis. Used to generate the final shareable artifact (video, screenshots, writeup) for interviews.

**Hardware detection and container limits.**
Platform detects available resources at startup and sets a profile:
- Low-end (8GB RAM): max 8-10 containers total
- Mid (16GB): max 15-18 containers
- High (32GB+): max 20-30 containers
Platform refuses to spawn beyond the limit and explains why.

**Resource budget for Phase 2.**
The projection engine is non-trivial. Realistic time allocation: 2-3 weeks for projection engine implementation alone, on top of the standard platform infrastructure. RJ should plan to be precise in directing Claude here (specific algorithms, specific loss functions, specific confidence interval computations) rather than asking Claude to "build a projection engine" generically.

RJ's role in Phase 2: review architecture, integrate with curriculum content, test usability, direct precisely on the projection engine math (this is where Claude needs the most guidance). RJ does not write the platform code itself.

## 10. Phase 3: RJ goes through the curriculum (September onwards)

Starting September 1, RJ implements the lessons as a user. The rule for this phase: Claude is a tutor and reviewer, not a code writer. RJ writes:
- The actual rate limiter code
- The actual queue worker code
- The actual sharding logic
- The actual deployment pipelines
- The actual observability instrumentation

When stuck: Claude explains concepts. When done with an implementation: Claude reviews, points out failure modes, suggests improvements.

Output of Phase 3: a single project (chosen from A-F) fully implemented by RJ, with load test results, failure mode documentation, postmortems, and a public writeup. This is the main artifact for the NVIDIA reapplication and other interviews.

## 11. Phase 4: Public release (October onwards)

Light release:
- Open-source the platform code on GitHub
- Publish curriculum content as part of the same repo
- Announce to the audience built during Phase 1 (newsletter subscribers, Twitter/Bluesky followers) — this is the difference between launching with momentum vs launching to silence
- Post on HN, r/ExperiencedDevs, r/cscareerquestions, relevant Discords
- Accept GitHub issues, no SLA
- Don't build community features (forums, etc.)
- Don't take payment in v1

If response is strong, can iterate. If response is muted, the artifact still exists for interview purposes and the work was not wasted.

## 12. Risks and mitigations

**Risk: Phase 1 timeline slips.**
- Mitigation: hard cutoff per lesson. If a lesson takes more than 1 week (deep) or 3 days (short), publish what you have and move on. Exception: Lesson 11a (queuing theory) gets 2-3 weeks because the theory is genuinely hard and shallow understanding defeats the purpose.
- Mitigation: week 18 is buffer.

**Risk: Loss of momentum without external response.**
- Mitigation: end-of-week-3 publication push to get early signal.
- Mitigation: audience-building cadence per Section 8a creates more feedback loops than just blog drops.

**Risk: Curriculum perfectionism prevents Phase 2 start.**
- Mitigation: hard rule that Phase 2 starts at week 11 regardless of curriculum completion state for later modules.

**Risk: CSCI 544 deadlines collide with curriculum work.**
- Mitigation: 544 takes priority when they conflict. Curriculum slips are absorbed into buffer. If buffer exhausted, drop the lowest-priority lessons (likely Lesson 19 on-call, Lesson 30 cost optimization, possibly Lesson 23 alerting since it's read-heavy).

**Risk: Phase 3 quality compromised by Claude over-help.**
- Mitigation: explicit rule, written down, that Phase 3 implementations are RJ's own code.
- Mitigation: every project gets a writeup that includes "what I struggled with," which is hard to fake.
- Mitigation: consider recording yourself coding the implementations — not for an audience necessarily, just so there's a record. Forces actually doing the work, and the recordings are themselves potential interview artifacts.

**Risk: NVIDIA reapplication doesn't land.**
- Mitigation: don't optimize narrowly for NVIDIA. The artifacts work for any scale-relevant interview.
- Mitigation: continue applying broadly; this is one signal, not the whole strategy.

**Risk: Projection engine ships with bad math.**
- This is the highest-stakes technical risk. If the projection engine produces numbers that look authoritative but are wrong, every senior engineer who looks at the platform spots the fakeness in 30 seconds, and the credibility damage outweighs everything else the platform does well.
- Mitigation: Lesson 11a is non-negotiable. Read Gunther's "Guerrilla Capacity Planning" thoroughly before directing Claude on the implementation. If the theory feels too hard after 2 weeks, the right move is to descope the projection engine to "real measurements only, no projection" rather than ship something subtly wrong.
- Mitigation: explicit confidence intervals on every projection. Refusal to project beyond 100x measured load. Visual distinction between measured and projected zones.
- Mitigation: have at least one senior engineer (USC alumni, Twitter contact, etc.) review the projection engine's math before public launch.

**Risk: Audience building gets neglected because it doesn't feel urgent.**
- Mitigation: treat the audience-building posts (week 5 and week 12) as deliverables with deadlines, not nice-to-haves.
- Mitigation: per-blog cross-posting takes 2-3 hours and should be batched into a single session per week, not spread throughout.
- Mitigation: the cadence between deep posts (one short post or thread every 7-10 days) keeps engagement warm without requiring big writing investment.

**Risk: Job hunting collides with curriculum work.**
- Job interviews always take priority over curriculum work. If a phone screen with a good company comes up, take it, even if it means missing a self-imposed deadline.
- The curriculum is for the long game. Specific interviews are immediate opportunities and should never be sacrificed for curriculum velocity.

## 13. What this is not

- This is not a business plan. The product is not optimized to make money.
- This is not a polished consumer product. It's a learning platform for a specific kind of learner.
- This is not a replacement for system design interview prep. Use HelloInterview alongside.
- This is not a substitute for real industry experience. It's the closest available proxy when industry experience is gated.
- This is not a guarantee that NVIDIA or anyone else will hire RJ. It's evidence that improves the odds.

## 14. Open questions to revisit later

- Final project choice (A-F) for Phase 3 capstone. Decide at end of Module 2.
- Platform pricing in Phase 4 (probably free, but worth revisiting if response is strong).
- Whether to add the YouTube Live capstone project after the chosen v1 project.
- Whether to formalize the curriculum into a proper course offering at any point.

---

*End of plan. This document replaces the original Project Hydra spec dated April 25, 2026.*