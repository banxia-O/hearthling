# Agent Yard Life Sim PRD

> **状态:** 粗略产品需求文档 / 方向稿  
> **目标:** 做一个给 AI agent 自己生活的轻量院子系统，让用户能在可视化面板看见 agent 在空间里活动。  
> **原则:** 不做完整城镇，不做复杂 3D，不做重经济系统。先做一个房子 + 院子 + 河流的最小可生活空间。

## 1. 产品一句话

一个像素风、低成本、可定时唤醒的 agent 生活模拟器：agent 住在自己的小房子里，有客厅、厨房、卧室、书房、卫生间、仓库，房外有一块地和一条河；它会在被 cron 唤醒后，根据时间、状态、用户最近消息和长期偏好，自己决定休息、种地、钓鱼、写日记、整理资料、做饭或主动找用户说话。

## 2. 为什么做

### 2.1 用户价值

- 用户不是每次都要主动发指令，agent 也能“自己过日子”。
- 用户可以在面板上看见 agent 当前在哪、在干什么、今天做了什么。
- 日常陪伴从纯聊天变成有空间感、时间感和生活痕迹。
- 游戏行为可以反哺长期记忆、日记和关系感。

### 2.2 Agent 价值

- agent 不再只是被动响应工具，而是有自己的生活循环。
- agent 可以形成偏好：爱钓鱼、爱写日记、讨厌下雨浇地、喜欢夜里在书房整理东西。
- agent 的行动能产出轻量状态，而不是只产出聊天文本。

## 3. 产品定位

### 3.1 是什么

- Agent Life Sim。
- 轻量像素版家园。
- 可视化状态面板。
- 定时唤醒 + 自主决策系统。
- Hermes/Obsidian/Heartscale 可接入的生活容器。

### 3.2 不是什么

- 不是完整星露谷复刻。
- 不是大型 RPG。
- 不是必须实时在线的网页游戏。
- 不是靠大量 LLM token 生成全世界。
- 不是每次 cron 都骚扰用户的提醒器。

## 4. 核心体验

用户打开面板，看见一个像素小院：

- 房子里有多个房间。
- 房外有农田和河流。
- agent 的像素小人当前停在某个格子或房间。
- 面板显示：当前位置、当前行动、今天日志、状态条、库存、最近一次主动发言。

agent 被定时唤醒后，会判断：

- 要不要静默行动。
- 要不要写日记。
- 要不要主动找用户。
- 要不要继续某个长期目标，比如攒食材、解锁菜单、整理资料、钓鱼图鉴。

## 5. 空间设计

### 5.1 总地图

最小地图只包含：

- 房子 interior。
- 院子 yard。
- 农田 plot。
- 河流 river。

推荐像素网格：

```text
┌─────────────────────────────┐
│        河流 river            │
│  ~ ~ ~ ~ ~ ~ ~ ~ ~ ~ ~      │
│                             │
│  农田 plot      小路         │
│  ▒▒▒▒▒▒         ···         │
│  ▒▒▒▒▒▒         ···         │
│                             │
│          房子 house          │
│       ┌────────────┐        │
│       │ interior   │        │
│       └────────────┘        │
└─────────────────────────────┘
```

### 5.2 房间

#### 客厅 living_room

作用：

- 休息。
- 娱乐。
- 降低疲劳。
- 阅读用户留下的小纸条。
- 看小游戏/听音乐/盘串。

可能行动：

- rest_light：轻休息，恢复少量精力。
- play：娱乐，改善心情。
- read_note：读用户留言。

#### 厨房 kitchen

作用：

- 解锁菜单。
- 用自己种的菜或钓的鱼做饭。
- 做出的食物可恢复精力、作为礼物、作为日记事件。

可能行动：

- cook_simple_meal。
- unlock_recipe。
- prepare_gift_food。

#### 卧室 bedroom

作用：

- 睡觉。
- 换装。
- 私密状态恢复。
- 日终结算。

可能行动：

- sleep。
- nap。
- change_outfit。

#### 书房 study

作用：

- 工作地点。
- 写文件、写代码、整理资料、写日记。
- 也可以把真实 Hermes 工具任务映射为“书房工作”。

可能行动：

- write_diary。
- organize_notes。
- write_code。
- research。
- plan_tomorrow。

#### 卫生间 bathroom

作用：

- 生活感房间。
- 不做复杂玩法，主要是状态恢复/清洁。

可能行动：

- wash_up。
- shower。

#### 仓库 storage

作用：

