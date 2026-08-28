# 量潮产品研发档案索引

本目录记录每个产品的产品级思考与需求地图，是产品体系建设的核心载体。

## 目录结构

每个产品一个目录，目录名与领域仓库 `apps/` 或 `packages/` 中的子模块名保持一致：

```
<product-name>/
├── index.md        # 产品档案：产品级思考
└── requirement.md  # 需求档案：用户故事地图
```

## 产品体系全景

### 核心理念

1. **产品即服务**：以产品形态提供服务，而非抽象工程
2. **数据驱动**：用运营数据找到产品的结构，让产品在真实使用中持续被检验
3. **阶段方法**：产品研发分阶段推进（产品策划→架构师→项目经理→QA→产品运营）
4. **需求明确性**：能做出规格、写得出用户故事即明确，写不出来即模糊

### 产品分层架构

#### 前台/用户侧产品（非云系列）

| 产品 | 定位 | 核心价值 |
|------|------|----------|
| [qtclass](qtclass/index.md) | 课程平台，学习内容的入口 | 快速拼起课程体系，收集外部教程 |
| [qtcrowd](qtcrowd/index.md) | 课堂→众包→招聘链中的信用平台 | 信用积累与标准化外化 |
| [qtdata](qtdata/index.md) | 以项目制交付的数据处理服务 | 让客户以可预期的成本获得数据 |
| [qtfiction](qtfiction/index.md) | 小说作品的合规黄页/链接导航 | 规避合规风险，汇集作品入口 |
| [qthealth](qthealth/index.md) | 面向个人的健康管理前台应用 | 高频健康记录，情绪日记等 |
| [qtmedia](qtmedia/index.md) | 新媒体运营的首要发布渠道 | 统一发布渠道，快速信息发布 |
| [qtrecurit](qtrecurit/index.md) | 候选人的透明窗口与信任场 | 公平透明的筛选机制，信用系统 |

#### 后台/平台产品（qtcloud-* 系列）

**核心底座层**

| 产品 | 定位 | 核心价值 |
|------|------|----------|
| [qtcloud](qtcloud/index.md) | 主工作站，日常工作平台与产品体系核心 | 替代 WorkBuddy 等工作平台，最高频入口 |
| [qtcloud-product](qtcloud-product/index.md) | 量潮云的核心后台 | 构建产品体系的元结构 |
| [qtcloud-data](qtcloud-data/index.md) | 各产品数据处理与存储的底座 | 数据契约、采集、加工、AI生成、存储 |
| [qtcloud-business](qtcloud-business/index.md) | 以"业务"为中心的商务机制底座 | 定义业务→订单实例化→数据回流校准 |
| [qtcloud-econ](qtcloud-econ/index.md) | 各产品经济机制的底座 | 定价机制、激励机制、风险机制 |
| [qtcloud-security](qtcloud-security/index.md) | 数据安全与赔付机制的底座 | 安全分级、SLA绑定赔付 |

**业务云层**

| 产品 | 定位 | 核心价值 |
|------|------|----------|
| [qtcloud-course](qtcloud-course/index.md) | 课程研发与交付平台 | 分解→制作→审核→组合的最小循环 |
| [qtcloud-crowd](qtcloud-crowd/index.md) | 众包平台的管理后台 | 运营管理、任务管理、数据观测 |
| [qtcloud-execute](qtcloud-execute/index.md) | 高密度复杂分工下的任务执行平台 | 任务列表为中心的结构，CEO任务机制 |

**职能云层**

| 产品 | 定位 | 核心价值 |
|------|------|----------|
| [qtcloud-finance](qtcloud-finance/index.md) | 算账工具而非记账工具 | 无精确账也能估算，最高加密 |
| [qtcloud-devops](qtcloud-devops/index.md) | 部署与发布的可观测性 | 已读先行，缓存式服务端 |
| [qtcloud-connect](qtcloud-connect/index.md) | 沟通渠道管理 | 操作与验证分离 |
| [qtcloud-growth](qtcloud-growth/index.md) | 增长逻辑梳理与漏斗建模 | 漏斗可编辑，指导团队行动 |
| [qtcloud-delib](qtcloud-delib/index.md) | 议事与决策平台 | 约束代表履职，评估决策循环健康度 |

