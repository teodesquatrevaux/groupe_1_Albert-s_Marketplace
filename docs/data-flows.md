# Data flows

One row per flow: where the data comes from, what it is, who uses it, how often
it moves, and in which format.

| # | Source | Data | Consumer | Frequency | Format | Notes |
|---|---|---|---|---|---|---|
| 1 | Shop (website, checkout) | Orders, customers, products, reviews, saved cards, passwords | PostgreSQL (25 tables) | On every user action | SQL rows (transactional) | Frequency inferred: the brief does not state it |
| 2 | PostgreSQL | The same tables, with no separation between business data and sensitive data | Analysts, via direct queries | On demand (ad hoc) | SQL queries | Analysts read the tables the shop writes to |
| 3 | PostgreSQL | Table export | Analysts' spreadsheets | Every night | CSV | Exact scope not stated in the brief |


## Under the matrix, answer

- Which flows carry personal data?
  Flows 1 and 2 (customers, saved cards, passwords). Flow 3 as well if the export includes these tables, which the brief does not specify.
- Which consumers read directly from a system that also serves customers?
  The analysts (flow 2): they query the PostgreSQL database that the shop uses.
- Where could the same figure be computed twice, in two different ways?
  Revenue (flow 4): it is recomputed in several spreadsheets from the CSV export, which explains why the reports disagree.
