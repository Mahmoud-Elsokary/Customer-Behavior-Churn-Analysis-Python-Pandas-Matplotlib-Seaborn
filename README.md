# Customer Behavior & Churn Analysis

## Business problem

Customer retention is important for sustainable growth, but teams need to understand which observed customer behaviors and account attributes are associated with churn before deciding where to focus retention efforts. This project explores the supplied customer-behavior dataset to identify descriptive churn patterns across digital activity, product engagement, transaction behavior, customer-service contacts, push status, and saved-card status. The goal is to surface practical questions and evidence-based priorities—not to claim causal drivers.

## Talk about the data

The analysis uses the provided `customer_behaviour.csv` file. It contains **3,333 rows and 20 columns**, with one row per customer in the supplied file.

| Data area | Included fields |
|---|---|
| Customer/account | `account length`, `location code`, `user id` |
| Preferences/status | `credit card info save`, `push status` |
| Digital activity | `desktop sessions`, `app sessions`, `session duration` |
| Product engagement | `add to wishlist`, `total product detail views`, `sale product views`, `product detail view per app session`, `add to cart per session` |
| Promotions/value | `promotion clicks`, `avg order value`, `discount rate per visited products` |
| Transactions/support/outcome | `desktop transactions`, `app transactions`, `customer service calls`, `churn` |

**Data quality and preparation:** the dataset has **0 missing values**, **0 exact duplicate rows**, and **3,333 unique user IDs**. Four numeric fields use decimal-comma formatting in the CSV and are converted to numeric values by the existing notebook preparation steps. The project notebook retains its original code; explanatory Markdown insight cells have been added.

**Important assumptions:** the analysis interprets `churn = 1` as churned and `churn = 0` as retained, following the conventional binary encoding. Please confirm this with the data owner because no data dictionary was supplied. The measurement units, currency for `avg_order_value`, meaning of location codes, and exact definitions of status fields are not documented.

## Show the results of your analysis

### 1. Numerical behavior distributions

The selected-feature histograms show the spread of account length, digital activity, transactions, product engagement, promotions, order value, and customer-service contacts.

![Selected numerical behavior distributions](assets/selected_behavior_distributions.png)

Key observations:

- **2,411 customers (72.3%) have zero wishlist additions**; the median is zero while the mean is 8.10.
- Mean desktop sessions are 179.8 and mean app sessions are 30.6.
- Customer-service calls are right-skewed: the mean is 1.56, the median is 1, and the observed maximum is 9.

### 2. Churn distribution

![Customer churn distribution](assets/churn_class_distribution.png)

| Churn code | Customers | Share |
|---|---:|---:|
| `0` — treated as retained | 2,850 | 85.5% |
| `1` — treated as churned | 483 | 14.5% |

Under the stated encoding assumption, the overall churn rate is **14.5%**. The original notebook's pie chart labels are reversed under this mapping: `value_counts()` orders the observed classes as `0` then `1`, while the code labels the slices `churn` then `not_churn`. The supplemental chart above labels the classes explicitly and accurately under the stated assumption.

### 3. Categorical distributions

![Categorical distributions](assets/categorical_distributions.png)

The dataset is concentrated in location code `415` (1,655 records, 49.7%). `credit_card_info_save = yes` appears in 323 records, while `push_status = yes` appears in 922 records.

### 4. Segment-level churn insights

![Observed churn rates by status](assets/segment_churn_rates.png)

| Segment | Customers | Churned | Observed churn rate |
|---|---:|---:|---:|
| `push_status = yes` | 922 | 80 | 8.7% |
| `push_status = no` | 2,411 | 403 | 16.7% |
| `credit_card_info_save = yes` | 323 | 137 | 42.4% |
| `credit_card_info_save = no` | 3,010 | 346 | 11.5% |

Additional behavior comparisons by outcome:

| Metric (mean per record) | `churn = 0` | `churn = 1` |
|---|---:|---:|
| Desktop sessions | 175.21 | 206.94 |
| App sessions | 29.79 | 35.18 |
| App transactions | 4.53 | 4.16 |
| Customer-service calls | 1.45 | 2.23 |
| Wishlist additions | 8.60 | 5.12 |

**Business insights:**

1. **High customer-service contact count is a risk signal:** among customers with four or more calls, 138 of 267 are coded `churn = 1`, a **51.7%** segment churn rate. This group represents 8.0% of all records.
2. **Push status is associated with lower churn:** 8.7% for `yes` versus 16.7% for `no` (an 8.0-percentage-point difference). The data does not establish that push notifications cause the difference.
3. **Saved-card status shows an unusually high churn association:** 42.4% for `yes` versus 11.5% for `no`. Validate the definition, timing, and quality of this field before treating it as a business signal.
4. **Churned records show more sessions but slightly fewer app transactions on average:** 35.18 app sessions and 4.16 app transactions for `churn = 1`, versus 29.79 and 4.53 for `churn = 0`. Investigate the app conversion journey, but do not infer friction without event-level funnel data.
5. **Location codes provide little separation in this sample:** the churn rates for `408`, `415`, and `510` are 14.6%, 14.3%, and 14.9%, respectively.

## Next steps and limitations you faced

### Next steps

- Confirm the churn class mapping and obtain a data dictionary, including definitions for `push_status` and `credit_card_info_save`.
- Investigate support reasons for the 4+ service-call segment and test a service-recovery intervention.
- Run a randomized or otherwise controlled experiment before concluding that push communication improves retention.
- Audit the saved-card segment and check whether its high churn rate reflects field semantics, timing, or cohort composition.
- Collect event-level app funnel data to understand the relationship between sessions, product views, cart additions, and transactions.
- With a larger time-aware dataset, consider a baseline churn model and evaluate class-sensitive metrics such as recall, precision, and PR-AUC in addition to accuracy.

### Limitations

- No data dictionary, measurement units, currency definition, or mapping for location codes was provided.
- There is no date/time field, so retention trends, cohorts, and behavior before/after churn cannot be evaluated.
- The analysis is descriptive; it includes no statistical significance tests, causal design, or predictive model.
- The outcome class is imbalanced (85.5% class `0` vs 14.5% class `1`).
- The original notebook contains a reversed pie-chart label assignment under the conventional `1 = churn` interpretation. A later automatic histogram also includes `user_id` and `location_code` and excludes some later numeric columns because its 5×3 plotting grid is fixed.
- The field meaning `1 = churned` is a working assumption pending confirmation from the data owner.

---

**Tools:** Python, Pandas, Matplotlib, Seaborn, Jupyter Notebook.
