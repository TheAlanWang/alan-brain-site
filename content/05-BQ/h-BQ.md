## Why did you transfer majors and come back to campus to study?

I started in Finance and Big Data, and after working on digital transformation projects I realized I enjoyed building systems more than analyzing results. I was often working with engineers, APIs, and data pipelines, and I wanted the ability to design and implement things myself.  

That’s why I transferred to Computer Science—to build strong fundamentals in algorithms, software engineering, and systems.  

I chose to come back to campus because I learn best in a structured, high-intensity environment: collaborating with classmates, getting feedback from professors, and doing team projects that mirror real engineering work.  

My goal is to become a software engineer and land an internship where I can combine my problem-solving background with solid CS skills.

## 1) Tight deadline + quick decisions

**Answer (EN)**  
When I was a consultant at Cubewise, I was supporting a client from Alibaba Group who needed to produce a monthly accounting report. Mid-month, they told us they wanted to generate the next month’s report using our system—but we only had about one week left, and the reporting module wasn’t fully finished.

I needed to make quick decisions to meet the deadline while keeping accuracy and audit risk low. I made a **scope** decision immediately: I split the reporting logic into **must-have vs. nice-to-have**. We implemented the core calculations for the major report items in our system first, and for the smaller items we temporarily relied on their existing system as a fallback. In parallel, I set up a fast validation plan—before go-live, we ran the system against last month’s data to confirm correctness end-to-end.

As a result, we delivered the core reporting capability within a week, successfully generated the monthly report from our system, and the client submitted accurate numbers on time. After the deadline, we continued iterating to bring the remaining smaller items into the new system.

At Cubewise, a client needed to generate a monthly accounting report, and they asked mid-month to use our system with only one week left. I made a quick scope decision: deliver the core calculations for the major report items first, and use their existing system temporarily for minor items. I validated the release by running it on last month’s data before go-live. We delivered on time and produced accurate numbers for the report submission.

---

## 2) Important decision without consulting manager

**Answer (EN)**  
During a Deloitte engagement, I was leading a client workshop when we found conflicting requirements between the business team and IT. My manager was in another meeting, and the client wanted an immediate decision to keep momentum. I proposed two options with tradeoffs and recommended a temporary decision: proceed with a reversible configuration, document assumptions, and schedule a 30-minute sign-off with key stakeholders within 24 hours. That let the team continue building without blocking. The next day, we confirmed the direction and avoided rework because we kept it reversible and well-documented.  
**可替换**：冲突点、你给的两个选项、你如何“可逆决策 + 快速补签字”。

---

## 3) Above and beyond (outside job description)

**Situation / Task**  
At Cubewise, we were doing frequent deployments for **IBM TM1**. The deployment packages were mainly stored as **metadata**, so the team had to manually select and bundle the right objects each time. It was time-consuming and also risky—missing one item could break something in production.

**Action**  
Although this wasn’t part of my formal responsibilities, I built a lightweight workflow to make deployments safer and faster. The workflow compared the **dev environment** and **production environment** packages, automatically detected what had changed, generated a deployment package, and then prepared it for upload. I also added basic checks to catch common mistakes and documented the steps so the team could use it consistently.

**Result**  
As a result, we reduced manual effort and avoided deployment errors caused by missed metadata. Deployments became more repeatable and the team spent less time on packaging and more time on actual delivery.

---

## 4) Most proud project and why

The project I’m most proud of is an AI-powered document assistant. Because it tackles a real reliability problem in AI applications: hallucinations. My goal was to make the system answer **only based on the uploaded document**.

To do that, I implemented a pipeline that **extracts and chunks** the PDF. Then uses **keyword retrieval** to rank the chunks based on user's questions.  Select the most relevant passages for a user’s question. I pass only those top passages into GPT, which keeps the response grounded and reduces hallucinations.

I also deployed the system on AWS, using **S3 to store documents** and a backend service to handle the API requests. This project taught me a lot. Chunk strategy, data security and data isolation. Fastapi framework.

In the future, I plan to upgrade it into a full **RAG** system using embeddings, and add user authentication and document-level access control.

---

## 5) Decision with incomplete data/information

**Answer (EN)**  
In a production support situation, we saw intermittent failures in a document workflow, but logs were incomplete due to a misconfigured monitoring rule. We still needed to decide whether to roll back. I pulled what we had—error frequency, impacted user groups, and recent change history—and ran a quick risk assessment. Because the issue affected a critical path, I decided to roll back the last change and enabled additional logging immediately. The rollback stabilized the system, and the new logs helped us identify the root cause for a safe re-release later.  
**可替换**：故障类型、你用的“风险评估依据”。

---

## 6) Another qualification not shown yet

**Answer (EN)**  
One qualification I haven’t highlighted is my ability to bridge business and engineering in fast-moving environments. I’ve worked across consulting and technical project management, so I’m comfortable translating ambiguous business needs into actionable technical requirements. I also communicate clearly with different audiences—executives, engineers, and external vendors—and I’m bilingual, which helps when teams are global. This combination helps me move projects forward, reduce misunderstandings, and keep delivery aligned with real user needs.  
**可替换**：你最擅长的“翻译需求/推进共识”的一个例子。

---

## 7) Ownership + extra mile to solve customer issue

