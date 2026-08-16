# 产品蓝图 · 量潮产品云 (QtCloud Product)

> 核心问题：做的是不是用户要的？体验是否到位？技术对不对？

---

## 〇、业务视角

### 1. 业务背景

- **所属领域**：产品研发管理
- **解决的核心问题**：产品越来越多，产品决策越来越困难；产品团队的知识分散在个人头脑和零散文档中，缺乏共享的产品全景视图，导致认知偏差和决策低效
- **业务方**：产品管理部门、研发团队

### 2. 业务价值

- **认知对齐**：一张画布让所有人看到同一张产品地图，减少沟通偏差
- **决策提效**：MVP 发布线拖拽即可调整版本范围，快速响应变化
- **组合决策**：跨产品的组合视图支撑产品取舍与资源分配
- **知识沉淀**：产品结构知识显式化、文档化，新人上手有据可依

### 3. 成功指标

| 指标 | 衡量方式 |
|------|---------|
| 团队覆盖 | 产品团队使用 Studio 维护故事地图的比例 |
| 认知一致性 | 团队对版本范围的理解是否一致（定性） |
| 文档关联度 | PRD/IxD/ADD 与故事地图的对应覆盖率 |
| 组合覆盖 | 平台集中管理的产品占全部产品的比例 |

---

## 一、产品视角

### 1. 产品定位

- **产品名称**：量潮产品云（QtCloud Product）— 产品决策平台；QtCloud Studio 是其画布客户端
- **核心价值主张**：将产品团队的隐性结构知识显式化，通过共享的可视化视图——单产品的故事地图与多产品的组合视图——集中管理和可视化所有产品，支撑产品决策
- **目标用户**：产品负责人、产品经理、设计师、工程师

### 2. 功能域

| 模块 | 职责 | 优先级 |
|------|------|--------|
| 用户故事地图看板 | 需求模块：二维矩阵（活动层 → 任务层 → Release 行） | P0 |
| 产品切换器 | 每个产品 = 一个项目空间，顶部 switcher 切换 | P0 |
| 规格（事件风暴） | 规格模块：以事件风暴梳理产品规格，识别领域事件与业务规则（规划中） | P1 |
| Release 行 | 故事按发布版本（MVP / 未来迭代）分组为行，可折叠 | P0 |
| 故事卡拖放 | 跨任务列拖放故事卡片（矩阵列容器为目标），重新组织地图 | P0 |
| 文档网站 | MyST Markdown 发布 PRD/IxD/ADD 文档 | P1 |
| 产品目录 | 登记产品、状态与负责人，形成产品清单 | P1 |
| 组合视图 | 跨产品可视化：成熟度、投入分布、版本时间线 | P1 |
| LLM PRD 编写 | AI 辅助生成 PRD（实验性） | P2 |
| 决策记录 | 决策关联产品与依据，可追溯 | P2 |

### 3. 用户流程

```
1. PM 打开 Studio → 顶部产品切换器（每个产品 = 一个项目空间），默认进入 qtcloud-devops 空间
2. 产品空间侧边导航：需求（用户故事地图看板）/ 规格（事件风暴，规划中）
3. PM 在需求看板的任务列间拖放故事卡片 → 重新组织用户故事
4. PM 折叠/展开 Release 行 → 聚焦某个发布版本的故事范围
5. PM 切换产品空间或模块 → 浏览其他产品/规格
```

### 4. 设计决策

| 决策 | 理由 | 舍弃 |
|------|------|------|
| Canvas 画布渲染 | 复杂图形场景下 DOM 性能不足 | DOM 的天然可访问性 |
| Flutter 全平台 | 一套代码覆盖桌面/移动/Web | Web 包体积较大 |
| 回调驱动数据流 | 简单明确，无需状态管理库 | 复杂状态时追踪困难 |
| 不可变模型 (copyWith) | 避免副作用，数据流清晰 | 频繁 GC 开销 |

---

## 二、设计视角

