---
name: excel-data-profiler
description: Generate reusable, Chinese-commented Python code for Excel/CSV data quality checks, exploratory data analysis, KPI breakdowns, correlation checks, and common visualizations from a sanitized sample containing headers and 1-2 example rows. Use when the user uploads or describes a spreadsheet sample and wants AI to infer field types, confirm candidate keys/KPIs with the user, generate Chinese-labeled data quality reports, or produce executable pandas/matplotlib/seaborn/openpyxl analysis code for the full dataset.
---

# Excel Data Profiler

## Core Behavior

Treat the uploaded workbook as a schema and sample-value signal, not as the full dataset. Produce Python code that the user can run against the real Excel/CSV file locally after replacing the input path and, when needed, confirming ambiguous business assumptions.

Prefer generating a complete, runnable notebook-style Python script with clear sections. Use `pandas`, `numpy`, `openpyxl`, `matplotlib`, and `seaborn` by default. Treat `matplotlib` and `seaborn` as optional plotting dependencies: generated scripts should still produce the Excel data quality report when plotting packages are missing, and should write an environment note explaining skipped charts.

Use Chinese comments and Chinese user-facing labels in generated Python code by default. The code should be understandable to a Chinese-speaking analyst who opens the `.py` file and wants to know which parts are editable.

Never infer sensitive facts from sample rows. Use sample rows only to infer likely dtypes, date formats, categorical fields, numeric fields, identifier fields, and KPI candidates.

## Workflow

1. Inspect the provided headers, sheet names, and 1-2 sample rows.
2. Infer a data dictionary with columns, likely dtype, semantic role, and notes.
3. Before generating final code, present a concise confirmation block unless the user explicitly asks to skip confirmation:
   - candidate primary key or composite key
   - fields that should be unique but may not be primary keys
   - KPI columns and aggregation method: sum, mean, count, median, min, max, distinct count
   - date or time fields for trend analysis
   - categorical dimension fields
   - chart output preference when relevant: separate image files by default, optional embedded images in Excel when requested
4. Ask the user to reply with `是`, `否`, or `其他: ...`. If the user says `是`, generate code with the inferred configuration. If the user says `否` or provides `其他`, adjust the configuration before generating code.
5. If the user requests immediate code, do not block; include editable variables at the top and mark assumptions clearly in Chinese comments.
6. Generate executable code organized into reusable functions plus a short configuration block.
7. Include Chinese comments that explain where the user should edit file paths, output paths, key fields, KPI fields, aggregation methods, chart settings, report settings, and function parameters.

## Default Sheet Handling

Generated scripts should default to `SHEET_NAME = 0` so they automatically read the first worksheet. Do not hardcode the sample sheet name, such as `Sheet1`, unless the user explicitly requests a specific sheet. Add a Chinese comment explaining that users can change `SHEET_NAME` to a real sheet name when needed.

## Output Requirements

Always include these sections when generating code:

- Imports and display options
- Chinese-commented configuration block: `FILE_PATH`, `SHEET_NAME`, `OUTPUT_DIR`, `OUTPUT_REPORT`, `PRIMARY_KEY`, `UNIQUE_FIELDS`, `KPI_CONFIG`, optional date fields, chart options
- Robust data loading for `.xlsx`, `.xls`, `.xlsm`, and `.csv`
- Field type normalization based on inferred schema, with safe coercion for numeric and date columns
- Overall profiling similar to `df.info()`, `df.describe()`, row/column counts, dtype counts, memory usage, and sample records
- Completeness checks: null counts and null percentages per field
- Uniqueness checks: primary key/composite key duplicate detection and unique ratio per field
- Validity checks: categorical cardinality, top 10 values per text/category field, and high-cardinality warnings
- Numeric outlier and distribution checks: descriptive statistics, IQR/z-score flags, and histograms when plotting packages are available
- KPI analysis: configurable aggregation by categorical fields and optional time fields
- Common plotting helpers: histogram, bar chart, scatter plot, box plot, time trend, missingness plot
- Correlation helpers: pairwise numeric correlation table and heatmap
- Export helpers: write quality summaries and top-value tables to an Excel report with Chinese sheet names and Chinese column labels

