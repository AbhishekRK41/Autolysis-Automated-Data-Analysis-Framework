# Autolysis — Automated Data Analysis Framework

Point Autolysis at any CSV and it profiles the data, runs outlier detection and clustering, generates visualizations, and asks an LLM to narrate the findings — producing a complete `README.md` report with embedded charts, with zero manual analysis.

## What it does

1. **Profiles the dataset** — summary statistics and a missing-value breakdown for every column.
2. **Detects outliers** using z-scores on numeric columns and passes them to the LLM as part of its prompt.
3. **Clusters rows** with k-means (3 clusters) over the numeric columns. The cluster assignments and centres are computed in the code but are not yet written into the report.
4. **Visualizes** the data — a correlation heatmap plus a distribution plot per numeric column.
5. **Narrates the results** by sending the summary stats, missing-value report, and outliers to an LLM (via a hosted OpenAI-compatible proxy) and writing its analysis into the report.
6. **Writes it all to `README.md`** in the working directory, with the PNG charts embedded inline.

A Random Forest feature-importance function (`feature_importance()`) is also implemented but is not yet wired into the report.

## Architecture

```
autolysis.py
├── detect_outliers()        # z-score based outlier detection
├── perform_clustering()     # k-means over numeric columns (computed, not yet reported)
├── feature_importance()     # RandomForestRegressor feature ranking (implemented, not yet called)
├── create_visualizations()  # correlation heatmap + per-column distributions
├── query_llm()              # sends dataset summary to an LLM for narrative insights
├── generate_readme()        # assembles everything into README.md
└── main()                   # CLI entry point — reads argv[1] as the CSV path
```

The script declares its dependencies in a PEP 723 header, so `uv run autolysis.py <dataset.csv>` installs them automatically. Without `uv`, install them with pip as shown below.

Sample runs are included for three datasets — `goodreads/`, `happiness/`, and `media/` — each with its own generated report and charts, so you can see example output without running anything.

## Setup

**Requirements:** Python 3.11+

```bash
git clone https://github.com/AbhishekRK41/Autolysis-Automated-Data-Analysis-Framework.git
cd Autolysis-Automated-Data-Analysis-Framework
pip install pandas matplotlib seaborn numpy requests scikit-learn scipy
```

The LLM narrative step calls a hosted proxy and expects an API token:

```bash
export API_TOKEN="your-api-token-here"
```

*(Without a token, every other step — stats, outliers, clustering, charts — still runs; only the narrative "Insights" section will note it couldn't reach the LLM.)*

## Usage

```bash
python autolysis.py <path-to-dataset.csv>
# or, with uv:
uv run autolysis.py <path-to-dataset.csv>
```

This produces, in the current directory:
- `README.md` — the full analysis report
- `correlation_heatmap.png`
- `<column>_distribution.png` for every numeric column

**Example:**
```bash
cd goodreads
python ../autolysis.py goodreads.csv
```

## Example output

See [`goodreads/`](./goodreads), [`happiness/`](./happiness), and [`media/`](./media) for full generated reports on real datasets, including the correlation heatmaps and per-column distribution charts Autolysis produces automatically.

## License

MIT — see [LICENSE](./LICENSE).
