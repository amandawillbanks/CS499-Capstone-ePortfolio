# CS 499 Capstone — Milestone Two Narrative
## Software Design and Engineering Enhancement
**Amanda Willbanks**

---

### Artifact Description

The artifact I selected for the software design and engineering category is the Grazioso Salvare Dashboard, a Python-based interactive web application I originally developed in December 2025 during CS-340: Client/Server Development. The dashboard connects to a MongoDB database containing animal shelter records from the Austin Animal Center and allows users to filter, explore, and visualize data to identify animals that are suitable candidates for specialized rescue training programs. The application is built using the Dash and JupyterDash frameworks, with Pandas for data handling, Plotly Express for charting, and Dash Leaflet for geolocation mapping. It follows the Model-View-Controller design pattern, where a separate CRUD module handles all database interactions.

---

### Justification for Inclusion

I selected this artifact for my ePortfolio because it represents a complete, functional full-stack application that touches several areas of computer science simultaneously — database integration, interactive UI design, data visualization, and backend logic — all within a single, reviewable codebase. This makes it a strong vehicle for demonstrating growth across multiple competencies.

The specific components that showcase my software design and engineering skills are the enhancements I made to the code during this milestone. The original artifact, while functional, had several significant weaknesses that I identified during my Milestone One code review. The most serious was that database credentials were hardcoded as plain-text strings directly in the notebook:

```python
raw_username = "aacuser"
raw_password = "mongolygosh"
```

This is a real security vulnerability — if the notebook were shared, pushed to a repository, or accessed by another user, those credentials would be immediately exposed. In my enhanced version, I replaced these lines with environment variable lookups using `os.environ.get()`, and added a clear `EnvironmentError` with instructions if the variables are not set. This reflects industry-standard credential management practice.

The second major improvement was extracting the rescue filter criteria out of the callback function and into a dedicated configuration dictionary called `RESCUE_CRITERIA`. In the original code, the breed lists, age ranges, and sex requirements for each rescue type were embedded directly inside the callback logic as raw MongoDB query dictionaries. This meant that changing a single breed name required locating and editing the callback — a maintainability risk. In the enhanced version, all criteria live in one place at the top of the file, and a new helper function, `build_query_from_criteria()`, translates them into MongoDB query documents. Adding a new rescue type now requires only a new entry in the dictionary, with no changes to the callback itself.

Third, I replaced fragile positional index lookups in the map callback — such as `dff.iloc[row, 4]` and `dff.iloc[row, 9]` — with named column lookups using `dff.loc[row, "breed"]` and `dff.loc[row, "name"]`. The original magic numbers would silently return wrong data if the dataset schema ever changed. The named approach is both safer and self-documenting.

Finally, I added meaningful inline comments throughout the code explaining the business logic behind specific decisions — why the Water Rescue age cap is 26 weeks, why the map is centered on coordinates 30.75, -97.48, and why `selected_rows` is initialized to zero.

---

### Course Outcome Alignment

The enhancements I completed in this milestone align with the outcomes I planned in Module One. Specifically:

- **Course Outcome 1** (building collaborative environments): Clean, modular, documented code is what makes it possible for a team to work together on a codebase. Extracting criteria into a named dictionary and adding docstrings to every function directly supports this outcome.
- **Course Outcome 3** (designing and evaluating computing solutions): The decision to separate configuration from logic is a deliberate design trade-off with measurable maintainability benefits. I designed `build_query_from_criteria()` to be reusable and testable independently of the callback.
- **Course Outcome 5** (developing a security mindset): Replacing hardcoded credentials with environment variables directly addresses a real, identified vulnerability. This change was motivated by anticipating what would happen if this notebook were shared — a realistic scenario in any collaborative or academic environment.

I do not have updates to my outcome-coverage plans at this time. The Category 2 and Category 3 enhancements, which address algorithms and databases respectively, remain planned for upcoming milestones.

---

### Reflection

The process of enhancing this artifact taught me that the gap between code that works and code that is well-engineered is often wider than it appears when you first write it. When I built the original dashboard in CS-340, my goal was functionality — getting the filters to work, getting the map to render, getting the table to update. I succeeded at that goal, but the code I produced was brittle in ways I did not fully appreciate at the time.

Going back to that code five months later with fresh eyes and a code review checklist was genuinely revealing. The hardcoded credentials stood out immediately, but the structural issues — the rescue criteria buried inside callback logic, the magic index numbers — were subtler. Those are the kinds of problems that do not break anything right away but create significant pain as a codebase grows or changes hands.

The most meaningful challenge I faced was resisting the urge to over-engineer. There are many more things I could refactor — splitting the notebook into separate modules, adding unit tests, building a configuration file system. But the assignment asked me to make targeted, planned improvements, not to rewrite the application from scratch. Learning to scope enhancements appropriately, making deliberate trade-off decisions, and stopping when the planned work is done are themselves important software engineering skills. I came away with a clearer appreciation for the difference between planned, purposeful improvement and unbounded refactoring.
