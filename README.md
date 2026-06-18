# Excel Data Profiler 使用指南 / User Guide

## 背景 / Background

`excel-data-profiler` 旨在通过 AI 辅助解决大型 Excel/CSV 文件的初期数据探索与质量检测需求。在传统的 AI 数据分析流程中，直接将完整数据上传给大模型对话框会面临两大痛点：
1. **数据隐私与泄露风险**：真实的商业或个人敏感数据通常受规章限制，绝不能轻易上传至线上的 AI 接口。
2. **性能与成本瓶颈**：数据量过大不仅会导致上传和分析耗时极长，还会消耗海量的 Token，极易触发上下文长度限制。

为了解决这些问题，本工具采用了**“AI 分析数据框架 -> 生成定制化脚本 -> 纯本地运行”**的模式。你只需提供仅包含表头和 1-2 行脱敏数据的轻量级样例，AI 就能帮你推断数据结构并生成一份专属的 Python 分析代码。你在本地机器上运行这段代码，即可安全、高效地完成自动质检、查重、空值、枚举值、异常值、KPI 汇总、相关性和图表分析。这从根本上杜绝了数据泄露风险，并彻底打破了 Token 消耗与数据规模的限制。

`excel-data-profiler` is designed to assist with the initial data exploration and quality profiling of large Excel/CSV files. Uploading full datasets directly to AI chat interfaces typically presents two major issues:
1. **Data Privacy and Leakage Risks**: Sensitive business or personal data is strictly regulated and cannot be exposed to online LLM APIs.
2. **Performance and Cost Limits**: Massive datasets take too long to upload/process and consume excessive amounts of tokens, often hitting context length limits.

To solve this, this tool adopts an **"AI infers schema -> Generates tailored script -> Runs locally"** approach. By uploading a lightweight, sanitized sample containing only headers and 1-2 rows, the AI generates a customized Python script for your exact dataset. You then run this script on your local machine to perform automated quality checks, deduplication, missing value analysis, outlier detection, KPI aggregation, correlation, and visualizations. This guarantees zero data leakage and completely bypasses token/size limitations.

## 操作流程 / Step-by-Step Workflow

**步骤 1：本地提取样例 (Extract Sample)**  
打开你需要分析的原始大型 Excel/CSV 文件，仅复制**表头（Headers）和 1-2 行具体数据记录**，将它们粘贴并保存为一个全新的 Excel 文件。  
*Open your large source Excel/CSV file, copy only the headers and 1-2 rows of data, and save them into a brand-new Excel file.*

**步骤 2：手动脱敏 (Sanitize Data)**  
根据你的业务经验，手动将这个新文件中的敏感信息替换或打码。例如：将真实人名改为“张XX”，修改真实手机号、清除真实身份证号、邮箱或详细地址等可能识别个人的信息。  
*Based on your business context, manually mask or replace sensitive information in this new file (e.g., change real names to "John Doe", mask phone numbers, clear ID numbers, emails, or exact addresses).*

**步骤 3：上传并调用 Skill (Upload & Invoke)**  
将这份脱敏后的轻量级样例 Excel 上传到 AI 对话框，并发送以下指令：  
*Upload this sanitized, lightweight sample file to the AI chat and send the following prompt:*
```
Use $excel-data-profiler 分析这个脱敏 Excel 样例，并生成完整的数据质量检测和探索分析 Python 代码。
```

**步骤 4：确认配置 (Confirm Settings)**
AI 将基于样例推断候选字段并向你确认：
The AI will infer the schema and ask you to confirm the configuration: 请回复：是 / 否 / 其他: ...
确认后，它会生成最终的 Python 脚本。

**步骤 5：本地运行 (Run Locally)**
复制生成的 Python 脚本。在代码顶部的配置区将文件路径修改为你本地原始大文件的真实路径，并在本地 Python 环境中运行它。
Copy the generated Python script, point the file path variable to your actual large dataset on your local machine, and run it.


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

运行生成的 Python 脚本后，你将在本地获得：
- 一份 data_quality_report.xlsx 数据质量报告。
- 可选的图表 PNG 文件（默认输出在单独文件夹）。
- 可选：把图表嵌入 Excel 报告中。
After running the generated Python script locally, you will get:
- A data_quality_report.xlsx data quality report.
- Optional chart PNG files (saved to a folder by default).
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