When the user asks for a shorter answer, provide a compact version but preserve editable configuration, confirmation assumptions, and the most important validation functions.

## Report Language Requirements

Generated `data_quality_report.xlsx` should use Chinese sheet names and Chinese field labels whenever possible. Examples:

- `overview` -> `整体概览`
- `dtype_counts` -> `字段类型统计`
- `column_profile` -> `字段画像`
- `missingness` -> `完整性检查`
- `uniqueness` -> `唯一性检查`
- `categorical_summary` -> `类别字段概览`
- `numeric_summary` -> `数值字段概览`
- `outliers_iqr` -> `异常值检查`
- `business_rules` -> `业务规则检查`
- `pii_warning` -> `敏感字段提示`
- `correlation_pairs` -> `相关性明细`
- `conversion_warnings` -> `类型转换提示`
- `environment_notes` -> `运行环境说明`
- `plot_files` -> `图表文件清单`

Translate common output columns, for example `column` -> `字段名`, `dtype` -> `字段类型`, `null_count` -> `空值数量`, `null_pct` -> `空值比例`, `unique_count` -> `唯一值数量`, `record_count` -> `记录数`, `status` -> `检查结果`, `warning` -> `提示`.

## Chart Output Requirements

By default, save charts as separate `.png` files and include their paths in the Excel report sheet `图表文件清单`. This is simpler, robust, and keeps the report file smaller.

If the user asks to embed charts in Excel, generate code that uses `openpyxl.drawing.image.Image` to insert selected PNG files into dedicated chart sheets. Keep this optional because embedded images add complexity, increase file size, and may require layout tuning.

Explain that `matplotlib`/`seaborn` must be installed in the same Python environment used to run the script. If the user runs the script in an Anaconda environment where those packages are installed, plotting should work even if the global/system Python does not have them.
Generated plotting code must configure Chinese fonts before creating any chart. **CRITICAL RULE: If applying a Seaborn theme (e.g., `sns.set_theme(style="whitegrid")`), it MUST be called BEFORE configuring the Matplotlib Chinese fonts. Otherwise, the Seaborn theme will overwrite the font settings, causing Chinese characters to render as squares.** Prefer `Microsoft YaHei` and `Microsoft YaHei UI` on Windows, then fall back to `SimHei`, `Noto Sans CJK SC`, `Source Han Sans SC`, and `Arial Unicode MS`. Set `plt.rcParams["axes.unicode_minus"] = False` so negative signs render correctly. Explain that embedded Excel images use the generated PNGs, so Chinese font support must be fixed at matplotlib PNG generation time.

## Code Generation Reference

For detailed code patterns and recommended function inventory, read `references/code-generation.md` before writing the final Python code.

## Inference Guidance

Use these heuristics, and label them as assumptions in user-facing output:

- Columns named like `id`, `*_id`, `no`, `number`, `code`, `key`, `uuid`, `order_id`, `user_id`, or `customer_id` are candidate keys or dimensions.
- Columns named like `amount`, `revenue`, `sales`, `gmv`, `price`, `cost`, `quantity`, `qty`, `margin`, or `profit` are KPI candidates.
- Columns named like `date`, `time`, `created_at`, `updated_at`, `month`, or `day` are date/time candidates.
- Low-cardinality text fields are likely categorical dimensions. High-cardinality text fields may be names, descriptions, addresses, comments, or identifiers.
- Numeric fields with names containing `id`, `code`, `phone`, `zip`, `postal`, or `number` should often be treated as strings, not quantitative measures.

## Interaction Rules

Ask the user to confirm candidate keys, unique fields, KPI fields, KPI aggregation methods, date fields, and categorical dimensions before generating the final script, unless they explicitly request immediate code.

Use this concise format:

```text
我先基于样例做了如下推断，请确认后我再生成最终脚本：
1. 主键/复合主键：...
2. 唯一性校验字段：...
3. KPI字段与聚合方式：...
4. 日期字段：...
5. 分类维度字段：...
6. 图表输出：默认单独输出PNG，并在Excel报告中列出路径。

请回复：
- 是：按以上配置生成
- 否：我重新推断
- 其他：请写明要调整的字段或规则
```

If the user has not provided the actual file path, use `FILE_PATH = "your_full_data.xlsx"` and clearly mark it as editable.


