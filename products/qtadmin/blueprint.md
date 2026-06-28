# 产品蓝图 · 量潮管理后台 (QtAdmin)

> 核心问题：做的是不是用户要的？体验是否到位？技术对不对？

---

## 〇、业务视角

### 1. 业务背景

- **所属领域**：企业内部管理
- **解决的核心问题**：多业务线、多职能的管理入口分散，信息孤岛严重，管理层缺乏全局视角
- **业务方**：创始人、各业务线负责人、运营管理

### 2. 业务价值

- **统一入口**：一个后台覆盖所有业务线与职能领域，无需多系统切换
- **第二大脑**：整合信息、知识和决策链，从数据到洞察
- **自动化**：CLI 自动化合规检查，降低人工审核成本

### 3. 成功指标

| 指标 | 衡量方式 |
|------|---------|
| 接入业务线数 | 已纳入系统的业务域数量 |
| 资产合规率 | asset status 检查的合规通过率 |
| 知识覆盖率 | knowl 提取的知识结构与实际业务文档的覆盖度 |

---

## 一、产品视角

### 1. 产品定位

- **产品名称**：量潮管理后台 (QtAdmin) — 公司第二大脑
- **核心价值主张**：公司内部全面管理工具，覆盖各业务线与职能领域，以第二大脑理念整合信息与决策
- **目标用户**：内部运营、管理员、各业务线负责人
- **三种交付形态**：Studio（Flutter 客户端）、CLI（Rust 命令行）、Provider（Go API 服务）

### 2. 功能域

| 模块 | 职责 | 层级 | 优先级 |
|------|------|------|--------|
| Dashboard | 多工作区概览、决策卡片、KPI | Studio | P0 |
| QtConsult | 咨询项目双面板决策看板 | Studio / CLI / Provider | P0 |
| QtClass | 课堂管理（校企/基地/内训/1对1 四类） | Studio / CLI / Provider | P0 |
| Human | 员工/部门/岗位 CRUD、招聘计划 | CLI / Provider | P0 |
| Asset | 日志归档、结构合规检查、语义质量评分 | CLI | P0 |
| Connect | 飞书邮件通知、规则分发 | CLI / Provider | P0 |
| Knowl | 知识采集（LLM 文档提取）、本体抽取 | CLI | P1 |
| 用户故事地图 | 产品需求画布 | Studio | P2 |

### 3. 用户流程

```
1. 管理员登录 Studio → 选择工作区（创始人/公司）→ 查看 Dashboard
2. 管理业务数据 → 在 QtConsult/QtClass 中完成 CRUD
3. 日常合规 → CLI 执行 asset status/quality 检查数字资产质量
4. 知识积累 → CLI 执行 knowl 从文档中提取知识
5. 通知分发 → Connect 模块自动发送飞书邮件通知
```

### 4. 设计决策

| 决策 | 理由 | 舍弃 |
|------|------|------|
| 双域模型（业务域/职能域） | 清晰划分业务线与跨领域能力 | 扁平命名空间的简单性 |
| Fixture 驱动开发 | Studio 依赖 JSON fixture 数据层，零后端即可开发 | 无法验证后端 API 集成 |
| 实验→工程管道 | Python 原型 → Rust CLI 稳定 → 文档沉淀 | 多语言维护成本 |
| 三形态交付 | Studio 可视化、CLI 自动化、Provider API 集成 | 多端功能一致性的维护负担 |

---

## 二、设计视角

### 1. 布局结构

经典管理后台布局：可配置侧栏导航 + 内容区。导航结构来自 JSON fixture，添加工作区无需改 Dart 代码。响应式：适配桌面与移动端。

### 2. 核心组件

| 组件 | 职责 |
|------|------|
| NavSidebar | 可配置侧栏导航（带分区和分隔线） |
| Dashboard | 今日概览 + 业务决策卡片 + 职能 KPI |
| QtConsult 双面板 | 信息板（发现/沟通）+ 策略板（需求/策略/决策链） |
| QtClass 卡片展示 | 四类课程卡片布局 |
| Thinking | 认知进化分析报告页 |

### 3. 样式方案

- **主题**：Material 3，专业商务风格
- **实现**：Flutter Material Design + flutter_bloc 管理状态

### 4. 交互

- 侧栏导航切换工作区/页面
- Dashboard 响应式布局适配不同屏幕
- QtConsult 双面板信息浏览与 CRUD 操作

---

## 三、技术视角

### 1. 架构概览

三形态交付：

```
Studio (Flutter) — 前端 UI
   ├── 依赖 data_sources 包加载 JSON fixture
   └── 通过 BLoC 模式管理页面状态

CLI (Rust) — 命令行工具
   ├── 12 个子命令分组，覆盖所有业务域/职能域
   └── --provider 标志切换本地/API 模式

Provider (Go) — REST API 服务
   ├── FileStore（开发）/ S3（生产预留）
   └── 配置：默认值 → JSON 文件 → 环境变量
```

### 2. 技术栈

| 形态 | 语言 | 框架/库 |
|------|------|---------|
| Studio | Dart 3.8+ | Flutter, Material 3, flutter_bloc, go_router, freezed |
| CLI | Rust 2021 | clap, serde, reqwest, quanttide-agent |
| Provider | Go 1.23 | net/http (ServeMux), slog |
| 基础设施 | Terraform | alicloud OSS + DNS |
| CI/CD | GitHub Actions | Flutter Web → Aliyun OSS |

### 3. 数据流

```
Studio: JSON Fixture → DataLoader → BLoC → UI
CLI:    CLI 参数 → Rust 逻辑 → 本地文件 / Provider API
Provider: HTTP 请求 → Store 接口（file/s3）→ JSON 响应
```

### 4. 架构决策 (ADR)

| ADR | 方案 | 理由 |
|-----|------|------|
| 双域模型 | qt-前缀业务域 / 无前缀职能域 | 跨 CLI 模块和 Studio 包一致应用 |
| 数据驱动导航 | JSON fixture 定义导航结构 | 添加工作区零 Dart 代码变更 |
| Fixture 优先 | DataLoader.inject() 注入测试/开发数据 | 后端就绪后替换数据源层即可 |
| Store 接口抽象 | FileStore（现）/ S3（预留） | 本地 JSON 开发，生产对象存储 |
| | 实验→工程管道 | Python 原型 → Rust 稳定 → 文档 |
