# QuickSight Calculated Field Tester

A lightweight, browser-based tool to write, test, and debug [AWS QuickSight](https://aws.amazon.com/quicksight/) calculated field formulas against sample data — no AWS account required.

## Overview

When building QuickSight dashboards, iterating on calculated field expressions can be slow because you need to publish and refresh your analysis each time. This tool lets you prototype formulas locally in your browser with an interactive spreadsheet editor and instant formula evaluation.

**Key highlights:**

- 🚀 **Zero setup** — open `index.html` in any modern browser
- 📊 **Editable data table** — add, remove, and rename columns and rows on the fly
- ✍️ **Formula editor** — write QuickSight expressions with `{column}` references
- ⚡ **Instant evaluation** — run formulas row-by-row with `Ctrl+Enter` / `Cmd+Enter`
- 🔧 **No dependencies** — single self-contained HTML file, pure vanilla JavaScript

## Getting Started

1. Clone or download this repository:
   ```bash
   git clone https://github.com/nicosandller/quicksight_calculated_field_tester.git
   ```
2. Open `index.html` in your browser.
3. Write a formula in the left panel, edit sample data in the right panel, and click **▶ Run Formula** (or press `Ctrl+Enter`).
4. Results appear in the output panel at the bottom.

## User Interface

The application has a three-panel layout:

| Panel | Description |
|-------|-------------|
| **Formula Editor** (left) | Text area where you write QuickSight calculated field expressions |
| **Data Table** (right) | Interactive spreadsheet with editable cells, column headers, and add/remove controls |
| **Output** (bottom) | Displays the evaluated result for each row after running the formula |

### Default Sample Data

The tool comes pre-loaded with sample data to get started quickly:

| timestamp | is_surplus | day_only |
|---|---|---|
| 2024-01-15 06:00:00 | 0 | 2024-01-15 |
| 2024-01-15 08:00:00 | 1 | 2024-01-15 |
| 2024-01-15 10:00:00 | 1 | 2024-01-15 |
| 2024-01-15 13:00:00 | 1 | 2024-01-15 |
| 2024-01-15 15:00:00 | 0 | 2024-01-15 |

You can freely modify this data or replace it entirely to match your use case.

## Supported Functions

### Conditional

| Function | Description |
|----------|-------------|
| `ifelse(condition, then, else)` | Conditional expression. Supports chained conditions: `ifelse(cond1, val1, cond2, val2, default)` |

### Date & Time

| Function | Description |
|----------|-------------|
| `truncDate(unit, date)` | Truncate a date to the specified unit (`'DD'`, `'HH'`, `'MI'`, `'MM'`, `'YYYY'`) |
| `addDateTime(amount, unit, date)` | Add a time interval to a date (`'MI'`, `'HH'`, `'DD'`, `'MM'`, `'YYYY'`) |
| `dateDiff(date1, date2, unit)` | Difference between two dates in the given unit (`'DD'`, `'HH'`, `'MI'`, `'SS'`) |
| `extract(unit, date)` | Extract a component from a date (`'HH'`, `'MI'`, `'DD'`, `'MM'`, `'YYYY'`, `'SS'`) |

### Aggregation

| Function | Description |
|----------|-------------|
| `min(expr)` | Minimum value across all rows |
| `max(expr)` | Maximum value across all rows |
| `minOver(expr, [partition_cols])` | Minimum within a partition |
| `maxOver(expr, [partition_cols])` | Maximum within a partition |

### Type Conversion

| Function | Description |
|----------|-------------|
| `toString(expr)` | Convert to string |
| `parseInt(expr)` / `parseInteger(expr)` | Convert to integer |
| `parseDecimal(expr)` / `parseFloat(expr)` | Convert to decimal |

### Math

| Function | Description |
|----------|-------------|
| `abs(expr)` | Absolute value |
| `ceil(expr)` | Round up to nearest integer |
| `floor(expr)` | Round down to nearest integer |
| `round(expr)` | Round to nearest integer |

### Null Checks

| Function | Description |
|----------|-------------|
| `isNull(expr)` | Returns `true` if the value is null |
| `isNotNull(expr)` | Returns `true` if the value is not null |

## Operators

| Category | Operators |
|----------|-----------|
| Arithmetic | `+`, `-`, `*`, `/` (`+` also concatenates strings) |
| Comparison | `<`, `>`, `<=`, `>=`, `=`, `!=` |
| Logical | `AND`, `OR` |
| Grouping | `(` `)` |

## Syntax Reference

- **Column references**: `{columnName}`
- **Strings**: single-quoted — `'hello'`
- **Numbers**: `42`, `3.14`
- **Dates**: ISO format in cells — `2024-01-15` or `2024-01-15 08:30:00`
- **Booleans**: `true`, `false`
- **Null**: `NULL`

## Formula Examples

**Categorise by hour of day:**
```
ifelse(
  extract('HH', {timestamp}) >= 8,
  'morning',
  'night'
)
```

**Flag surplus rows:**
```
ifelse({is_surplus} = 1, 'surplus', 'normal')
```

**Add one day to a date:**
```
addDateTime(1, 'DD', {timestamp})
```

**Minutes between two dates:**
```
dateDiff({day_only}, {timestamp}, 'MI')
```

**Aggregate — maximum timestamp across all rows:**
```
max({timestamp})
```

**Partitioned aggregation:**
```
maxOver({timestamp}, [{is_surplus}])
```

## How It Works

Under the hood, the tool implements a **recursive-descent parser** and **tree-walking evaluator** entirely in vanilla JavaScript:

1. **Tokeniser** — splits the formula string into tokens (strings, numbers, column references, operators, function names, etc.)
2. **Parser** — builds an Abstract Syntax Tree (AST) from the token stream using recursive descent with operator-precedence parsing
3. **Evaluator** — walks the AST for each data row, resolving column references from the table, calling built-in functions, and computing the final result

All processing happens client-side in the browser — no data ever leaves your machine.

## Tech Stack

- **HTML5 / CSS3 / JavaScript (ES6+)** — single-file application, no build step
- **CSS Grid** — three-panel responsive layout
- **CSS Custom Properties** — dark-mode colour theme
- **Google Fonts** — JetBrains Mono (code) and Syne (headings)

## License

This project is licensed under the [MIT License](LICENSE).

© 2026 Nicolas Elias Sandller
