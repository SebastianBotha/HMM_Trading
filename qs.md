# QuantStats Reference Guide

## Overview
This document provides detailed information about the QuantStats Python package, the compatibility issues encountered, and solutions implemented to make it work with modern Python environments.

## QuantStats Package Description
QuantStats is a Python library for portfolio performance analytics and risk metrics. It provides tools to:
- Calculate performance metrics (Sharpe ratio, Sortino, etc.)
- Generate visual tear sheets
- Compare strategies to benchmarks
- Analyze drawdowns and volatility

GitHub: https://github.com/ranaroussi/quantstats

## Patch Scripts for Compatibility Issues

### Patch 1: Fixing Pandas Resampling Error

```python
# fix_quantstats.py
import os

# Path to the problematic file
file_path = '/opt/miniconda3/envs/quantstats-env/lib/python3.9/site-packages/quantstats/_plotting/core.py'

# Read the content
with open(file_path, 'r') as file:
    content = file.read()

# Fix the problematic line
fixed_content = content.replace(
    'returns = returns.last() if compound is True else returns.sum(axis=0)',
    'returns = returns.last() if compound is True else returns.sum()'
)

# Write the fixed content back
with open(file_path, 'w') as file:
    file.write(fixed_content)

print("QuantStats patched successfully!")
```

**Issue fixed:** This patch addresses the `UnsupportedFunctionCall` error caused by incompatibility between newer pandas versions and how QuantStats calls the resampling methods. The error occurs because pandas deprecated passing additional kwargs to `DatetimeIndexResampler.sum`.

### Patch 2: Fixing Pandas Inplace Operation Warnings

```python
# fix_quantstats_inplace.py
import os

# Path to the problematic file
file_path = '/opt/miniconda3/envs/quantstats-env/lib/python3.9/site-packages/quantstats/_plotting/core.py'

# Read the content
with open(file_path, 'r') as file:
    content = file.read()

# Fix the problematic inplace operations
replacements = [
    ('port["Weekly"].ffill(inplace=True)', 'port["Weekly"] = port["Weekly"].ffill()'),
    ('port["Monthly"].ffill(inplace=True)', 'port["Monthly"] = port["Monthly"].ffill()'),
    ('port["Quarterly"].ffill(inplace=True)', 'port["Quarterly"] = port["Quarterly"].ffill()'),
    ('port["Yearly"].ffill(inplace=True)', 'port["Yearly"] = port["Yearly"].ffill()')
]

# Apply all the replacements
fixed_content = content
for old, new in replacements:
    fixed_content = fixed_content.replace(old, new)

# Write the fixed content back
with open(file_path, 'w') as file:
    file.write(fixed_content)

print("QuantStats inplace operations patched successfully!")
```

**Issue fixed:** This patch addresses the `FutureWarning` about chained assignment with inplace methods that will be deprecated in pandas 3.0. The fix replaces problematic inplace operations with the recommended approach.

## Backup Plans for QuantStats Compatibility

### Plan A: Create a Dedicated Conda Environment with Compatible Versions

```bash
# Create a new environment with Python 3.7
conda create -n quantstats-stable python=3.7
conda activate quantstats-stable

# Install specific versions known to work together
pip install pandas==0.25.3
pip install numpy==1.17.2
pip install matplotlib==3.1.3
pip install seaborn==0.9.0
pip install scipy==1.3.1
pip install tabulate==0.8.7
pip install yfinance==0.1.54
pip install plotly==4.5.0
pip install quantstats==0.0.59
```

This environment recreates the ecosystem from when QuantStats was actively maintained, ensuring maximum compatibility.

### Plan B: Use a Community Fork with Fixes

Search GitHub for maintained forks of QuantStats that include compatibility fixes for newer Python and pandas versions. Install directly from the fork:

```bash
pip install git+https://github.com/[username]/quantstats.git
```

### Plan C: Create Your Own Fixed Fork

1. Fork the QuantStats repository on GitHub
2. Apply the patches shown above to your fork
3. Install your modified version:

```bash
pip install git+https://github.com/[your-username]/quantstats.git
```

## Usage Reference

### Basic Usage Example

```python
import quantstats as qs

# Extend pandas functionality
qs.extend_pandas()

# Download returns for a ticker
stock = qs.utils.download_returns('AAPL', period='1y')

# Calculate metrics
sharpe = stock.sharpe()
print(f"Sharpe Ratio: {sharpe:.4f}")

# Create a snapshot plot
qs.plots.snapshot(stock, title='Apple Performance', show=True)

# Generate a full HTML tearsheet
qs.reports.html(stock, "SPY", output="tearsheet.html")
```

### Common Functions Reference

```python
# Performance metrics
qs.stats.sharpe(returns)
qs.stats.sortino(returns)
qs.stats.max_drawdown(returns)
qs.stats.cagr(returns)
qs.stats.volatility(returns)

# With extended pandas
returns.sharpe()
returns.sortino()
returns.max_drawdown()

# Plotting
qs.plots.monthly_returns(returns)
qs.plots.yearly_returns(returns)
qs.plots.drawdown(returns)
qs.plots.drawdowns_periods(returns)
qs.plots.rolling_volatility(returns)
qs.plots.rolling_sharpe(returns)

# Reports
qs.reports.html(returns, benchmark="SPY", output="report.html")
qs.reports.full(returns)  # Jupyter notebook display
qs.reports.metrics(returns, mode='full')  # Just the metrics table
```

## Troubleshooting Additional Issues

1. **Font errors**: Install system fonts or modify matplotlib configuration
   ```bash
   # For Ubuntu/Debian
   sudo apt-get install msttcorefonts -qq
   
   # For macOS
   brew install --cask font-microsoft-office
   ```

2. **YFinance data fetching issues**: Update yfinance or use alternative data
   ```bash
   pip install --upgrade yfinance
   ```

3. **Jupyter integration**: Make sure ipykernel is installed
   ```bash
   pip install ipykernel
   python -m ipykernel install --user --name=quantstats-env
   ```

## Version Compatibility Cheat Sheet

| QuantStats Version | Compatible Pandas | Compatible NumPy | Python Version |
|:-------------------|:------------------|:-----------------|:---------------|
| 0.0.59             | 0.25.3            | 1.17.2           | 3.7            |
| 0.0.60-0.0.64      | ~1.0.0            | ~1.19.0          | 3.7-3.8        |
| Latest             | Patched Required  | Recent           | 3.7-3.9        |

---

This reference guide was created on April 17, 2025. Package dependencies and compatibility may change over time.

