# Autolysis — Automated Data Analysis Framework

Point Autolysis at any CSV and it profiles the data, runs outlier detection, clustering, and feature-importance analysis, generates visualizations, and asks an LLM to narrate the findings — producing a complete `README.md` report with embedded charts, with zero manual analysis.

## What it does

1. **Profiles the dataset** — summary statistics and a missing-value breakdown for every column.
2. **Detects outliers** using z-scores on numeric columns.
3. **Clusters rows** with k-means (3 clusters) to surface natural groupings.
4. **Ranks feature importance** with a Random Forest regressor.
5. **Visualizes** the data — a correlation heatmap plus a distribution plot per numeric column.
6. **Narrates the results** by sending the summary stats, missing-value report, and outliers to an LLM (via a hosted OpenAI-compatible proxy) and writing its analysis into the report.
7. **Writes it all to `README.md`** in the working directory, with the PNG charts embedded inline.

## Architecture

```
autolysis.py
├── detect_outliers()        # z-score based outlier detection
├── perform_clustering()     # k-means over numeric columns
├── feature_importance()     # RandomForestRegressor feature ranking
├── create_visualizations()  # correlation heatmap + per-column distributions
├── query_llm()              # sends dataset summary to an LLM for narrative insights
├── generate_readme()        # assembles everything into README.md
└── main()                   # CLI entry point — reads argv[1] as the CSV path
```

The script is self-contained: it checks for its own dependencies at import time and pip-installs anything missing, so a bare `python autolysis.py` on a fresh machine will bootstrap itself.

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