### 1. 布局结构

顶部产品切换器 + 产品空间布局（参考项目管理软件）：切换器选择产品（每个产品 = 一个项目空间），空间内为侧边导航（需求 / 规格）+ 内容区。需求为故事地图看板，采用「二维矩阵 + 跨列合并」：X 轴为任务列（活动层橙 / 任务层紫，活动跨列合并），Y 轴为 Release 版本行（可折叠），双轴滚动。

### 2. 核心组件

| 组件 | 职责 |
|------|------|
| StoryMapCanvas | 顶层画布容器（二维矩阵渲染 + 拖拽） |
| ActivityLayerRow | 活动层（橙色，跨列合并） |
| TaskLayerRow | 任务层（紫色，每列一个任务） |
| ReleaseRow | Release 行（可折叠，故事卡片 + 拖放目标） |
| StoryCard | 故事明细卡（仅标题，LongPressDraggable） |

### 3. 样式方案

- **主题**：Material 3，视觉减负——活动层橙 `#FFB74D`、任务层紫 `#B39DDB` 规范色；故事卡片白色细边框，仅展示标题；Release 行灰分隔线
- **实现**：Flutter Material Design

### 4. 交互

- 垂直滚动浏览 Release 行，水平滚动浏览任务列
- Release 行点击折叠 / 展开
- 长按拖拽故事卡片跨任务列移动
- 故事卡片点击交互（回调待实现业务逻辑）

---

## 三、技术视角

### 1. 架构概览

Flutter 桌面/Web 应用，纯客户端原型，无后端依赖。

```
ProductCloudScreen（根组件：顶部产品切换器 + 产品空间）
  ├── models/        → 领域模型（Product, StoryMap, UserActivity, UserTask, UserStory）
  ├── assets/data/   → 种子数据（manifest + products/*.json，CLI 加工，Studio 渲染）
  ├── screens/       → 页面模块（ProductCloudScreen / RequirementScreen / SpecificationScreen）
  ├── widgets/       → 画布组件（Canvas, ActivityLayerRow, TaskLayerRow, ReleaseRow, StoryCard）
  └── main.dart      → 应用入口（加载种子数据 → ProductCloudScreen）
```

### 2. 技术栈

| 层 | 技术 |
|----|------|
| UI 框架 | Flutter (Dart 3.10+), Material 3 |
| 状态管理 | StatefulWidget + copyWith 不可变模式 |
| 拖放 | Flutter 原生 Draggable / DragTarget |
| 文档 | MyST Markdown (mystmd) → GitHub Pages |
| 种子数据 | assets/data/products.json（JSON，CLI 加工，Studio rootBundle 加载） |
| CI/CD | GitHub Actions：deploy-docs（docs → Pages）+ deploy-studio（studio/* tag → OSS+CDN） |
| 部署 | OSS 静态桶 + CDN product.cloud.quanttide.com（IaC：manifests/terraform） |
| 工具脚本 | Python (PRD 编写助手, LLM 调用) |

### 3. 数据流

```
用户操作（拖放/点击/拖动发布线）
    ↓
回调事件（onStoryMove / onStoryTap / onMVPLineMove）
    ↓
上层组件更新数据（copyWith）
    ↓
画布重绘
```

数据纯内存，不持久化。所有模型不可变，通过 `copyWith` 创建新实例。

### 4. 架构决策 (ADR)

| ADR | 方案 | 理由 |
|-----|------|------|
| 领域模型命名 | StoryMap → UserActivity → UserTask → UserStory | 反映业务概念，遵循故事地图方法论 |
| UI 组件命名 | Canvas → ActivityLayerRow → TaskLayerRow → ReleaseRow → StoryCard | 反映视觉形态，与领域模型分离 |
| 视图与逻辑分离 | 视图不修改数据，通过回调上传事件 | 画布不包含业务逻辑 |
| 全平台 | Flutter | 一套代码覆盖桌面/移动/Web |
