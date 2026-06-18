# CS 499 Capstone — Milestone Four Narrative
## Databases Enhancement
**Amanda Willbanks**

---

### Artifact Description

The artifact I selected for the databases category is the same Grazioso Salvare Dashboard I enhanced in Milestones Two and Three. It is a Python-based interactive web dashboard originally developed in December 2025 during CS-340: Client/Server Development. The dashboard connects to a MongoDB database containing animal shelter records from the Austin Animal Center and allows users to filter, explore, and visualize data to identify animals suitable for specialized rescue training programs. The application is built using the Dash and JupyterDash frameworks, with Pandas for data handling, Plotly Express for charting, and Dash Leaflet for geolocation mapping. It follows the Model-View-Controller design pattern, with a separate CRUD module serving as the model layer.

For this milestone, the enhanced version is `ProjectTwoDashboard-Complete-Enhanced-v3.ipynb`, paired with an updated `CRUD_Python_Module`. These build on the v2 version from Milestone Three and add the database-specific improvements described below.

---

### Justification for Inclusion

I selected this artifact for the databases category because the gap between its original state and a well-engineered database layer is specific and instructive. In both the original dashboard and the Milestone Two version, the callback function responsible for handling user filter selections was also responsible for constructing raw MongoDB query documents — field names, `$in` operators, `$lte` and `$gte` range comparisons — inline, inside controller code. That is a violation of the separation of concerns that the MVC pattern is supposed to enforce. The model layer is supposed to own all data access logic. The controller is supposed to ask for data by intent, not by implementation detail.

The most significant enhancement in this milestone is moving query construction out of the dashboard and into the CRUD module. I added a module-level `_RESCUE_QUERIES` dictionary to `CRUD_Python_Module` that maps each rescue type code to its complete MongoDB query document:

```python
_RESCUE_QUERIES = {
    "WR":  { "animal_type": "Dog", "breed": {"$in": [...]}, ... },
    "MWR": { "animal_type": "Dog", "breed": {"$in": [...]}, ... },
    "DIT": { "animal_type": "Dog", "breed": {"$in": [...]}, ... },
}
```

The new `get_rescue_candidates(rescue_type)` method uses this dictionary to build and execute the appropriate query, returning cleaned records to the caller. The dashboard's `fetch_or_cache()` function now calls `shelter.get_rescue_candidates(filter_type)` for rescue filters — it no longer constructs any MongoDB syntax. If a field is renamed in the database schema, only `_RESCUE_QUERIES` in the CRUD module needs to change. The dashboard callback is untouched.

The second enhancement is input validation inside `get_rescue_candidates()`. Before executing any query, the method checks that `rescue_type` is a string and that it is a recognized key in `_RESCUE_QUERIES`. If either check fails, it raises a typed exception — `TypeError` or `ValueError` — with a descriptive message that names the invalid input and lists the valid options. The `fetch_or_cache()` function in the notebook catches these exceptions specifically and logs them clearly, rather than letting them propagate as generic database errors. This validation pattern follows the principle that the model layer should be the boundary where data contract violations are caught and communicated, not silently swallowed.

The third enhancement is a new `read_sample(limit=100)` method that returns a small number of records without loading the full collection. The original dashboard's startup sequence called `fetch_df({})` — an empty query that returned all 10,000 records into memory immediately. That result was used only to establish the DataTable's column schema and provide a fallback for the map callback. Fetching 10,000 documents to get a column list is wasteful, and it gets more expensive as the collection grows. In v3, the startup sequence calls `shelter.read_sample(100)` instead. The full dataset loads on demand only when the user explicitly selects the All Animals filter, at which point `fetch_or_cache("ALL")` runs the full query and caches the result for the remainder of the session. `read_sample()` also validates its `limit` parameter, raising a `ValueError` for non-positive integers.

The fourth enhancement is a documented index strategy in the CRUD module. The filter queries run against four fields: `animal_type`, `breed`, `sex_upon_outcome`, and `age_upon_outcome_in_weeks`. On an unindexed collection, each query performs a full O(n) collection scan. The module now includes a comment block documenting two specific indexes that should be created:

```
db.animals.createIndex({ animal_type: 1, breed: 1, sex_upon_outcome: 1 })
db.animals.createIndex({ age_upon_outcome_in_weeks: 1 })
```

The first covers the three exact-match and `$in` fields shared by all rescue queries. The second supports the numeric range comparisons. Together, they reduce per-query cost from O(n) to approximately O(log n + k), where k is the number of matching documents. This documentation lives in the module because that is where a future developer deploying this application will look for database configuration guidance.

---

### Course Outcome Alignment

The enhancements I completed in this milestone align with the outcomes I planned in Module One. Specifically:

