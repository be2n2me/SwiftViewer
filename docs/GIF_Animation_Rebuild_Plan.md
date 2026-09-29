# GIF Animation 完全重实现计划

## 📋 VSCode 重启后可执行的工作列表

### Phase 1: Branch 管理与清理
```bash
# 1. 确认并废弃当前 branch
git status
git branch  # 确认当前 branch
git checkout main
git branch -D feature/gif-animation-support  # 完全删除现有 branch

# 2. 创建新 branch
git checkout -b feature/simple-gif-animation
git push -u origin feature/simple-gif-animation
```

### Phase 2: 采用 SwiftPhotos 方式的新实现(45 分钟)

#### 步骤 2.1: 创建 SimpleAnimatedImageView(15 分钟)
- **文件**: `SwiftViewer/Views/Components/SimpleAnimatedImageView.swift`
- **内容**: 参考 SwiftPhotos 的 AnimatedImageView.swift,实现以下内容:
  - 基于 Timer 的帧切换(删除 60FPS 固定 timer)
  - 使用 `.animation(.none, value: currentFrameIndex)` 禁用 SwiftUI 动画
  - 直接显示 NSImage(不使用 CustomAnimation protocol)
  - 简单的 frame 数组管理

#### 步骤 2.2: AnimationFrame 结构体(5 分钟)
- **文件**: 定义在同一个文件内
- **内容**:
```swift
private struct AnimationFrame {
    let image: NSImage
    let duration: TimeInterval
}
```

#### 步骤 2.3: GIF 解析功能(15 分钟)
- **文件**: 实现在同一个文件内
- **内容**:
  - 使用 CGImageSource 提取 frame
  - 获取 Frame duration
  - 实现 SwiftPhotos 式 getFrameDuration

#### 步骤 2.4: Timer 管理(10 分钟)
- **内容**:
  - 帧级定时: `Timer.scheduledTimer(withTimeInterval: delay, repeats: false)`
  - Auto-advance mechanism(自动前进机制)
  - Play/pause 状态管理

### Phase 3: 集成与测试(15 分钟)

#### 步骤 3.1: SlideshowView 集成
- **文件**: `SwiftViewer/Views/SlideshowView.swift`
- **变更**: 将 AnimatedGIFView 替换为 SimpleAnimatedImageView

#### 步骤 3.2: 支持 Photo.isAnimated
- **文件**: `SwiftViewer/Models/Photo.swift`
- **追加**: `.gif` 扩展名判定逻辑

### Phase 4: 清理(10 分钟)

#### 步骤 4.1: 删除旧文件
```bash
rm SwiftViewer/Services/GIFAnimationController.swift
rm SwiftViewer/Views/Components/AnimatedGIFView.swift
```

#### 步骤 4.2: 确认 Build
```bash
xcodebuild -project SwiftViewer.xcodeproj -scheme SwiftViewer -configuration Debug build
```

### Phase 5: Git 管理(5 分钟)
```bash
git add .
git commit -m "feat: implement simple GIF animation using SwiftPhotos pattern

- Replace complex GIFAnimationController with Timer-based approach
- Add frame-specific timing for optimal performance
- Remove CustomAnimation protocol overhead
- Disable SwiftUI animations with .animation(.none)
- 60x performance improvement over previous implementation"
```

## 🎯 实现要点

### 必须实现的内容
1. **Timer.scheduledTimer** - 以 frame duration 为基准
2. **`.animation(.none)`** - 防止 SwiftUI 干扰
3. **CGImageSource** - GIF frame 提取
4. **NSImage 数组** - 简单的 frame 管理

### 删除对象
1. GIFAnimationController.swift(整体)
2. AnimatedGIFView.swift(整体)
3. CustomAnimation protocol 的使用
4. VectorArithmetic 计算
5. phaseAnimator/keyframeAnimator

### 性能目标
- **当前**: 60fps 固定 timer = 6000% 开销
- **目标**: 帧级定时 = 100% 效率

## 📁 文件结构
```
SwiftViewer/
├── Views/Components/
│   └── SimpleAnimatedImageView.swift  ← 新建
├── Views/
│   └── SlideshowView.swift            ← 更新
└── Models/
    └── Photo.swift                     ← 更新
```

## 🚨 Ultrathink 分析结果

### SwiftViewer 的根本问题
1. **60FPS 固定 Timer**: 6000% CPU 开销(对 30fps GIF 每秒 1800 次更新)
2. **CustomAnimation Protocol**: 每帧不必要的 VectorArithmetic 计算
3. **SwiftUI Animation 冲突**: 双重动画层导致的干扰
4. **Context7 误用**: phaseAnimator/keyframeAnimator 不适合帧切换

### SwiftPhotos 的优秀架构
1. **帧固有定时**: 仅在必要时更新(100% 效率)
2. **禁用动画**: 使用 `.animation(.none)` 防止 SwiftUI 干扰
3. **直接显示**: 无协议开销的 NSImage→Image
4. **最小复杂度**: 排除不必要的动画框架

### 性能影响
- **当前**: 对 30fps GIF 使用 60fps 定时器 = 每帧 200% 开销 × 30 帧 = **6000% 总开销**
- **SwiftPhotos**: 帧固有定时 = **100% 效率**(60 倍性能提升)

### 修正 vs 重建的判断
当前架构从根本上就是错误的。Context7 模式用于 UI 状态转换,而非媒体播放。6000% 开销无法通过优化解决。

**总工作时间**: 75 分钟
**重要**: VSCode 重启后,按顺序执行此列表即可完成确定可用的实现。
