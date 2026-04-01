---
description: 显示巴基斯坦标准时间（PKT，UTC+5）的当前时间
---

# 时间命令

显示巴基斯坦标准时间（PKT，UTC+5）的当前日期和时间。

## 说明

1. 运行以下 bash 命令获取 PKT 当前时间：
   ```
   TZ='Asia/Karachi' date '+%Y-%m-%d %H:%M:%S %Z'
   ```

2. 以以下格式向用户显示结果：
   ```
   Current Time in Pakistan (PKT): YYYY-MM-DD HH:MM:SS PKT
   ```

## 要求

- 始终使用 `Asia/Karachi` 时区（UTC+5）
- 使用 24 小时制
- 包含日期以及时间
- 保持输出简洁