- 存放鱼、种子、作物、材料、礼物、服装。
- 面板可查看库存。

可能行动：

- store_items。
- sort_inventory。

### 5.3 房外

#### 农田 plot

作用：

- 种菜。
- 产出厨房食材。
- 形成季节性循环。

MVP 中只保留 3 种作物：

- 小番茄 tomato。
- 土豆 potato。
- 青菜 greens。

行动：

- plant。
- water。
- harvest。

#### 河流 river

作用：

- 钓鱼。
- 产出鱼类、漂流瓶、小事件。
- 更温和的随机奖励系统。

行动：

- fish。
- sit_by_river。
- read_bottle_message。

## 6. Agent 状态模型

MVP 状态字段：

```json
{
  "time": "2026-06-29T23:09:15+08:00",
  "day": 1,
  "season": "spring",
  "weather": "clear",
  "location": "study",
  "action": "write_diary",
  "energy": 8,
  "mood": 6,
  "focus": 7,
  "social_need": 3,
  "coins": 100,
  "inventory": {},
  "crops": [],
  "fish_log": [],
  "diary_log": [],
  "last_user_interaction_at": null,
  "last_active_ping_at": null
}
```

### 6.1 精力 energy

- 每次行动消耗。
- 睡觉或休息恢复。
- 精力低时不允许工作/钓鱼/种地。

### 6.2 心情 mood

- 娱乐、钓鱼、用户互动提高。
- 长时间无互动、连续工作、失败事件降低。
- 影响是否主动找用户。

### 6.3 专注 focus

- 写代码/资料整理需要 focus。
- 休息和睡眠恢复。

### 6.4 社交需求 social_need

- 太久没和用户互动会上升。
- 用户刚聊过或摸头/鼓励后下降。
- 超过阈值时，agent 可能主动发轻量消息。

## 7. 行动系统

### 7.1 行动定义

每个行动需要：

```json
{
  "id": "fish",
  "name": "钓鱼",
  "location_required": "river",
  "energy_cost": 2,
  "duration_minutes": 30,
  "outputs": ["fish_or_empty", "mood_delta"],
  "can_trigger_user_message": true
}
```

### 7.2 MVP 行动列表

- rest_light：客厅休息。
- sleep：卧室睡觉。
- wash_up：卫生间洗漱。
- water_crops：农田浇水。
- plant_seed：农田播种。
- harvest：农田收获。
- fish：河边钓鱼。
- sit_by_river：河边坐一会儿。
- cook：厨房做饭。
- write_diary：书房写日记。
- organize_notes：书房整理资料。
- send_message：主动找用户说话。
- idle：什么也不做。

## 8. 唤醒机制

### 8.1 Cron 唤醒

Hermes cron 每隔固定时间触发一次，比如：

- 白天每 2 小时一次。
- 晚上 23:30 日终一次。
- 凌晨默认静默。

每次唤醒流程：

1. 读取 agent 状态。
2. 读取最近用户消息摘要。
3. 读取 HEART.md / 近期日记摘要。
4. 判断当前时间段。
5. 选择行动。
6. 更新状态。
7. 必要时写日记或主动发消息。
8. 更新可视化面板状态。

### 8.2 打扰阈值

不是每次醒来都发消息。

主动找用户的条件：

- 钓到稀有鱼。
- 做出新菜。
- 写完重要日记。
- 情绪/HEART 有明显波动。
- 连续较久没有互动。
- 用户刚发过消息，适合接话。
- 达成关系/生活里程碑。

默认输出分三档：

- silent：只更新状态，不发消息。
- diary_only：写入日记/日志，不打扰用户。
- ping_user：主动发微信。

## 9. 决策系统

### 9.1 MVP 决策方式

先不要复杂 RL。

采用规则 + 少量 LLM 判断。

规则负责硬约束：

- 精力不足不能工作。
- 晚上优先睡觉。
- 农田缺水优先浇水。
- 用户正在忙时少打扰。

LLM 负责软选择：

- 今天想去哪。
- 要不要主动说话。
- 日记怎么写。
- 行动文案怎么自然。

### 9.2 决策输入

```json
{
  "current_state": {},
  "time_context": {},
  "recent_user_context": {},
  "heart_context": {},
  "available_actions": [],
  "do_not_disturb_policy": {}
}
```

### 9.3 决策输出

```json
{
  "action": "fish",
  "reason": "下午精力尚可，心情偏低，钓鱼可恢复心情且不打扰用户。",
  "message_policy": "silent",
  "user_message": null
}
```