**工具云层**

| 产品 | 定位 | 核心价值 |
|------|------|----------|
| [qtcloud-agent](qtcloud-agent/index.md) | 智能体治理与沉淀平台 | 隐式流程显性化，对话即资产 |
| [qtcloud-think](qtcloud-think/index.md) | 认知工程平台 | 感知→理解→预测→决策的闭环 |
| [qtcloud-read](qtcloud-read/index.md) | 团队协作阅读器 | 阅读器先行，协作演化 |
| [qtcloud-write](qtcloud-write/index.md) | 标准化大批量写作工具 | 解决AI输出偏差与人类阅读不适 |
| [qtcloud-docs](qtcloud-docs/index.md) | 文档格式转换与成书 | 转换成Markdown，编辑成书 |
| [qtcloud-asset](qtcloud-asset/index.md) | 知识资产的契约中心 | 资产可维护、可复用 |
| [qtcloud-secret](qtcloud-secret/index.md) | 机密的安全传递与存储 | 先传递，后存储 |
| [qtcloud-infra](qtcloud-infra/index.md) | 事故报告收集与基础服务 | 先收集，后定用途 |

## 架构思想

### 前台/后台分离
- 前台（qtcrowd、qtdata等）面向终端用户
- 后台（qtcloud-*）提供管理、运营、数据观测能力
- 示例：qtcrowd（前台接单交付）↔ qtcloud-crowd（后台运营管理）

### 市场/执行分离
- 市场侧（qtcrowd）负责撮合与信用
- 执行侧（qtcloud-course）负责交付执行

### 职能域独立
- 一个云只做一件事
- 协同时引用（"见xxx/operation.md"），不合并

### 数据归属严格
- 生产数据归执行侧
- 市场数据归后台
- 用户故事归前台

## 商业化模式

### 以数据为中心的定价
- 处理费 + 存储费双轨制
- 不付费即删除，不背永久包袱
- 资源包、会员固定化收入

### SLA与赔付机制
- 安全等级量化分级（泄露/丢失/异常）
- 保险式定价（溢价覆盖风险）
- 分级赔付条款

### 信用系统
- 信用数据是冷启动资产与复利来源
- 越用越值钱，形成护城河

## 产品研发方法论

1. **需求刻画**：用户故事地图（活动→任务→故事）
2. **规格描述**：事件风暴（领域事件→聚合→业务规则）
3. **阶段流转**：产品策划→架构师→项目经理→QA→产品运营
4. **明确性检验**：能做出规格、写得出用户故事即明确

## 数据驱动策略

- **运营数据**：持续积累，逐渐告诉产品体系应该长成什么样
- **小样本训练**：先找到数据结构，再填满数据
- **冷启动资产**：历史数据校准AI，信用数据复利增长
- **机制偏差检测**：赔付率、坏账率、异常报价偏离预期即触发复审

## AI协作边界

- AI产出必过人审，草稿不是最终需求
- 需求描述无歧义即正确，AI觉得没有歧义就是对的
- "不想要什么"是更精确的围栏
- 端侧可接自有模型，服务端好维护好商业化

## 约定

- 需求明确性的检验：能做出规格、写得出用户故事就是明确的；写不出来就是模糊的；仍看不出来就看需求在阶段间的流转是否顺畅
- 档案随迭代更新，反映当前理解，不追求一次写全
- **边界划分十分严格**：`qtcloud-*`（云系列）= 平台/后台/职能域；非云系列（`qtcrowd`、`qtdata`、`qthealth`、`qtrecurit` 等）= 前台/用户侧
- 运营策略可单独成档（`operation.md`），与产品档案（index.md）、需求档案（requirement.md）并列
