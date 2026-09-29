# QODER.md

本文件为 Qoder 在此仓库中处理代码时提供指引。

## 项目概述

SwiftViewer 是使用 Swift 6 与 SwiftUI 构建的 macOS 照片查看器应用,目标平台为 macOS 15.0+。应用提供完整的图像查看能力,包括幻灯片功能、文件夹浏览与性能优化的图像缓存。

## 构建与开发命令

### 构建项目

```bash
# Debug 构建
xcodebuild -project SwiftViewer.xcodeproj -scheme SwiftViewer -configuration Debug build

# Release 构建
xcodebuild -project SwiftViewer.xcodeproj -scheme SwiftViewer -configuration Release build

# 清理构建目录
xcodebuild -project SwiftViewer.xcodeproj -scheme SwiftViewer clean
```

### 运行测试

```bash
# 运行全部测试
xcodebuild -project SwiftViewer.xcodeproj -scheme SwiftViewer -sdk macosx test

# 运行指定测试 target
xcodebuild -project SwiftViewer.xcodeproj -scheme SwiftViewer -sdk macosx -only-testing:SwiftViewerTests test
```

### SwiftLint

```bash
# 运行 SwiftLint
swiftlint

# 自动修复违规项
swiftlint --fix
```

## 架构

### MVVM 模式

- **Views**: `SwiftViewer/Views/` 中的 SwiftUI 视图
- **ViewModels**: 管理视图状态与业务逻辑的可观察对象
- **Models**: 表示领域对象的数据结构
- **Repositories**: 基于协议抽象的数据访问层
- **Services**: 可复用的业务逻辑组件

### 依赖注入

- 基于协议的依赖注入,便于测试
- 依赖通过初始化器注入
- 提供 Mock 实现用于测试

### 关键组件(待实现)

- **ImageLoader**: 支持 NSImage/CGImage 的异步图像加载
- **ImageCache**: 内存上限可配置的 LRU 缓存
- **FileManager 扩展**: 目录浏览与文件过滤
- **SettingsManager**: 应用偏好的 UserDefaults 封装
- **SlideShowController**: 基于计时器的图像轮换逻辑

## 测试驱动开发(TDD)工作流

1. **先写失败测试** 到相应测试文件
2. **实现最小化代码** 使测试通过
3. **重构** 同时保持测试绿色
4. **提交** 测试与实现一起提交

### 测试组织

- 单元测试: `SwiftViewerTests/`
- UI 测试: `SwiftViewerUITests/`
- 测试命名: `test_methodName_expectedBehavior_whenCondition()`

## Git 工作流(GitHub Flow)

### 创建功能

```bash
# 创建功能分支
git checkout -b feature/feature-name

# 实现完成后
git add .
git commit -m "feat: implement feature description"
git push -u origin feature/feature-name
```

### Pull Request 流程

1. 创建带有详细描述的 PR
2. 确保所有测试通过
3. 代码覆盖率必须 75%+
4. 合并前请求审查

## 记忆与记录

项目开发过程中的架构决策、实现计划、测试结果、性能指标、bug 修复与解决方案,可使用 Qoder 内置的记忆系统(SearchMemory / UpdateMemory)进行记录,将项目约定、架构决策与经验教训沉淀为长期记忆。

## 关键需求

### 支持的图像格式

- JPEG (.jpg, .jpeg)
- HEIC (.heic)
- GIF (.gif) 支持动画
- 未来: 通过插件系统支持 RAW 格式

### 性能目标

- 支持文件夹内 10,000+ 图像
- 缓存响应时间: 缓存命中 <10ms
- 内存使用: 用户可配置上限
- 预加载: 10-1000 张(可配置)

### UI/UX 功能

- 全屏模式(F 键切换)
- 幻灯片,间隔可配置(1-300 秒)
- 键盘导航(方向键)
- 排序选项(名称、日期、大小、随机)
- 可点击导航的进度条
- 非图像区域的模糊效果

### 所需 Entitlements

- `com.apple.security.app-sandbox`
- `com.apple.security.files.user-selected.read-only`
- `com.apple.security.files.bookmarks.app-scope`

## 开发指南

### 文件组织

- 每个文件一个职责
- 协议定义放在独立文件
- 扩展按逻辑分组
- 测试文件镜像源码结构

### 代码风格

- 遵循 Swift API Design Guidelines
- 异步操作使用 async/await
- 适当位置优先使用值类型
- 为公共 API 编写文档

### 错误处理

- 使用自定义错误类型
- 优雅处理损坏图像
- 访问受保护文件夹时请求权限
- 恰当记录错误(不使用 print 语句)

## 调试

### 启用调试日志

将 UserDefaults 键 `debugLoggingEnabled` 设为 true

