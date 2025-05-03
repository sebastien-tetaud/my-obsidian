
[[Sentinel 2  - Toward L2A processor]][[Deep Learning


## Planning

- Efficientnet-b2 with save strategy: sam , min + remove Dataset outlier

|                     | Model           | save strat | Norma     | Scheduler |     |
| ------------------- | --------------- | ---------- | --------- | --------- | --- |
| 2025-04-26_22-00-04 | resnet 34       | loss       | percentil | No        |     |
| 2025-04-27_11-19-42 | resnet 34       | loss       | percentil | Plateau   |     |
| 2025-04-29_16-34-20 | Efficientnet-b2 | loss       | percentil | Plateau   |     |
| 2025-04-30_10-50-51 | Efficientnet-b2 | sam        | percentil | Plateau   |     |
| 2025-05-01_09-37-23 | Efficientnet-b2 | sam        | basic     | Plateau   |     |
