# Visualising Data with Extreme Outliers in Power BI

Sample data, semantic model and two Power BI reports accompanying the endjin blog post [Visualising Data with Extreme Outliers in Power BI: A Practical Guide](https://endjin.com/blog/2026/02/visualising-data-with-extreme-outliers-in-power-bi).

The post compares nine ways of charting data where most values sit in a narrow range but a few extreme outliers stretch the axis. This repository lets you open each of those techniques in Power BI Desktop, look at the DAX behind them, and try them on your own data.

## What's in the repository

| Path | What it is |
|------|------------|
| `workflow_processing_times.csv` | The sample data: 600 rows covering 120 work items, each with the time spent in five workflow states. Five items are extreme outliers. |
| `workflow-processing-times.SemanticModel/` | The semantic model, in TMDL. Both reports share it. Contains the data table, an `ItemSummary` table with one row per item, and the `DAX Measures` table. |
| `approach-examples.pbip` and `approach-examples.Report/` | One page per approach from the post, each with a sidebar explaining what to look for. |
| `suggested-dashboard.pbip` and `suggested-dashboard.Report/` | The four-page dashboard described in the post's *Suggested Dashboard Structure* section, built from the same model. |

The projects use the Power BI Project (PBIP) format with the enhanced report format (PBIR), so every file is text and can be read on GitHub without Power BI.

## Opening the reports

You need Power BI Desktop (a recent monthly release) with two options turned on under **File > Options and settings > Options > Preview features**:

- **Power BI Project (.pbip) save option**
- **Store reports using enhanced metadata format**

Without these, Desktop will refuse to open the `.pbip` files.

Then:

1. Clone or download this repository.
2. Open `approach-examples.pbip` (or `suggested-dashboard.pbip`) in Power BI Desktop.
3. Go to **Transform data > Edit parameters** and set `DataFolder` to the full path of the folder you cloned into (the folder containing `workflow_processing_times.csv`).
4. Click **Refresh**.

The model loads its data from the CSV at refresh time, and the data cache is not committed to the repository, so the first open shows an empty model until you refresh. Open one project at a time: both reports point at the same semantic model folder.

The *Box Plot* page in `approach-examples` uses the [Box and Whisker Chart by MAQ Software](https://appsource.microsoft.com/en-us/product/power-bi-visuals/WA104381351) custom visual. Desktop fetches it from AppSource automatically when the page is opened; if you are offline, that page shows placeholders.

## The sample data

Each row of `workflow_processing_times.csv` records the time one item spent in one workflow state.

| Column | Meaning |
|--------|---------|
| `ItemId` | Work item identifier, `WI-001` to `WI-120` |
| `WorkflowState` | `Received`, `Validation`, `Processing`, `Review` or `Complete` |
| `ProcessingTimeMinutes` | Minutes spent in that state |
| `ProcessedDate` | Date the state was entered |
| `Category` | `Invoice`, `Purchase Order` or `Contract` |
| `Priority` | `Normal` or `High` |

Across all five states, 115 of the 120 items take between 33 and 111 minutes in total (median 71). The other five take between 7,465 and 14,465 minutes, which is five to ten days. The post treats any item whose total exceeds 120 minutes as an outlier.

The data is synthetic, generated to resemble a real engagement.

## The semantic model

Three tables:

- **`WorkflowProcessingTimes`** is the CSV as loaded, plus two calculated columns (`ProcessingTimeBucket` and its sort column) used by the per-step histogram.
- **`ItemSummary`** has one row per item, built in Power Query by grouping the data table. It carries each item's `TotalProcessingTime`, `StartDate`, a `TotalTimeBucket` for the histogram, a `WeekStarting` column for trend charts and an `ItemType` column that is `Outlier` or `Normal`.
- **`DAX Measures`** holds the 22 measures, in display folders that match the post: *Per-Step Statistics*, *Per-Item Statistics*, *Outlier Detection*, *Capped Axis Visualisation*, *Separate Normal/Outlier Visualisation* and *Formatting*. The post's DAX listings are taken from this table, so the names match.

`Outlier Threshold` is a measure (120). Change it there and every outlier calculation, colour rule and bookmark in both reports follows.

## Where each approach lives in `approach-examples`

| Post section | Report page |
|--------------|-------------|
| The Problem | *The Visualisation Challenge*, top chart |
| Approach 1: Logarithmic Scale | *The Visualisation Challenge*, bottom chart |
| Approach 2: Capped Axis with Outlier Indicators | *Capped Axis* |
| Approach 3: Separate Visualisations for Normal and Outlier Data | *Normal vs Outliers* |
| Approach 4: Drill-Down / Zoom Interaction | Not a separate page. Turn on the y-axis *Zoom slider* on any per-item chart. The bookmark variant is on the *Detailed Drill-Through* page of `suggested-dashboard`. |
| Approach 5: Percentile-Based Summary Views | *Percentile Analysis* |
| Approach 6: Histogram / Distribution View | *Executive Summary*, top chart |
| Approach 7: Box Plots | *Box Plot* |
| Approach 8: Table with Conditional Formatting | *Item Details* (right-click a bar on any per-item chart and choose *Drill through*) |
| Approach 9: Dual-Axis or Small Multiples for Temporal Trends | *Trends Over Time* |

## The `suggested-dashboard` report

The four pages from the post's *Suggested Dashboard Structure* section:

1. **Executive Summary**: cards for item count, median and P95 item total time, outlier count and outlier percentage; the distribution histogram; median item total time by week.
2. **State-by-State Analysis**: slicers for start date, category and priority; median and P95 per workflow state; median per state for normal items and for outlier items side by side.
3. **Outlier Investigation**: a formatted table of the outlier items; outlier counts by category, by priority and by week.
4. **Detailed Drill-Through**: a table of all items, a chart and a step list for the selected item, and *All Items* / *Outliers Only* buttons driven by bookmarks.

## Using it with your own data

Replace the CSV with a file that has the same six columns, or point the `WorkflowProcessingTimes` query at your own source and rename columns to match. Nothing in the measures is specific to workflows: wherever you see "processing time", read "the value you are charting".
