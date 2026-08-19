# DetailFlow（WorkBuddy 版 · 电商详情页 Skill）

DetailFlow 是一个用于**规划、生成、审核和交付电商详情页**的 WorkBuddy Skill。它从 Codex 原版（[AJbeckliy/detail-flow](https://github.com/AJbeckliy/detail-flow)）转换而来，工作契约与能力保持不变，只是把图像生成等环节对接到了 WorkBuddy 的内置能力。

> 本仓库是 [@billypala](https://github.com/billypala) 的 fork，面向**抖音 / 多平台电商带货**场景：给一张产品图 + 一张风格参考图，就能产出一套连贯的 8 屏带货详情长图。

## 服务对象

- 抖音、小红书、淘宝、拼多多、闲鱼等平台的**电商卖家 / 带货运营**
- 需要批量、稳定地产出**产品详情页 / 8 屏长图 / 主图详情图**的个人或团队
- 希望「AI 先出规划、人工确认后再生成、出问题只局部返修」的可控生产流程

## 核心能力

- 分析产品图与风格参考图，区分「已确认信息 / 图片可见事实 / AI 推断」
- 先输出 8 屏内容与视觉规划，确认后再生成，避免一口气乱出图
- 用首屏「卖点种子」串联整条购买叙事，避免 8 屏各说各话
- 建立文字视觉母版 + 可选 1:3 图像母版，统一配色、光线、字体与节奏
- 默认生成 9:21 竖版分屏，前 2–3 屏阶段就拼接预览、提前排雷
- 审核产品漂移、文字错乱、编造参数；问题定位到**最小返修层**，不整页重来
- 自动整理母版、单屏、预览图与完整长图到交付文件夹

## 与 Codex 原版的适配差异

| 环节 | Codex 原版 | 本 WorkBuddy 版 |
|------|-----------|----------------|
| 图像生成 | 调用 Codex 图像管线 | 内置 **ImageGen（文生图）** 生成 1:3 母版与 9:21 分屏 |
| 拼接预览 | 工具相关 | 用 **Python(PIL)** 把分屏按顺序拼成预览长图 |
| 图片自检 | Agent 读图 | 用 **Read 工具**读取生成图做连贯性 / 漂移 / 文字审查 |
| 触发方式 | `$detail-flow` | 直接说需求，或 `/detail-flow <产品图> [风格参考图]` |
| 接口元数据 | `agents/openai.yaml` | 已移除（WorkBuddy 用 SKILL.md frontmatter） |

> 关键纪律不变：**两次人工确认门禁**（先确认蓝图、再确认视觉样张包）；**不编造参数 / 认证 / 功效**；失败只返修最小责任层。

## 安装

### 方式一：克隆到 WorkBuddy 用户级 Skill 目录（推荐，跨项目可用）

Windows PowerShell：

```powershell
git clone https://github.com/billypala/detail-flow.git "$env:USERPROFILE\.workbuddy\skills\detail-flow"
```

macOS / Linux：

```bash
git clone https://github.com/billypala/detail-flow.git ~/.workbuddy/skills/detail-flow
```

### 方式二：在 WorkBuddy 中直接安装

向 WorkBuddy 提出：

```text
请从 https://github.com/billypala/detail-flow 安装这个 Skill。
```

安装后，给它产品图 + 风格参考图并说「生成 8 屏电商详情页」即可触发；也可手动 `/detail-flow <产品图> [风格参考图]`。

## 使用方式

最简请求：

```text
图1是产品图，图2是风格参考。
请为这个产品生成一套8屏电商详情页。
```

补充产品信息（推荐，减少 AI 编造）：

```text
产品名称：SonicAir H7 Pro
核心卖点：长续航、舒适包耳、蓝牙5.3、折叠收纳
目标人群：通勤和日常娱乐用户
禁止出现：主动降噪、防水、未经确认的认证
```

若未提供卖点 / 参数，DetailFlow 会根据产品图与品类合理推断，但会**明确提示这些为 AI 推断，商业使用前需你复核**。

## 工作流程

1. **分析输入** — 识别产品外观、结构、品牌元素，以及参考图的色彩、光线、人物与构图语言。
2. **确认产品信息** — 邀请你补充卖点、参数、受众与禁用宣传词（非强制问卷）。
3. **输出页面规划** — 先生成每屏的消费者问题、页面任务、文案结构、视觉模块、内容密度与衔接方式。
4. **用户确认（门禁 1）** — 你确认 / 修改蓝图后，才进入图像生成。
5. **建立视觉母版** — 锁定配色、光线、产品规则、字体层级、叙事节奏与连续视觉元素。
6. **生成前 2 屏 + 拼接预览** — 检查产品还原、参考风格、文案差异与页面统一度。
7. **用户确认（门禁 2）** — 你确认视觉样张包（1:3 母版 + 前 2 屏 + 预览 + 审核）后，才生成剩余分屏。
8. **生成剩余分屏 + 最终审核** — 保持同一视觉系统，检查漂移、错字、编造与断裂。
9. **局部返修与交付** — 仅改受影响的文案 / 产品 / 单屏 / 相邻屏，输出最终文件夹路径。

## 文件结构

```text
detail-flow/
├── SKILL.md                         # 核心工作契约与执行规则（WorkBuddy frontmatter）
├── README.md                       # 本说明
├── LICENSE                         # MIT
└── references/
    └── detail-page-patterns.md     # 详情页结构、文案模式、审核标准与返修策略
```

- `SKILL.md`：权威执行契约（英文），含两次确认门禁等 MUST 规则。
- `references/detail-page-patterns.md`：选版式、做审核、定位返修层时的参考手册。

## 设计原则

- 详情页是一条完整购买叙事，不是 8 张独立海报。
- 后续屏幕应展开首屏卖点，而不是不断加无关新卖点。
- 屏幕权重应有强弱、疏密、节奏变化，不要求都一样重。
- 产品图是身份基准，不能随意改颜色、结构、Logo 或佩戴方向。
- 对无法确认的参数、认证、奖项、功效、促销保持克制。
- 图像模型无法一次生成完全正确，审核与局部返修是正式流程的一部分。

## 当前限制

- 图像模型可能产生产品结构或文字细节漂移，需要人工审核。
- 1:3 母版用于统一视觉与叙事，不应直接裁切为最终分屏。
- 独立生成的分屏无法保证像同一张照片一样完全无缝连接（详见 `references/detail-page-patterns.md` 的连贯性规则）。
- 医疗、功效、认证和精确参数必须由你提供可靠信息。

## Repository

- 本 fork：https://github.com/billypala/detail-flow
- 上游原版：https://github.com/AJbeckliy/detail-flow

## 致谢

本 Skill 由 [AJbeckliy/detail-flow](https://github.com/AJbeckliy/detail-flow) 转换而来，版权与核心工作流归属原作者；本 fork 仅做 WorkBuddy 适配与电商场景资料完善。
