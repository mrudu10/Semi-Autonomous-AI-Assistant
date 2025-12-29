# LinkedIn InMail Assistant: Semi-Autonomous AI Agent 

### Problem statement: 
Often times, We connect with a lot of people every week to talk and learn from their journey, or to get some guidance or just to have a coffee chat! . Unfortunately, we get lost in 100's of connections we make every week and end up not actually breaking the ice with even 10% of people Solution: So here is a Automation workflow I have created to assist me get insights on what is Unique in their profile, what is that 1 thing that is common between both of us and finally, what is a perfect message to reach out to them

---

### Objective statement (for your n8n project)

Build a **semi-autonomous LinkedIn outreach assistant** that turns new LinkedIn connections into **high-quality, personalized ice-breaker cold messages** by automatically extracting profile signals, identifying **shared context** (common ground), and generating a **ready-to-send message draft**—while filtering to only relevant roles (PMs / Hiring Managers) and storing the enriched outputs in a database for follow-up.

---

### n8n Workflow:

<img width="1844" height="834" alt="Assignment 1" src="https://github.com/user-attachments/assets/894e4124-85c4-437d-b7ed-61ea2fee8910" />

---

### Tools & Techniques Implemented:
1. Data Handling techniques: Data Cleaning - Formatting into JSON, Converting raw text to tables (split-out), removing duplicate entries, merge data
2. Tools connected: Linkedin, n8n, ListConnections API integration
---

## Environment interaction (how your agent interacts with its surroundings)

### 1) Data Environment Monitoring

**What it monitors**

* New connection data coming from BrowserFlow (name, headline, role, company, “about”, recent activity if available)
* Data quality signals: duplicates, missing fields, noisy/unstructured text, irrelevant profiles

**How it reacts**

* Runs cleaning + dedupe
* Validates required fields before calling OpenAI (to reduce garbage-in → garbage-out)
* Flags “low-signal profiles” (e.g., empty bio) and falls back to a simpler message template

**Example in your flow**

* Split Out + Remove Duplicates + Edit Fields steps ensure consistent structured inputs to the LLM.

---

### 2) System Environment Monitoring

**What it monitors**

* External system health + constraints:

  * BrowserFlow API availability / rate limits
  * OpenAI latency / failure / token limits
  * n8n execution errors, retries, partial failures
  * Database insert success/fail

**How it reacts**

* Retry on transient failures (API timeouts)
* Circuit-break / fallback if OpenAI fails (store profile + “message_pending” status)
* Logging + traceability (store prompt version, run timestamp, error codes)

**Example in your flow**

* If OpenAI node fails, you can still write a record to DB with “generation_failed” and rerun later.

---

### 3) User Environment Monitoring

**What it monitors**

* Your intent and preferences (implicit “user context”):

  * Target personas: Product Managers + Hiring Managers
  * Tone style: friendly, concise, coffee-chat vibe
  * Your background anchors: GenAI/Product, Syracuse, Freshworks UK, etc. (whatever you want the agent to use consistently)

**How it reacts**

* Adapts message framing depending on recipient type:

  * PM → product curiosity + shared craft
  * Hiring Manager → value proposition + role alignment
* Avoids awkward assumptions (asks for missing info or uses safe defaults)

**Example in your flow**

* Filter step enforces: “Only people in my domain.”
* Prompting step injects “what’s unique + what’s common + draft message”.

---


