# Excel Data Profiler 使用指南 / User Guide

## 背景 / Background

`excel-data-profiler` 用于根据脱敏后的 Excel/CSV 样例生成可复用的数据质量检测和探索分析 Python 脚本。它适合你只有表头和 1-2 行 sample 数据、但希望在完整数据上运行自动质检、查重、空值、枚举值、异常值、KPI 汇总、相关性和图表分析的场景。

`excel-data-profiler` generates reusable Python scripts for data quality checks and exploratory analysis from a sanitized Excel/CSV sample. It is designed for cases where the sample contains only headers and 1-2 rows, while the generated script will run against the full dataset locally.

## 输入要求 / Expected Input

- Excel 或 CSV 文件。
- 文件应包含真实表头和 1-2 行代表性 sample 数据。
- 上传或提供给 AI 前，请先脱敏处理。
- 建议脱敏字段包括姓名、手机号、证件号、邮箱、详细地址、客户名称、员工名称、订单明细中可能识别个人的信息等。

- Excel or CSV file.
- The file should include real headers and 1-2 representative sample rows.
- Sanitize the data before sharing it with AI.
- Recommended sensitive fields to mask include names, phone numbers, ID numbers, emails, detailed addresses, customer names, employee names, and any order detail that can identify a person.

## 输出内容 / Expected Output

- 一份可运行的 Python 脚本。
- 一份 `data_quality_report.xlsx` 数据质量报告。
- 可选的图表 PNG 文件。
- 可选：把图表嵌入 Excel 报告中。

- A runnable Python script.
- A `data_quality_report.xlsx` data quality report.
- Optional chart PNG files.
- Optional embedded charts inside the Excel report.

## 使用方式 / How To Use

在对话中显式调用 skill：

```text
Use $excel-data-profiler 分析这个脱敏 Excel 样例，并生成完整的数据质量检测和探索分析 Python 代码。
```

The skill should first infer candidate fields and ask you to confirm:

```text
请回复：是 / 否 / 其他: ...
```

确认后，它会生成最终 Python 脚本。

## 你需要准备什么 / What You Need

- 完整数据文件保存在本地机器上。
- Python 环境中建议安装：`pandas`, `numpy`, `openpyxl`。
- 如果要生成图表，运行脚本的同一个 Python 环境还需要安装：`matplotlib`, `seaborn`。

- Keep the full dataset on your local machine.
- Recommended Python packages: `pandas`, `numpy`, `openpyxl`.
- For charts, install `matplotlib` and `seaborn` in the same Python environment used to run the script.

如果你使用 Anaconda，请在对应环境里运行或安装：

```bash
conda activate your_env
conda install pandas numpy openpyxl matplotlib seaborn
```

或者：

```bash
pip install pandas numpy openpyxl matplotlib seaborn
```

## 可手动修改的点 / Editable Settings

生成的 Python 脚本顶部会有中文注释配置区，通常可以修改：

- `FILE_PATH`: 完整数据文件路径。
- `SHEET_NAME`: Excel 工作表名称或序号。
- `OUTPUT_DIR`: 输出目录。
- `OUTPUT_REPORT`: 质检报告路径。
- `PRIMARY_KEY`: 主键或复合主键，用于查重。
- `UNIQUE_FIELDS`: 其他不应重复的字段。
- `DATE_COLUMNS`: 日期字段。
- `NUMERIC_COLUMNS`: 数值字段。
- `CATEGORICAL_COLUMNS`: 分类维度字段。
- `KPI_CONFIG`: KPI 字段与聚合方式，如 `sum`, `mean`, `count`, `median`, `min`, `max`, `nunique`。
- `TOP_N`: 每个分类字段输出前 N 个枚举值。
- `EMBED_CHARTS_IN_EXCEL`: 是否把图表嵌入 Excel。

The generated script will include a Chinese-commented configuration section. You can edit file paths, sheet names, primary keys, unique fields, date fields, numeric fields, categorical fields, KPI aggregation methods, top-N settings, and chart embedding behavior.

## 注意事项 / Notes

- 样例数据只有 1-2 行时，AI 对主键、KPI 和分类字段的判断只是推断，必须经过你确认。
- 生成的脚本默认把图表保存为单独 PNG，并在 Excel 报告中列出路径。这样更稳定、文件更轻。
- 如果选择嵌入图表，Excel 文件会变大，而且大量图表可能需要手动调整布局。
- `matplotlib`/`seaborn` 是否可用取决于你运行脚本的 Python 环境，不取决于电脑上是否存在另一个已安装这些包的环境。
- 请不要把未脱敏的人名、手机号、证件号、邮箱、详细地址等敏感数据上传给 AI。

- With only 1-2 sample rows, AI-inferred keys, KPIs, and dimensions are assumptions and should be confirmed.
- Charts are saved as separate PNG files by default and listed in the Excel report. This is more robust and keeps the workbook smaller.
- Embedded charts can make the workbook larger and may require layout tuning.
- `matplotlib`/`seaborn` availability depends on the Python environment used to run the script, not on whether another environment on the machine has them installed.
- Do not upload unsanitized personal or sensitive data to AI.
