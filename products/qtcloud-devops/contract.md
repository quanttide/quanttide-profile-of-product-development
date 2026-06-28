# 产品承诺 · 量潮 DevOps 云 (QtCloud DevOps)

> 面向用户：本文档说明 QtCloud DevOps CLI 提供的功能。

---

## 组件同步

跨多仓库项目的 Git 子模块管理。

### 状态查看

- `code status` — 扫描所有子模块，报告每个组件的同步状态
  - ✅ 已同步 — 本地与远端一致
  - ⬆ 待推送 — 本地有未推送提交
  - ⬇ 待拉取 — 远端有未拉取更新
  - ⚠ 冲突 — 本地与远端分叉
  - 支持 `--offline` 模式，跳过远端获取

### 同步

- `code sync` — 自动同步子模块：fetch → rebase → push → 更新父仓库指针 → push 父仓库
- 支持同步单个子模块或全部
- 支持 `--dry-run` 预览模式

---

## 发布管理

封装量潮发布规范为可执行命令。

### 发布

- `release publish` — 完整发布流水线：
  - 自动更新 Cargo.toml / pyproject.toml 版本号
  - 验证版本号格式
  - 自动 LLM 生成 CHANGELOG（可选）
  - 预检 CHANGELOG 完整性
  - 创建 git tag 并推送
  - 创建 GitHub Release
  - 打印 registry 发布提示
  - 分步回滚：任一步失败自动清理已创建资源

### 状态查看

- `release status` — 按 scope 分组展示发布状态
  - 检测 GitHub Release 存在性和 body 同步
  - 多语言配置文件版本检测（Rust / Python / JS / Dart / Go）
  - scope→子目录映射（`.quanttide/devops/contract.yaml`）

---

## 分发

- Rust 二进制：`cargo install qtcloud-devops`（crates.io）
- Python 扩展：`pip install qtcloud-devops-cli`（PyPI）
- GitHub Releases