**Answer (EN)**  
A business user reported that contract PDFs were being signed but not properly archived, causing audit risk. I took ownership end-to-end: reproduced the issue, reviewed workflow steps, and found a race condition between the signing callback and the storage service. I coordinated a short-term fix—retry logic and a temporary queue—then worked with engineering to implement a robust solution and add monitoring alerts. I also wrote a short guide for users on how to verify completion. The result was a stable workflow and fewer support tickets.  
**可替换**：你的“客户”是谁（内部用户/外部客户）、你加的监控/告警。

---

## 8) Learn a new technology quickly
When I worked at Deloitte, part of my responsibility was calculating **risk-weighted assets** for a bank. Initially, the client provided monthly data extracts—typically in Excel or CSV—so we could run the calculations directly.

Later, the client changed the requirement and asked us to pull the data directly from their database instead of sending files. That meant we had to learn SQL quickly and build a reliable way to query the right tables under a tight timeline.

I ramped up fast by learning core SQL patterns we needed—joins, filtering, aggregations, and window functions—and worked closely with the client’s data team to understand the schema and access permissions. We built the queries, validated them by reconciling results against the previous file-based outputs, and added basic checks to catch missing or inconsistent records.

In the end, we successfully integrated with the database, automated the data retrieval step, and delivered the product. It also reduced manual handoffs and improved consistency in the reporting process.

---

## 9) Significant challenges but delivered on time

**Answer (EN)**  
In a cross-team project, we had to integrate an approval workflow with a third-party system, but their API behavior changed late in the timeline. This created uncertainty in testing and risked the delivery date. I led a rapid triage: isolated the breaking change, aligned on a fallback approach, and organized parallel work streams—one team validating the new API, another implementing a backward-compatible adapter. We delivered the deadline by shipping the stable path first and scheduling the full upgrade after. The key was structured communication and disciplined scope control.  
**可替换**：第三方系统、fallback 方案。

---

## 10) Saw an opportunity and acted right away

**Answer (EN)**  
I noticed our team repeatedly had the same blockers during release—missing configuration steps and inconsistent handoffs. Instead of waiting for instructions, I drafted a one-page release checklist and a short “definition of done” template. I ran a quick review with engineering and QA, adjusted it, and then introduced it in the next release cycle. After that, releases became smoother, and we reduced last-minute surprises because expectations were explicit. It was a small change, but it had an outsized impact on delivery quality.  
**可替换**：你做的模板/流程是什么。

---

## 11) Dive deep to find root cause

**Answer (EN)**  
We saw a performance degradation where a document processing job slowed down dramatically under load. I dug into metrics, logs, and request traces, and built a small reproduction test to isolate the issue. I found the bottleneck was repeated parsing and redundant calls that scaled poorly. I proposed caching and batching, added profiling to confirm the hot spots, and then validated the fix with load tests. After the change, latency dropped and throughput improved, and we added monitoring to catch regressions early.  
**可替换**：具体 bottleneck（DB/N+1/IO/缓存）。

---

## 12) Disagreed but committed anyway

**Answer (EN)**  
I once disagreed with a teammate about whether to ship a feature quickly or spend more time refactoring. I shared my concerns with data—risk areas, testing gaps, and future maintenance cost—and proposed a compromise: ship a minimal version with guardrails and open a follow-up refactor ticket with clear acceptance criteria. The team decided to ship the minimal version. Even though it wasn’t my preferred approach, I fully committed to execution, helped strengthen tests, and documented the risks. The release went smoothly and we completed the refactor afterward.  
**可替换**：你怎么“表达反对但仍然支持团队决定”。

---

## 13) Build trust when new / skepticism

**Answer (EN)**  
When I joined a new team as a technical PM, some engineers were skeptical because I came from a consulting background. I focused on earning trust through actions: I learned the system quickly, asked precise questions, and removed friction by clarifying requirements and reducing unnecessary meetings. I also delivered quick wins—improving the ticket templates and ensuring decisions were documented. Over a few weeks, the team started involving me earlier because they saw I could help them move faster with less ambiguity.  
**可替换**：你做的 quick win 是什么。

---

## 14) Most challenging project and how you overcame it

**Answer (EN)**  
My most challenging project was leading a multi-system migration where we had to modernize a legacy workflow without disrupting daily operations. The difficulty was balancing new architecture changes with business continuity and multiple stakeholder expectations. I broke the work into phases, created clear success metrics, and used incremental rollouts with rollback plans. I kept a risk register, aligned stakeholders weekly, and ensured engineering had stable requirements and testing criteria. We completed the migration with minimal downtime and improved maintainability, and the phased approach prevented major surprises.  
**可替换**：迁移对象（系统/流程）、你怎么分阶段。

---

## 15) Conflict with a team member and resolution

**Answer (EN)**  
I had a conflict with a teammate about prioritization: they wanted to focus on new features, while I believed we needed to address reliability first. I scheduled a 1:1 to understand their concerns, and then we aligned on shared goals—user impact and delivery timeline. We used a simple framework: quantify risk and effort, then decide what must be done now vs. next. We agreed to ship a smaller feature set while fixing the most critical reliability issue first. The relationship improved because we focused on facts and shared outcomes rather than opinions.  
**可替换**：冲突点、你用的决策框架。