- **Course Outcome 1** (building collaborative environments that enable diverse audiences to support organizational decision making): The `get_rescue_candidates()` method, its typed exceptions, and the documented index strategy all make the codebase easier for a team to work with. A future developer adding a new rescue type edits `_RESCUE_QUERIES` in one place — the model layer — without needing to understand how the dashboard callback is structured. A database administrator reading the CRUD module finds exactly what indexes to create, documented with the reasoning behind them. These are forms of collaboration encoded in code structure rather than documentation written after the fact.

- **Course Outcome 2** (designing and delivering professional-quality technical communications): Moving query logic into the model layer and documenting it with clear method signatures and an index strategy is a form of technical communication through code structure. A developer reading `shelter.get_rescue_candidates("WR")` in the callback immediately understands what is being requested without needing to interpret MongoDB query syntax. The index documentation in the CRUD module communicates deployment requirements directly to whoever sets up the database.

- **Course Outcome 3** (designing and evaluating computing solutions): The decision to replace the full startup load with a 100-record sample is an explicit performance trade-off: the DataTable initializes slightly faster and with less memory, at the cost of showing fewer rows before the user makes a filter selection. Documenting the compound index strategy is an evaluation of query performance — I identified which fields the queries touch, reasoned about scan cost, and specified the minimum index configuration needed to make the queries efficient.

- **Course Outcome 4** (using well-founded techniques and tools to implement solutions that deliver value and accomplish industry-specific goals): The architectural change that moves query construction into the model layer is a direct application of the MVC pattern as it is practiced in industry-standard database-backed applications. The `get_rescue_candidates()` method, the `_RESCUE_QUERIES` configuration dictionary, and the `read_sample()` method are all examples of database engineering techniques — method-level encapsulation, separation of query logic from application logic, and performance-aware data access patterns — that would appear in any professional codebase using a document database.

- **Course Outcome 5** (developing a security mindset): Input validation at the model boundary is a direct application of the security principle of validating at system entry points. The `get_rescue_candidates()` method raises typed exceptions for invalid inputs rather than passing them through to the database driver, where they could produce unexpected behavior. In a more complex application where query parameters could include user-supplied text, this kind of validation at the model layer would be the primary defense against query injection patterns.

I have now completed enhancements in all three categories as planned in Module One. All five course outcomes are covered across the three milestones.

---

### Reflection

The database enhancement taught me something that I did not fully appreciate when I was a CS-340 student: the location of code matters as much as the correctness of code. The MongoDB queries I wrote for the original dashboard were technically correct — they returned the right animals. But they were in the wrong place. Having query construction inside a Dash callback means that the callback has two distinct responsibilities: responding to user input and knowing how to talk to the database. Those are different jobs, and mixing them makes both harder to change independently.

The experience of actually moving the queries into the CRUD module made the benefit concrete in a way that the principle alone does not. Once `_RESCUE_QUERIES` and `get_rescue_candidates()` existed in the model, I looked at `fetch_or_cache()` in the notebook and realized it had become much simpler — one line to call the CRUD method, rather than a block of code assembling field names and operators. The dashboard no longer needs to know that `sex_upon_outcome` is the field name, or that breed filtering uses `$in`, or that age filtering uses `$lte`. It just asks the model for Water Rescue candidates and gets them back. That reduction in what the callback needs to know about is what "separation of concerns" actually means in practice.

The startup query change was smaller in code but clarifying in reasoning. I had to ask myself why the application was loading 10,000 records before the user had done anything. The honest answer was: because the original code did it, and it worked, so no one questioned it. Going back and asking "what is this actually for?" — and discovering the answer was "only to get column names and serve as a map fallback" — made it obvious that a 100-record sample would serve both purposes at a fraction of the cost. That habit of questioning the purpose of existing code, rather than assuming it is necessary because it is there, is probably the most transferable skill I developed across all three enhancements in this capstone.

The most significant challenge I faced was deciding how to handle the fact that `RESCUE_CRITERIA` in the notebook and `_RESCUE_QUERIES` in the CRUD module both describe the same rescue types but serve different purposes. `RESCUE_CRITERIA` drives the scoring algorithm and contains fields like `primary_breeds` and `age_optimal_min` that have nothing to do with database queries. `_RESCUE_QUERIES` contains raw MongoDB query documents that have nothing to do with scoring. I had to resist the temptation to merge them into one shared configuration, because doing so would have re-entangled the layers I was trying to separate. The challenge was accepting that two representations of the same domain concept can coexist cleanly when they serve genuinely different roles — one belongs to the algorithm layer, the other belongs to the data layer — and that this duplication is intentional, not a mistake to be refactored away.