### 常见问题

- **沙盒违规**: 检查 entitlements
- **内存问题**: 检查缓存配置
- **性能**: 使用 Instruments 分析

## Qoder 行为规则

### 关键: 防止擅自实现的规则

#### 必须使用计划模式

**以下类型的指令必须使用计划模式:**

- 「提示方法」/「提案」/「告知步骤」
- 「怎么办」/「方案」/「策略」
- 任何对提案、方法或方案的请求

**计划模式流程:**

1. 彻底分析请求
2. 给出详细计划
3. 等待用户明确批准
4. 只有在批准后才进入实现

#### 绝对禁止事项

**未经用户明确指示,绝不:**

- 在给出方法后自动实现
- 在计划阶段修改文件
- 「顺手」优化
- 添加未被明确请求的功能

#### 需要两阶段确认

```
阶段 1: 计划(必须使用计划模式)
- 问题分析
- 方案提案
- 实现步骤
- 征求用户批准

阶段 2: 实现(仅在批准后)
- 严格执行已批准的计划
- 不偏离已批准的范围
- 不做额外的「改进」
```

#### 紧急停止触发词

**检测到以下表述时立即停止一切活动:**

- 「擅自」/「未经允许」/「没有指示」
- 「停下」/「停止」/「等一下」

#### 指令分类

```
提案型(→ 必须使用计划模式):
- 「提示方法」→ 计划阶段
- 「说明步骤」→ 计划阶段
- 「提出方案」→ 计划阶段

实现型(→ 可直接执行):
- 「修好它」→ 直接实现
- 「实现它」→ 直接实现
- 「提交」→ 直接实现
```

#### 验证清单

修改任何文件之前,确认:

- ✓ 用户给出了明确的实现指示?
- ✓ 计划已获批准?
- ✓ 变更在已批准范围内?
- ✓ 没有进行未授权的追加?

**如果任一答案为「否」→ 立即停止**

## SwiftViewer 功能扩展计划 2025

### 实现策略(4 阶段方案)

SwiftViewer 将通过系统的 4 阶段实现计划进行增强,重点是设置驱动的可配置性、插件可扩展性以及 SwiftUI 最佳实践的合规性。

#### 阶段 1: 设置基础(第 1-2 周)
**目标:** 扩展设置系统以支持所有计划的功能

**关键交付物:**
- 为 `SettingsManagerProtocol` 扩展 7 个新配置属性
- 更新 `SettingsView` UI 以支持动态实时配置变更
- 实现菜单勾选标记以提供可视化选择反馈
- 确保设置持久化并即时生效

**新增设置:**
- `autoHideDelay: TimeInterval`(1.0-60.0 秒)
- `animationDurations: [AnimationType: TimeInterval]`
- `blurRadius: Double` 与 `blurOpacity: Double`
- `loggingLevel: LogLevel`
- `slideShowCustomIntervals: [TimeInterval]`
- `windowPositioning: WindowPosition`

#### 阶段 2: 核心 UI 功能(第 3-4 周)
**目标:** 实现主要用户体验改进

**关键功能:**
- 可配置的控制栏自动隐藏(1-60 秒延迟)
- 控制器模糊/不透明度视觉效果系统
- 调试模式下条件性 UI 元素可见性
- 基础窗口定位(始终置顶/置底)

#### 阶段 3: 高级功能(第 5-6 周)
**目标:** 精细控制与自定义能力

**实现重点:**
- 统一的动画时长管理系统(13 处 `.easeInOut`)
- 幻灯片间隔选择: [1,2,3,5,10,20,30,60,120,300,600,1200,1800] 秒
- 窗口菜单扩展: 「移动与调整大小」、「全屏平铺」
- 图像显示标准化(仅 Fit 模式,移除缩放选项)

#### 阶段 4: 插件架构(第 7-8 周及以上)
**目标:** 通过插件系统实现未来的可扩展性

**架构组件:**
- 带插件接口的过渡特效框架
- 安全的插件加载机制
- 内置过渡实现
- 面向第三方扩展的插件 API 规范

### 技术标准
- **平台:** Swift 6、SwiftUI、macOS 15.0+
- **架构:** MVVM + 依赖注入(保持不变)
- **测试:** TDD,覆盖率要求 75%+
- **性能:** 支持 10,000+ 图像的能力
- **合规性:** 完全遵循 Context7 SwiftUI 最佳实践

### 质量关卡
每个阶段要求:
- 所有交付物的功能验证
- 现有功能的回归测试
- 性能基准验证
- 架构合规性审查

这一系统化方案确保 SwiftViewer 演进为高度可定制、可扩展的 macOS 应用,同时保持代码质量与架构完整性。
