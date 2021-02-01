---
title: openpyxl
date: 2021-02-01 13:16:50
tags: python
categories:
mp3:
cover:
---
合并单元格数据获取
```python
def get_cell_value(ws, row, column):
    for r in ws.merged_cells.ranges:
        if row >= r.min_row and row <= r.max_row and column >= r.min_col and column <= r.max_col:
            return ws.cell(r.min_row, r.min_col).value
    return ws.cell(row, column).value
```
