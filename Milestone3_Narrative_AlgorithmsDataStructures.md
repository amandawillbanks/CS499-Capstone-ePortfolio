# CS 499 Capstone — Milestone Three Narrative
## Algorithms and Data Structure Enhancement
**Amanda Willbanks**

---

### Artifact Description

The artifact I selected for the algorithms and data structure category is the same Grazioso Salvare Dashboard I enhanced in Milestone Two. It is a Python-based interactive web dashboard originally developed in December 2025 during CS-340: Client/Server Development. The dashboard connects to a MongoDB database containing animal shelter records from the Austin Animal Center and allows users to filter, explore, and visualize data to identify animals suitable for specialized rescue training programs. The application is built using the Dash and JupyterDash frameworks, with Pandas for data handling, Plotly Express for charting, and Dash Leaflet for geolocation mapping. It follows the Model-View-Controller design pattern, with a separate CRUD module handling all database interactions.

---

### Justification for Inclusion

I selected this artifact for the algorithms and data structure category because it contains a natural, domain-grounded problem that a well-designed algorithm can meaningfully solve. The Milestone Two version of the dashboard, while improved from the original, still used a binary filter: an animal either matched the MongoDB query or it did not. When thirty dogs met the Water Rescue criteria, they appeared in whatever order MongoDB returned them — insertion order — with no indication of which ones were the strongest candidates. That is a ranking problem, and ranking problems are where algorithms earn their value.

The enhancements I implemented in this milestone directly address that gap. The primary addition is `compute_suitability_score()`, a weighted scoring function that assigns a numeric rescue suitability score to each animal based on three criteria: breed match quality (5 points for a primary match, 3 for a secondary match), age within the optimal trainability window (3 points), and sex match (2 points), for a maximum of 10. The primary versus secondary breed distinction required extending `RESCUE_CRITERIA` with a new `primary_breeds` key for each rescue type. For Water Rescue, for example, the Labrador Retriever Mix is the primary breed — the most proven water-rescue dog — while the Chesapeake Bay Retriever and Newfoundland are secondary. This domain knowledge is encoded directly in the data structure so the scoring function can apply it without any hard-coded per-rescue-type conditionals.

The age scoring adds a second layer of nuance. Rather than awarding points to any animal that passes the filter boundary, the function compares the animal's age to a tighter `age_optimal_min` and `age_optimal_max` window added to each criteria entry. A 12-week Water Rescue candidate, well within the peak socialization window, scores 3 age points. A 25-week candidate that technically qualifies under the 26-week filter cap scores 0. The companion function `rank_candidates()` applies the scoring across the entire filtered DataFrame and sorts descending by score so the strongest candidates appear at the top of the table automatically.

The second enhancement is `BREED_INDEX`, a precomputed inverted index built once at startup from `RESCUE_CRITERIA`. It maps each breed name to the set of rescue type codes it qualifies for, enabling O(1) lookup anywhere in the application instead of an O(k×n) scan across all criteria entries each time. A lookup like `BREED_INDEX["German Shepherd"]` immediately returns `{"MWR", "DIT"}` — the same information that would otherwise require checking every rescue type's breed list.

The third enhancement is `_query_cache`, a module-level dictionary that caches MongoDB query results by filter type key. Because rescue queries are deterministic — the same filter always produces the same query and, on a static dataset, the same results — the cache never becomes stale during a session. The `fetch_or_cache()` function checks the cache before issuing any database query, so a user who switches between filter types does not trigger redundant round-trips for previously fetched results.

The fourth enhancement makes the chart context-aware through `_CHART_CONFIG`, a dictionary that maps each filter type to a specific column and chart type. When All Animals is selected, the chart shows the familiar breed distribution pie. When Water Rescue or Mountain Rescue is active, it switches to an age histogram, because age is the most informative differentiator within a result set that already constrains breed. When Disaster Individual Tracking is active, it shows an outcome type pie chart, because tracking candidates span a wide age range and outcome type reveals the intake pathway that correlates with trainability. The `update_graphs` callback now takes `filter_type` as a second input and selects the appropriate visualization at runtime.

---

### Course Outcome Alignment

The enhancements I completed in this milestone align with the outcomes I planned in Module One. Specifically:

- **Course Outcome 3** (designing and evaluating computing solutions using algorithmic principles): Designing the scoring function required explicit decisions about weighting — why breed earns 5 points for a primary match and 3 for a secondary match, why the optimal age window is rewarded separately from the filter boundary. These are not arbitrary numbers; they reflect the relative importance of each criterion in rescue training selection, and they can be argued for or changed by a domain expert. The algorithm can be evaluated against those design decisions, which is what makes it a genuine algorithmic solution rather than ad-hoc logic.

- **Course Outcome 4** (using innovative techniques and tools to deliver value): The ranked output is a meaningfully more useful tool for the end user. A trainer evaluating candidates no longer needs to manually scan and compare animals — the top scorers surface automatically. The context-aware chart provides informative visualizations tailored to each filter state rather than a generic breed count that becomes redundant once a rescue filter already constrains breed.

- **Course Outcome 1** (building collaborative environments): The `BREED_INDEX` and `_CHART_CONFIG` data structures follow the same configuration-over-hard-coding principle established in Milestone Two. A future developer who needs to add a new rescue type modifies one dictionary entry and gets correct scoring, correct caching, and correct chart selection automatically.

I do not have updates to my outcome-coverage plans at this time. The Category 3 enhancement addressing databases remains planned for the next milestone.

---

### Reflection

The most significant thing I learned while building this enhancement is how much the choice of data structure shapes the algorithm that uses it. When I first designed the scoring function in my Milestone One code review pseudocode, I described breed matching as a lookup against a list. Once I sat down to implement it, I realized that a list lookup is O(n) per animal, and running that across thousands of rows adds up. Converting the breed lists to Python sets made each individual lookup O(1). That is a small change in code but a meaningful shift in thinking — I was no longer just writing a function, I was making a deliberate algorithmic decision with a measurable consequence.

The inverted `BREED_INDEX` came from the same reasoning. I wanted a way to answer "what rescue types does this breed qualify for?" without scanning all criteria every time. Precomputing the index at startup trades a small amount of memory for fast runtime lookups — the canonical space-time tradeoff. Encountering that tradeoff in a running application rather than a textbook exercise made it feel concrete in a way that classroom examples often do not.

The hardest design decision was choosing the scoring weights. There is no objectively correct answer to whether breed match should be worth 5 points or 7, or whether age should outweigh sex. I treated this as a decision that needed to be made explicitly, documented, and justified — not tuned by trial and error. The weights I chose reflect the training priorities described in the Grazioso Salvare project requirements, where breed type and age trainability window are the primary selection criteria and sex is a secondary preference. Separating those weights into the `RESCUE_CRITERIA` data structure means a domain expert can adjust them by editing one dictionary, with no changes to the scoring algorithm itself. Learning to make that kind of separation — between what an algorithm does and the parameters that configure how it does it — is probably the most durable insight I am taking from this milestone.
