---
name: video-intel-montage
description: 在抖音、B站、Telegram和本地视频源执行目标导向的视频浏览与处理工作流。用于需要按任务类型完成 (1) 新闻资讯收集与事实整理、(2) 用户偏好画面截取与归档、(3) 视频片段筛选与剪辑拼接 的场景。
---

# video-intel-montage

按以下顺序执行，确保每一步都有可追溯输出。

## 1. 明确任务模式

先判断当前任务属于以下哪一类（可多选）：
- `news_collection`：收集并整理新闻相关视频信息
- `preference_capture`：截取用户偏好图像并结构化保存
- `video_editing`：筛选素材并生成可直接剪辑的片段清单

若需求不清晰，先补齐：主题、时间范围、平台优先级、输出格式、语言范围、敏感词限制。

## 2. 统一采集维度

对每条候选视频统一记录以下字段：
- `platform`（douyin/bilibili/telegram/local）
- `source_locator`（链接、频道名+消息ID、本地路径）
- `title_or_caption`
- `author_or_channel`
- `publish_time`
- `duration`
- `core_tags`（3~8个关键词）
- `summary_1to2_sentences`
- `quality_score`（1~5）
- `relevance_score`（1~5）

优先复用 `references/task-templates.md` 的表头与示例。

## 3. 按模式执行

### A) news_collection

1. 围绕主题列出检索词（事件名、人物、机构、别名、时间词）。
2. 在四类来源各采集至少一轮候选，避免单平台偏差。
3. 排除明显重复、营销搬运、时间过旧素材。
4. 输出 `新闻线索摘要`：
   - 时间线（最早 -> 最新）
   - 主要叙事分歧点
   - 高置信来源与低置信来源标记
5. 给出下一轮补采建议（缺失视角/地区/当事方）。

### B) preference_capture

1. 先提炼用户偏好维度：人物、镜头、构图、色调、场景、节奏、字幕风格。
2. 浏览素材时仅保留“高匹配帧”，并为每张图附标签：
   - `subject`、`style`、`color`、`mood`、`shot_type`
3. 使用统一命名：`YYYYMMDD_platform_topic_seq.jpg/png`
4. 同步写入截图索引表，记录来源时间点与用途建议（开场/转场/结尾）。
5. 明确版权与使用限制；无法确认授权时标记为 `pending-rights`。

### C) video_editing

1. 为剪辑目标定义时长与结构（如 60s：5s钩子+40s主叙事+15s收束）。
2. 从候选视频中抽取可用片段，记录 in/out timecode。
3. 对每个片段标注用途：`hook`、`evidence`、`emotion`、`transition`、`ending`。
4. 输出粗剪单（EDL风格文本即可）：素材、时码、顺序、转场建议、字幕要点。
5. 检查节奏与信息密度，删除冗余重复镜头。

## 4. 质量门槛

提交前逐项检查：
- 覆盖多个平台，不单点依赖
- 每条结论可回溯到具体视频来源
- 时间信息完整且时区一致
- 区分事实、推测、主观判断
- 涉及人物与敏感话题时保持中性表述

## 5. 产出模板

根据任务类型套用 `references/task-templates.md`：
- 新闻：`News Brief + Source Table`
- 偏好截图：`Preference Frames Index`
- 剪辑：`Clip Plan + EDL Text`

## 6. 失败回退

当平台不可访问、结果过少或来源冲突时：
1. 明确报告阻塞原因；
2. 优先切换到其他平台或本地素材继续；
3. 输出“当前可交付最小结果 + 后续补采清单”。
