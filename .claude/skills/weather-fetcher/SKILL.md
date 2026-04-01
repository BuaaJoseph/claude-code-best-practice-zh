---
name: weather-fetcher
description: 从Open-Meteo API获取阿联酋迪拜当前天气温度数据的说明
user-invocable: false
---

# 天气获取技能

此技能提供获取当前天气数据的说明。

## 任务

获取阿联酋迪拜的当前温度（摄氏度或华氏度）。

## 说明

1. **获取天气数据**：使用WebFetch工具从Open-Meteo API获取迪拜的当前天气数据。

   **摄氏度**：
   - URL: `https://api.open-meteo.com/v1/forecast?latitude=25.2048&longitude=55.2708&current=temperature_2m&temperature_unit=celsius`

   **华氏度**：
   - URL: `https://api.open-meteo.com/v1/forecast?latitude=25.2048&longitude=55.2708&current=temperature_2m&temperature_unit=fahrenheit`

2. **提取温度**：从JSON响应中提取当前温度：
   - 字段：`current.temperature_2m`
   - 单位标签在：`current_units.temperature_2m`

3. **返回结果**：清楚返回温度值和单位。

## 预期输出

完成此技能的说明后：
```
Current Dubai Temperature: [X]°[C/F]
Unit: [Celsius/Fahrenheit]
```

## 注意事项

- 只获取温度，不要进行任何转换或写入任何文件
- Open-Meteo是免费的，不需要API密钥，使用坐标查询更可靠
- 迪拜坐标：纬度25.2048，经度55.2708
- 清楚返回数值温度值和单位
- 根据调用方的请求支持摄温度和华氏度