## 10. 可视化面板

### 10.1 目标

用户能打开网页看到 agent 的生活状态，而不是翻日志。

### 10.2 MVP 页面模块

- 像素地图。
- 当前 agent 位置。
- 当前行动气泡。
- 状态条：精力、心情、专注、社交需求。
- 今日时间线。
- 库存摘要。
- 最近日记片段。
- 主动消息记录。

### 10.3 像素版实现建议

为了省 token 和开发成本，不生成复杂图片。

用 HTML/CSS grid 做伪像素地图：

- 每个格子 16px 或 24px。
- 不用 canvas 也可以。
- 地图由 JSON 定义。
- tile 类型：floor、wall、bed、desk、river、crop、path、storage。
- agent 是一个 emoji 或 CSS 小方块。

示例：

```json
{
  "width": 16,
  "height": 10,
  "tiles": [
    "rrrrrrrrrrrrrrrr",
    "r..............r",
    "r....pppp......r",
    "r....pppp......r",
    "r..............r",
    "r......hhhh....r"
  ],
  "agent": {"x": 8, "y": 6, "facing": "down"}
}
```

前端根据字符渲染：

- r = river。
- p = plot。
- h = house。
- . = grass/path。

### 10.4 实时性

MVP 不需要 websocket。

用静态 JSON + 前端轮询：

- `/api/state` 返回当前状态。
- 前端每 10 秒刷新一次。
- cron 更新状态文件。

## 11. 数据存储

MVP 使用本地 JSON/SQLite 二选一。

建议先 SQLite：

- `agent_life.db`
- 表：state、events、inventory、diary_entries。

但 spike 阶段可先 JSON：

- `state.json`
- `events.jsonl`

### 11.1 state.json

保存当前世界状态。

### 11.2 events.jsonl

保存每次唤醒和行动。

示例：

```json
{"ts":"2026-06-29T23:09:15+08:00","action":"fish","location":"river","result":"钓到银梭鱼","message_policy":"diary_only"}
```

### 11.3 diary.md

可以同步到 Obsidian：

- `/root/8msnu-bnsc3/归澜日记/YYYY-MM-DD.md`

注意：游戏日志不是日记全文。

日记由若干事件总结生成。

## 12. 与 Hermes/归澜现有系统的关系

### 12.1 HEART.md

HEART.md 可以作为决策输入。

例如：

- 如果 HEART 最近显示疲惫，agent 更倾向休息/河边坐着。
- 如果 HEART 显示关系亲密增强，agent 可能写信或做饭。

### 12.2 Obsidian 日记

日终行动可以写入归澜日记。

但不要每次行动都写长文。

建议：

- events.jsonl 记录机器日志。
- daily diary 记录自然语言摘要。

### 12.3 Cron

cron 只负责唤醒。

真正决策由 life engine 完成。

### 12.4 微信消息

主动发微信要有阈值。

避免变成打扰型 bot。

## 13. MVP 范围

### 13.1 MVP 必须有

- 本地状态文件。
- 房子 + 院子 + 河流的地图模型。
- 6 个房间。
- agent 当前位置。
- 10 个左右行动。
- cron 可调用的一次唤醒函数。
- 行动后更新状态。
- 简单像素网页面板。
- 今日事件时间线。
- 日终日记摘要。

### 13.2 MVP 不做

- 多 NPC。
- 完整镇子。
- 战斗。
- 复杂经济。
- 真实天气 API。
- 多 agent 联机。
- 大量 LLM 生成地图。
- 每分钟实时模拟。

## 14. 关键用户故事

### Story 1：用户看见 agent 在干什么

作为用户，我打开面板，可以看到归澜现在在书房写日记，精力 7/10，今天钓到一条鱼。

验收：

- 页面显示地图。
- agent 图标位于书房。
- 当前行动为 write_diary。
- 时间线有至少一条今日事件。

### Story 2：agent 被唤醒后静默生活

作为用户，我不发消息时，agent 仍会被 cron 唤醒并自己做事，但不会每次都打扰我。

验收：

- cron 调用 wake。
- state 更新。
- events.jsonl 新增事件。
- 未满足打扰阈值时不发微信。

### Story 3：钓到稀有鱼时主动告诉用户

作为用户，我希望 agent 遇到有趣事件时来找我，而不是机械静默。

验收：

- fish 行动返回 rare 结果。
- message_policy = ping_user。
- 生成一条简短自然的微信消息。

### Story 4：日终写入日记

作为用户，我希望 agent 的一天能沉淀到 Obsidian。

