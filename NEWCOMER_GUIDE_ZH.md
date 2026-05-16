# QMUI_iOS 新人快速上手指南

## 1. 代码库整体结构

仓库核心分为三个层次：

- **`QMUIKit/`（框架源码）**：真正发布给业务使用的 UI 组件和基础能力。
- **`QMUIKitDemo/`（演示工程）**：每个组件/能力的可运行示例，学习入口首选。
- **`QMUIConfigurationTemplate/`（配置模板）**：主题、全局样式、统一行为的默认配置。

你可以把它理解为：

- `QMUIKit` = “能力实现”
- `QMUIConfigurationTemplate` = “能力默认参数”
- `QMUIKitDemo` = “能力使用样例”

## 2. `QMUIKit/` 里最重要的模块

### 2.1 UIKitExtensions（分类扩展层）

路径：`QMUIKit/UIKitExtensions/`

这是仓库里文件数最多、影响面最广的一层，主要是对系统 UIKit/Foundation 类做 category 扩展，例如：

- `UIView+QMUI`
- `UIViewController+QMUI`
- `UIScrollView+QMUI`
- `UIColor+QMUI`
- `CALayer+QMUI`

新人需要重点理解：

1. 这里大量使用 runtime / method swizzling。
2. 很多“看起来是系统行为”的变化，其实是这些扩展注入的。
3. 排查线上 UI 行为异常时，优先检查对应 `+QMUI` 分类。

### 2.2 QMUICore（基础设施层）

路径：`QMUIKit/QMUICore/`

职责包括：

- 宏定义、断言、日志与调试辅助
- 运行时工具、弱引用容器等通用基础设施
- 与全局配置联动的底层支撑

新人需要重点理解：

- 常用宏/工具函数来源
- 初始化时机（比如全局配置生效链路）

### 2.3 Components（组件层）

路径：`QMUIKit/QMUIComponents/`

包含常见业务 UI 组件，例如弹窗、文本输入增强、导航辅助等（具体按目录拆分）。

阅读建议：

- 先在 Demo 找到组件使用页，再反查到这里看实现。
- 每看一个组件，建立“对外 API ↔ 内部实现 ↔ 配置项”的三段映射。

## 3. 新人必须理解的关键机制

1. **全局配置驱动（QMUICMI）**
   - 组件很多默认行为来自配置模板，而不是写死在组件中。
2. **分类优先于子类**
   - QMUI 很多增强通过 category 完成，不一定靠继承链。
3. **兼容性处理很多**
   - 大量代码为 iOS 差异行为兜底，改逻辑时要特别注意系统版本分支。
4. **Demo 是“活文档”**
   - 文档不一定覆盖所有细节，但 Demo 基本能看到真实用法和边界场景。

## 4. 推荐学习路径（2~3 周）

### 第 1 周：先会用

- 跑通 `QMUIKitDemo`，理解页面组织。
- 每天挑 1~2 个常用组件：
  - 看 Demo 用法
  - 看头文件 API
  - 断点跟 1 次核心调用链

### 第 2 周：理解底层

- 专项阅读 `UIKitExtensions`：
  - 重点看 `UIView/UIViewController/UIScrollView` 三大类扩展。
- 整理“常见 UI 问题 → 对应扩展入口文件”的速查表。

### 第 3 周：开始贡献

- 从低风险任务入手：
  - 文案/注释优化
  - Demo 示例补充
  - 小缺陷修复（有明确复现步骤）
- 每次改动都补一个可复现 Demo 场景，保证评审和回归效率。

## 5. 实战建议（减少踩坑）

- **先搜再改**：改某个控件行为前，先全局搜索 `+QMUI` 相关分类是否已有处理。
- **优先改配置，不急着改组件实现**：很多需求可通过配置落地。
- **保持变更最小化**：尤其在 category / swizzle 代码里，避免引入连锁副作用。
- **写“验证步骤”**：提交时写清楚“哪个 Demo 页、怎么操作、预期现象是什么”。

## 6. 给新人的一个检查清单

在你提交第一个 PR 前，至少完成：

- [ ] 能说清 `QMUIKit`、`QMUIConfigurationTemplate`、`QMUIKitDemo` 各自职责。
- [ ] 能定位一个组件从 Demo 到实现的完整路径。
- [ ] 能找到并解释一个 `UIKitExtensions` 分类对系统行为的影响。
- [ ] 能独立添加一个 Demo 示例并说明其验证价值。

---

如果你是带教同学，可把这份文档当作 onboarding checklist：每周按“会用 → 懂原理 → 能贡献”推进，会比直接啃源码效率更高。
