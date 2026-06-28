# 产品蓝图 · 量潮 DevOps 云 (QtCloud DevOps)

> 核心问题：做的是不是用户要的？体验是否到位？技术对不对？

---

## 〇、业务视角

### 1. 业务背景

- **所属领域**：DevOps / 研发基础设施
- **解决的核心问题**：量潮采用多仓库 + 子模块组织代码，组件间的同步和发布缺乏自动化工具，依赖人工操作，易出错
- **业务方**：所有使用量潮领域仓库的研发团队

### 2. 业务价值

- **自动化**：子模块同步和版本发布从人工操作变为一条命令
- **规范固化**：发布流程（CHANGELOG → tag → Release）编码为可执行命令，减少人为遗漏
- **统一入口**：一个 CLI 工具覆盖所有领域仓库的运维操作

### 3. 成功指标

| 指标 | 衡量方式 |
|------|---------|
| 采用率 | 量潮团队使用 `code sync` 和 `release publish` 的比例 |
| 发布效率 | 一次发布从操作到完成的时间 |

---

## 一、产品视角

### 1. 产品定位

- **产品名称**：QtCloud DevOps — DevOps CLI 工具
- **核心价值主张**：封装量潮发布规范为可执行命令，自动化子模块同步和版本发布
- **目标用户**：量潮研发团队

### 2. 功能域

| 模块 | 职责 | 优先级 |
|------|------|--------|
| code status | 扫描子模块同步状态 | P0 |
| code sync | 自动同步子模块 | P0 |
| release publish | 发布流水线（版本更新 → CHANGELOG → tag → Release） | P0 |
| release status | 按 scope 分组展示发布状态 | P0 |

### 3. 用户流程

```
1. 研发进入领域仓库 → code status 查看各组件状态
2. 发现有组件待同步 → code sync 一键同步
3. 准备发布 → release status 查看各 scope 发布状态
4. 选择 scope 执行 → release publish --version cli/v0.6.1 --yes
5. 工具自动更新版本号 → 校验 CHANGELOG → 创建 tag → 推送 → 创建 Release
```

### 4. 设计决策

| 决策 | 理由 | 舍弃 |
|------|------|------|
| Rust 实现 | 性能好、跨平台分发方便 | 团队学习成本 |
| git2 (libgit2) | 不依赖系统 git，行为一致 | 体积较大 |
| 双目标分发 (CLI + Python) | Rust 核心 + PyO3 绑定，覆盖原生和 Python 生态 | 维护两种构建配置 |

---

## 二、设计视角

### 1. 布局结构

CLI 工具，两级子命令：`code`（组件管理）+ `release`（发布管理）。scope 配置通过 `.quanttide/devops/contract.yaml` 定义 scope→子目录映射。

### 2. 核心组件

| 组件 | 职责 |
|------|------|
| code/status | 扫描并报告子模块同步状态 |
| code/sync | 编排子模块同步流程 |
| release/publish | 编排发布流水线 |
| git/submodule | Git 底层操作封装（git2） |

### 3. 交互

- 命令行参数驱动，输出结构化状态信息
- `--dry-run` 预览模式
- 发布流程有确认提示，支持 `--yes` 跳过

---

## 三、技术视角

### 1. 架构概览

三层分离：

```
CLI 入口 (clap)
    ↓
code/（业务层）  release/（发布子领域）
    ↓
git/（Git 底层，libgit2）
```

### 2. 技术栈

| 层 | 技术 |
|----|------|
| 语言 | Rust 2021 |
| CLI 框架 | clap v4 (derive) |
| Git | git2 v0.19 (libgit2 vendored) |
| LLM | quanttide-agent |
| Python 绑定 | PyO3 v0.23 |
| CI/CD | GitHub Actions |
| 分发 | crates.io + PyPI + GitHub Releases |

### 3. 数据流

```
用户命令 → clap 解析 → 业务层处理 → git2 操作 → 结果输出
                        ↓
                   LLM CHANGELOG 生成（release publish 中）
```

### 4. 架构决策 (ADR)

| ADR | 方案 | 理由 |
|-----|------|------|
| 三层分离 | code(git)/release/git 分层 | 业务逻辑与 git 操作解耦 |
| 幂等操作 | tag/Release 已存在则跳过 | 支持重试，安全 |
| 分步回滚 | 失败时自动清理已创建资源 | 避免残留 tag 或空 Release |
