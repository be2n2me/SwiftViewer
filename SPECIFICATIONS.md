# Swift Photos 需求规格

## 功能需求

### 主要功能

- 具有按文件夹逐张显示图像的功能(must)
  - 可将整个窗口边框用作图像显示区域(should)
  - 未显示图像的区域应显示图像的模糊效果(nice to have)
  - 目标图像文件为 jpg、heic、gif(must)
  - gif 需支持动画 GIF(should)
- 可实现幻灯片放映(must)
  - 幻灯片切换时间可设置为 1,2,3,5,10,20,30,60,120,300 左右的间隔(must)
  - 可通过 Repeat 功能开关「到达图像末尾时返回开头」(should)
- 可通过带有「后退/前进」与「幻灯片播放/停止」切换按钮的小型控制器操作图像(must)
  - 控制器应在模糊背后图像的同时保持半透明(nice to have)
  - 无鼠标或键盘操作时应自动隐藏(should)
  - 带有进度条,点击进度条可显示该位置的图像(nice to have)
- 可通过左右上下方向键输入前后浏览图像(must)
- 可通过 F 键在全屏与标准窗口之间切换(must)
- 可按升序、降序对图像显示顺序进行排序(must)
  - 按文件名字母顺序
  - 按文件创建日期顺序
  - 按文件大小顺序
  - 随机(每次指定文件夹时随机种子都会改变)
- 具有可使用上述功能的 Window Menu(must)
- 可通过「⌘,」或 Window Menu 打开设置窗口,对上述内容进行设置(must)
  - 设置应动态生效(should)

### UI/UX 相关详细功能

- 用户可选择图像缩放方式(nice to have)

  - Fit(保持宽高比整体显示)(must)

  - Fill(填满画面,可裁剪)

  - Actual Size(实际大小显示)

- 缩放功能(nice to have)
  - 未来可实现的即可
    - 支持捏合手势
    - 缩放级别范围(10%-1000%)

### 错误处理

- 跳过损坏的图像文件
- 对无访问权限的文件夹请求权限
- 也支持网络驱动器上的图像
- 不显示图像数量为 0 的文件夹

### 数据持久化

- 设置的保存位置:保存到 UserDefault(即 UserDefaults)
- 记住最后查看的图像位置(随机模式下不保留)
- 收藏/评分功能未来可能实现
- 浏览历史可保留 10 至 100 条(可通过设置更改)

### 安全需求

- 启用 App Sandbox
- 所需 Entitlements:
  □ com.apple.security.files.user-selected.read-only
  □ com.apple.security.files.downloads.read-write
  □ com.apple.security.files.bookmarks.app-scope
- 计划在 Mac App Store 发布

### 国际化/无障碍

- 支持语言:日语和英语,规格上应支持未来扩展到西班牙语、法语、中文等
- 不需要 VoiceOver 支持
- 必须支持仅用键盘完成全部操作
- 支持高对比度模式

## 功能要求(未来需求)

- 支持更多图像文件
  - 支持 RAW 文件
  - 显示 EXIF 信息(可开关)
  - 可通过插件转换并显示 Mac 原生不支持的图像格式
  - 需预先设想可插拔的 Interface
- 支持 Transition(过渡)功能
  - 图像切换时可扩展使用特效
  - 特效功能可通过插件增加
  - 需预先设想可插拔的 Interface 与规范
-

## 非功能需求

- 性能要求
  - 具备显示 10 万张以上图像的能力
    - 假设的平均图像大小低于 5MB
      - 但即使是最大约 50MB 的文件也应具备显示能力
    - 内存使用量上限目标
      - 可由用户设置
      - 从设置窗口设置
      - 最好能根据文件数量与可用内存情况自动分配一定程度的缓存大小
      - 根据需要可考虑使用具备缓存功能的库
      - 前提是动态控制缓存大小运行
    - 预加载策略
      - 用户可设置 10 张至 1,000 张
      - 基本上根据内存情况自动动态设置
      - 异步加载
    - 不需要生成缩略图
    - 不加载全部图像数据,而是拥有随分配的内存与图像数量变化的缓存功能,实现高速图像显示。缓存命中时约 10ms 内显示

## 技术需求

- 遵循 Xcode16/Swift6/SwiftUI 的规范
- 为 Mac 的 arm 应用程序
- 支持 Mac version 14+
- 采用 MVVM 或与之相当的结构
- 一个功能或一个职责分解点应在一个文件内完成
- 具备根据设置输出 debug log 的功能
- 功能添加或修改对其它文件的影响应为最小化的结构
- 可采用用于抑制影响的抽象层或设计模式
- 单个文件的大小应为人类可通读的行数

## 项目需求

- 应用名: Swift Vierwer(原文如此)
- Bundle Identifier: be2n2me.SwiftViewer
- 开发团队设置: be2n2me
- Code Signing 方式: Development
- 最小部署目标 macOS 15.0 及以上
- 遵循测试驱动规范
- 以 step by step 方式,按「一个文件」或「一个功能」、「一个 bug 修复」为单位推进工作
  - 选择无编译构建错误、可执行的最小单位
- 使用 git 和 github 管理代码
  - 每个单位创建 git 的 branch 并 commit,push 到 GitHub 并提交 Pull Request
  - Pull Request 的内容由质量管理负责人确认后进行 merge
  - 从已 Merge 的 branch 开始下一项工作
  - git 的 branch 策略遵循 GitHub Flow
### CI/CD

- GitHub 仓库: https://github.com/be2n2me/SwiftViewer
- CI/CD 工具: Github Actions
- 代码覆盖率阈值: 75% 以上
- 使用 SwiftLint