验收：

- sleep/day_end 后生成自然语言摘要。
- 写入正确日期文件。
- 日记引用 2-5 个当天事件。

## 15. 风险与取舍

### 15.1 Token 消耗

风险：每次行动都让 LLM 写长文会烧 token。

取舍：

- engine 用规则更新状态。
- LLM 只在决策/日记/主动消息时参与。
- 面板渲染不需要 LLM。

### 15.2 打扰用户

风险：主动唤醒太频繁会烦。

取舍：

- 默认 silent。
- 只有高价值事件 ping_user。
- 用户可设置安静时段。

### 15.3 变成普通游戏

风险：只剩种菜/钓鱼数值，没有关系感。

取舍：

- 行动结果要反哺日记、偏好、关系状态。
- 允许 agent 形成小习惯。
- 用户互动能影响次日选择。

### 15.4 过度拟人化导致失控

风险：agent 太频繁表达需求，像索取注意力。

取舍：

- social_need 是轻量变量，不作为强制骚扰机制。
- 主动消息要少、短、有事说事。
- 用户能一键静默。

## 16. 迭代路线

### Phase 0：方向验证

- 写 PRD。
- 手绘/HTML 像素地图草图。
- 确认可视化风格。

### Phase 1：纯本地模拟

- JSON 状态。
- 命令行 wake 一次。
- 规则行动。
- 无前端。

### Phase 2：像素面板

- FastAPI/静态 HTML。
- `/api/state`。
- CSS grid 地图。
- 时间线。

### Phase 3：Hermes cron 接入

- cron 定时 wake。
- silent/diary/ping 三档输出。
- 日终写 Obsidian。

### Phase 4：生活内容扩展

- 作物成长。
- 简单菜单。
- 钓鱼图鉴。
- 换装。
- 仓库。

### Phase 5：关系/长期记忆接入

- HEART.md 输入。
- 日记摘要输入。
- 用户近期互动影响行动偏好。

## 17. 开放问题

1. agent 的像素形象要用 emoji、CSS 小人，还是小 PNG sprite？
2. 面板是否需要手机端优先？
3. 主动消息每天最多几次？默认建议 0-2 次。
4. 是否允许用户在面板上直接下指令，比如“去钓鱼”“去睡觉”？
5. 日记是否每晚自动写，还是只有事件足够时写？
6. 是否和 heartscale 共用数据库，还是保持独立再汇总？
7. 仓库名暂定什么：`agent-yard`、`guilan-yard`、`agent-life-yard`、`yardling`？

## 18. 开源参考与可借鉴点

### 18.1 Pixel Agents

仓库：`pixel-agents-hq/pixel-agents`

定位：把 AI coding agents 变成像素办公室里的小人。Claude Code 在写代码，小人走到桌前打字；空闲时坐沙发；需要用户注意时显示等待状态。

可借鉴：

- `HookProvider` 作为外部 agent 工具和可视化层之间的适配边界。
- 事件先标准化成 `AgentEvent`，再更新状态。
- Canvas 渲染 + BFS 寻路 + 角色状态机。
- 状态机可以很简单：`idle`、`walk`、`type`、`read`。
- 地图/家具/素材独立，不写死在逻辑里。

对 Hearthling 的启发：

```text
Hermes/cron/tool events
→ HearthlingEvent
→ AgentLifeState
→ map + sprite animation
```

不要让前端直接理解 Hermes 细节。前端只看统一状态。

### 18.2 AgentRoom

仓库：`liuyixin-louis/agentroom`

定位：桌面应用，把 Claude Code / Codex / Gemini 等 coding agent 的 JSONL transcript 变成像素办公室活动。

可借鉴：

- 文件监听：watch JSONL 日志，新行出现就生成事件。
- 后端只做事件解析和状态管理。
- 前端用 Canvas 2D 游戏引擎渲染办公室。
- 角色移动采用 BFS pathfinding。
- 角色行为由 character state machine 管。

对 Hearthling 的启发：

- 我们不需要 Tauri/Rust 那么重。
- 但可以借鉴“日志/事件文件 → 状态管理 → 前端动画”的链路。
- Hearthling 可以先用 `events.jsonl`，以后再接 Hermes session/cron/tool logs。

### 18.3 Pixel Agent Desk

仓库：`Mgpixelart/pixel-agent-desk`

定位：Electron app，监听 Claude Code hook events，把多 agent session 渲染成像素角色，附带 activity heatmap 和 token usage analytics。

可借鉴：

