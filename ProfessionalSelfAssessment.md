# Professional Self-Assessment

**Amanda Willbanks**
**CS 499 — Computer Science Capstone**
**Southern New Hampshire University**

---

## Introduction

Completing the Computer Science program at SNHU has given me the opportunity to build, evaluate, and improve software across multiple domains, from database-backed web applications and mobile development to software testing, security analysis, and collaborative team projects. The capstone course, CS 499, asked me to take an artifact from earlier in my program and enhance it in ways that demonstrate growth across the full breadth of the discipline. That process — selecting an artifact, conducting a formal code review, planning targeted improvements, and executing them across three distinct categories, forced me to revisit my own earlier work with a critical eye and apply everything I have learned since I first wrote it.

This ePortfolio is the result. It presents a single artifact, the Grazioso Salvare Dashboard, enhanced across three milestones to demonstrate competence in software design and engineering, algorithms and data structures, and databases. But the skills that made those enhancements possible were developed across the entire program, not just in one course. This self-assessment introduces those skills, explains how the program shaped them, and connects them to the technical work that follows.

---

## Collaborating in a Team Environment

Software development is rarely a solo activity, and several courses in the program emphasized that directly. In CS 310, I worked on a collaborative team project where shared version control, code reviews, and coordinated task planning were essential to delivering a working product. That experience taught me that writing code other people can read, extend, and maintain is as important as writing code that runs correctly.

That principle carried directly into my capstone enhancements. When I extracted the rescue filter criteria into a configuration dictionary in Milestone Two, I was not just improving maintainability for myself — I was making the codebase navigable for any future developer who might need to add a rescue type or modify a filter. When I documented the recommended MongoDB index strategy in the CRUD module in Milestone Four, I was writing for a database administrator who might never read the dashboard code at all. These are collaborative design decisions: structuring code so that different team members can work on different layers independently, without needing to understand the entire system at once.

---

## Communicating with Stakeholders

Technical skill loses much of its value if it cannot be communicated clearly to the people who depend on it. CS 255 and CS 250 both required translating between technical implementation and business requirements, creating system design documents, writing user stories, and presenting technical decisions in terms that non-technical stakeholders could evaluate.

In the capstone, this skill showed up most clearly in the enhancement narratives I wrote for each milestone. Each narrative explains not just what I changed, but why it matters, why moving credentials out of source code reduces organizational risk, why a weighted scoring algorithm is more useful than an unranked list, why separating query logic from controller logic reduces the cost of future schema changes. The ability to articulate the business value of a technical decision, not just the technical mechanism, is something I consider one of my strongest professional skills.

---

## Data Structures and Algorithms

CS 260 gave me my formal introduction to data structures and algorithmic analysis, but the capstone is where I applied those concepts to a real, domain-specific problem. The Milestone Three enhancement required me to design a weighted scoring algorithm that ranks rescue candidates by suitability, accounting for breed match quality, age within an optimal trainability window, and sex preference. Designing the scoring weights required domain reasoning, not just algorithmic knowledge: understanding why a primary breed match should carry more weight than a secondary match, and why the optimal age window should be narrower than the filter boundary.

Beyond the scoring algorithm itself, I built an inverted index data structure that maps breed names to rescue type codes for O(1) lookup, and I implemented a query result cache that eliminates redundant database round-trips for previously fetched filter results. These are straightforward applications of the space-time tradeoff principle — trading a small amount of memory at startup for faster runtime performance, but encountering them in a running application rather than a textbook exercise made the tradeoff feel concrete and consequential.

---

## Software Engineering and Database

Software engineering and database design were the two areas where the gap between my original CS 340 work and professional-quality code was most visible, and where the enhancements I made during the capstone were most substantive.

On the software engineering side, the original dashboard had hardcoded credentials, rescue filter logic embedded inside UI callbacks, and fragile positional index lookups that would silently return wrong data if the dataset schema changed. These are the kinds of problems that do not break anything immediately but create significant risk as a codebase evolves. The Milestone Two enhancements addressed each one: environment variable credential management, a centralized configuration dictionary, and named column references. CS 320 had reinforced the importance of writing testable, maintainable code, and CS 305 had sensitized me to the security implications of credential exposure — both of those courses directly informed the decisions I made in this milestone.

On the database side, the most significant enhancement was architectural. The original dashboard constructed raw MongoDB query documents — field names, `$in` operators, range comparisons — directly inside Dash callback functions. That violated the separation of concerns that the MVC pattern is supposed to enforce. In Milestone Four, I moved all query construction into the CRUD module, behind a `get_rescue_candidates()` method that accepts a rescue type code and returns results. The dashboard no longer needs to know what fields the database uses or how queries are structured. I also replaced the full 10,000-record startup load with a 100-record sample method and documented a compound index strategy that reduces query cost from O(n) collection scans to O(log n + k) index scans. DAD 220 introduced me to database design principles, and CS 340 gave me hands-on experience with MongoDB, the capstone enhancement was my opportunity to demonstrate that I can apply both at an architectural level, not just a functional one.

---

## Security

Security is not a feature that gets added at the end, it is a mindset that should inform decisions throughout development. CS 305 taught me to think about software from an adversarial perspective: what happens if this input is malicious, what happens if this credential is exposed, what happens if this validation is missing.

The most concrete security improvement in my capstone work was replacing hardcoded database credentials with environment variable lookups. The original notebook contained a plain-text username and password that would have been exposed to anyone who accessed the file — a realistic scenario in any shared or version-controlled environment. Beyond credential management, I added input validation at the model boundary in Milestone Four: the `get_rescue_candidates()` method raises typed exceptions for invalid inputs rather than passing them through to the database driver. In a more complex application where query parameters could include user-supplied text, this kind of validation would be the primary defense against injection attacks. These are small changes in code but meaningful shifts in professional practice — the habit of asking "what could go wrong if this input is not what I expect?" before writing the happy path.

---

## Portfolio Summary: How the Artifacts Fit Together

The technical artifacts in this ePortfolio all derive from a single project — the Grazioso Salvare Dashboard — enhanced across three capstone milestones. I chose a single artifact deliberately, because demonstrating depth of improvement across multiple dimensions of the same codebase illustrates a more complete engineering perspective than surface-level work across unrelated projects.

The three enhancements build on each other in a logical progression:

- **Milestone Two — Software Design and Engineering** established the structural foundation: secure credential handling, a clean configuration layer, and self-documenting code. These changes made the codebase safe to share and maintainable enough to extend.

- **Milestone Three — Algorithms and Data Structures** added analytical capability on top of that foundation: a weighted scoring algorithm that ranks rescue candidates by suitability, an inverted index for efficient breed-to-rescue-type lookup, query result caching, and context-aware data visualization. These enhancements transformed the dashboard from a filter-and-display tool into a decision-support system.

- **Milestone Four — Databases** completed the architectural separation by moving all query construction into the model layer, adding input validation at the data boundary, optimizing startup performance with a sample-based load strategy, and documenting the index configuration needed for production-level query performance.

Together, the three milestones demonstrate that I can take a working but fragile application, identify its structural weaknesses through a systematic code review, plan targeted improvements, and execute them with attention to security, performance, maintainability, and user value. The enhancement narratives that accompany each artifact document the reasoning behind every design decision — not just what changed, but why it matters and which course outcomes it addresses.

The artifacts that follow this self-assessment include the original dashboard, each enhanced version, the CRUD module, and the milestone narratives. They represent the strongest evidence I can offer of my readiness to contribute as a software professional.