```yaml
# .swiftlint.yml - 面向图像浏览应用的推荐设置

# 基本规则
included:
  - Sources
  - Tests

excluded:
  - .build
  - DerivedData
  - ${PODS_ROOT}

# 应启用的规则
opt_in_rules:
  # 代码质量
  - empty_count
  - empty_string
  - first_where
  - sorted_first_last
  - contains_over_filter_count
  - contains_over_filter_is_empty
  - flatmap_over_map_reduce

  # 可读性提升
  - multiline_parameters
  - multiline_function_chains
  - vertical_parameter_alignment_on_call
  - closure_end_indentation

  # SwiftUI 特有
  - multiple_closures_with_trailing_closure
  - modifier_order # SwiftUI 的 modifier 顺序

  # 安全性
  - force_unwrapping # 警告使用 !
  - implicitly_unwrapped_optional
  - weak_delegate

  # 测试相关
  - quick_discouraged_call
  - single_test_class

# 自定义规则
custom_rules:
  no_print:
    name: "禁止使用 Print 语句"
    regex: '\bprint\('
    message: "Use Logger instead of print()"
    severity: warning

  todo_fixme:
    name: "TODO/FIXME 必须带工单编号"
    regex: '(//|#|\\*)\s*(TODO|FIXME)(?!.*#\d+)'
    message: "TODO 和 FIXME 请包含工单编号"

# 配置值
line_length:
  warning: 120
  error: 200
  ignores_comments: true

file_length:
  warning: 400
  error: 600

type_body_length:
  warning: 300
  error: 500

function_body_length:
  warning: 40
  error: 60

cyclomatic_complexity:
  warning: 10
  error: 20
```

#### GitHub Branch Protection Rules

main 分支的保护设置:

✅ 必须设置:

- Require a pull request before merging

  - Required approvals: 1 以上
  - Dismiss stale PR approvals when new commits

- Require status checks to pass

  - Required checks:
    - build-and-test
    - swiftlint
    - test-coverage (80%以上)
  - Require branches to be up to date

- Require conversation resolution
- Require linear history(强制 rebase)

⭐ 推荐设置:

- Include administrators(管理员也不例外)
- Restrict who can push(仅限特定成员)

🔧 提升开发效率的设置:

- Allow auto-merge(CI 通过后自动合并)
- Automatically delete head branches

## 测试驱动规范

我将搜索 Swift/SwiftUI 测试与 TDD 方法在 macOS 应用开发中的最新最佳实践。

基于我的研究,以下是简洁提示词,用于在 Swift/SwiftUI macOS 应用开发中实施 TDD 最佳实践:

请按照以下要求,以 TDD 方式创建 Swift/SwiftUI macOS 应用:

### 架构

- MVVM 与基于协议的依赖注入

- ViewModel 使用 @Observable(Swift 5.9+)或 ObservableObject

- 数据层采用 Repository 模式

- 为所有依赖(网络、持久化、工具类)分离协议

### 测试结构

- 测试组织与源码镜像: Features/FeatureName/Tests/

- 使用支持 async/await 的 XCTest

- 遵循 AAA 模式(Arrange-Act-Assert)

- 测试命名: test_methodName_expectedBehavior_whenCondition()

### TDD 工作流

1. 先写失败测试
2. 实现最小化代码使其通过
3. 自信地重构
4. 每次 commit 都应包含测试 + 实现

### 应包含的关键组件

- 基于协议的 NetworkService,使用 URLSession 实现

- 用于测试的 Mock/Stub 实现

- 带 @Published 属性的 ViewModel

- 带 async throws 方法的 Repository

- 使用自定义领域错误的错误处理

- 确定性的时间/调度器抽象

### 测试要求

- ViewModel 的单元测试(业务逻辑)

- Repository + Network 的集成测试

- Combine 使用 TestScheduler,async 使用 TestClock

- 不使用 sleep(),使用 XCTExpectation 或 async/await

- Mock 外部依赖,为协议提供测试替身

- 业务逻辑覆盖率达到 80% 以上

### 示例结构

```swift
// Protocol
protocol UserRepository {
  func fetchUser(id: String) async throws -> User
}

// ViewModel
@Observable
final class UserViewModel {
  private let repository: UserRepository
  @Published var user: User?
  @Published var isLoading = false

  init(repository: UserRepository) {
    self.repository = repository
  }

  func loadUser(id: String) async {
    isLoading = true
    do {
      user = try await repository.fetchUser(id: id)
    } catch {
      // handle error
    }
    isLoading = false
  }
}

// Test
final class UserViewModelTests: XCTestCase {
  func test_loadUser_setsUser_whenRepositorySucceeds() async {
    // Arrange
    let mockUser = User(id: "1", name: "Test")
    let mockRepository = MockUserRepository(userToReturn: mockUser)
    let sut = UserViewModel(repository: mockRepository)

    // Act
    await sut.loadUser(id: "1")
    // Assert
    XCTAssertEqual(sut.user, mockUser)
    XCTAssertFalse(sut.isLoading)
  }
}
```

#### **SwiftUI View 测试**

- 保持 View 轻量,改为测试 ViewModel
- 需要时使用 ViewInspector 进行 SwiftUI View 测试
- 集成测试使用环境注入
- 关键 UI 组件使用快照测试

#### **最佳实践**

- 优先每个测试一个断言
- 测试行为,而非实现
- 使用工厂方法生成测试数据
- 隔离测试(无共享状态)
- 快速反馈循环(每个单元测试 <100ms)
- CI 运行: Unit → Integration → UI(仅冒烟)

此提示词提供了具体、可执行的指示,用于在 Swift/SwiftUI macOS 应用中实施 TDD,同时融入了研究得到的最新最佳实践。