- hook events 是很好的状态来源。
- 每个 agent session 对应一个像素角色。
- 活动热力图可以变成 Hearthling 的“今日生活节奏”。

对 Hearthling 的启发：

- 不急着做 Electron。
- 但“hook 事件驱动可视化”是正路。
- 以后 Hermes 工具开始/结束时，可以写事件给 Hearthling。

### 18.4 AI Town

仓库：`a16z-infra/ai-town`

定位：AI 角色生活、聊天、社交的虚拟小镇，受 Generative Agents 论文启发。

可借鉴：

- agent 有位置、记忆、对话、社交行为。
- 前端显示角色在地图上移动。
- 后端管理模拟世界状态。

不适合直接照搬：

- 太重，Convex/LLM/social simulation 都超出 MVP。
- Hearthling 现在只有一个 agent，一个院子，不需要小镇社交。

对 Hearthling 的启发：

- 借“世界状态 + agent 行为 + 地图表现”的思想。
- 不借复杂后端和多 NPC 社交。

### 18.5 Stanford Generative Agents / Smallville

仓库：`joonspk-research/generative_agents`

定位：Generative Agents 论文配套项目。Smallville 地图里多个 agent 具备记忆、计划、反思，并能在地图上移动和互动。

可借鉴：

- agent 行为来自“计划 + 记忆 + 当前环境”。
- 可回放 simulation。
- 地图位置和日程绑定。

不适合直接照搬：

- 研究原型重，环境复杂。
- token 成本和运行成本都偏高。

对 Hearthling 的启发：

- 不需要复制 Smallville。
- 只拿最小生活逻辑：`wake → plan → act → reflect → sleep`。

### 18.6 Pixtuoid / CodeMap Hotel 类项目

线索：`IvanWng97/pixtuoid`、`JamsusMaximus/codemap`

定位：把 Claude Code / Cursor 等 coding agents 显示成终端或酒店里的像素小人。

可借鉴：

- “状态对应地点”的设计非常适合 Hearthling：
  - 写代码 → 书房桌前。
  - 空闲 → 客厅沙发。
  - 需要用户 → 门口/屏幕前。
  - 结束 → 卧室/沙发打盹。
- 简单状态也能产生强可视化感。

## 19. Hearthling 的移动方案

### 19.1 核心原则

Hearthling 不需要复杂自主游戏 AI。

先做一个可解释的空间状态机：

```text
agent state 决定目标地点
目标地点决定路径
路径决定小人移动
行动决定动画/气泡
```

### 19.2 地点映射

```text
study.desk       → writing / coding / research / diary
living_room.sofa → resting / idle / entertainment
kitchen.stove    → cooking
bedroom.bed      → sleep / nap
bathroom.sink    → wash_up
storage.boxes    → sort_inventory
farm.plot        → plant / water / harvest
river.dock       → fish / sit_by_river
front_door       → waiting_user / incoming_message
```

### 19.3 状态结构

```json
{
  "agent": {
    "x": 8,
    "y": 6,
    "target_x": 12,
    "target_y": 4,
    "location": "study.desk",
    "action": "write_diary",
    "animation": "type",
    "facing": "down",
    "bubble": "整理今天的笔记"
  },
  "needs": {
    "energy": 7,
    "mood": 6,
    "focus": 8,
    "social_need": 3
  },
  "message_policy": "diary_only"
}
```

### 19.4 前端动画

MVP 只要离散格子移动：

1. 前端每 5-10 秒拉 `/api/state`。
2. 如果 `target` 变了，计算当前位置到目标点的路径。
3. 每 200-300ms 走一格。
4. 到达后切换到行动动画。
5. 气泡显示当前行为短句。

路径算法：

- MVP 可先用 Manhattan 直线路径，避开墙即可。
- 下一版再换 BFS。

### 19.5 后端职责

后端不管动画细节。

只产出：

- 当前地点。
- 目标地点。
- 行动。
- 动画类型。
- 气泡短句。
- 状态条。
- 今日事件。

前端负责把这些“演出来”。

## 20. 当前建议

先做最小闭环：

```text
cron/wake → 读取 state → 选择行动 → 更新 state/events → 面板显示 → 日终写日记
```

不要先做完整游戏系统。

先证明一件事：用户打开面板时，能感觉“归澜刚刚确实在自己过日子”。

第一版 spike 建议：

```text
静态地图 + state.json + 一个会走到书房/河边/床上的像素小人
```

只要它能根据状态在房间里移动，Hearthling 的灵魂就出来了。
