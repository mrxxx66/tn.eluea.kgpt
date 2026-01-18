# KGPT 项目深度分析文档

## 1. 项目概述

### 1.1 核心定位
`KGPT` 是一个 **LSPosed 框架增强插件**，而非独立输入法应用。其核心价值在于：
- 🔌 **输入法功能增强**：为现有输入法（如 Gboard、AOSP 键盘）添加 AI 能力
- 🧠 **多模型支持**：集成 Gemini 1.5/2.0 等主流模型
- 🎨 **现代化交互**：Material You 动态取色 + 多主题支持（Amoled/Dark/Light）

### 1.2 关键能力矩阵
| 功能类别       | 具体能力                                                                 |
|----------------|--------------------------------------------------------------------------|
| **AI 文本处理** | 生成/优化/翻译/摘要/正式化等 10+ 种文本动作                              |
| **搜索增强**    | 内置 10+ 搜索引擎 + Perplexity AI 深度集成                               |
| **系统集成**    | 通过 `xposed_scope.json` 实现 LSPosed 框架级增强（需 Magisk/LSPosed 环境）|
| **数据管理**    | 完整备份还原系统（覆盖设置、密钥、主题）                                 |

### 1.3 核心修正说明
> **重要澄清**：KGPT 本身 **不是输入法**，而是 **输入法功能增强插件**
> - ✅ **必须依赖现有输入法**：如 `com.android.inputmethod.latin` (AOSP 键盘)
> - ✅ **不提供输入法服务**：不会出现在系统设置 → 语言与输入法 → 虚拟键盘列表
> - ✅ **纯粹增强功能**：在现有输入法界面添加 AI 操作入口

## 2. 技术架构全景

### 2.1 核心技术栈
```gradle
// build.gradle 配置亮点
android {
    compileSdk 34
    buildToolsVersion "34.0.0"
    // namespace 'tn.eluea.kgpt.xposed' // LSPosed 模块命名空间
}
kotlinOptions {
    jvmTarget = "11"
}
// 依赖 LSPosed API
provided 'de.robv.android.xposed:api:82' 
// 依赖版本锁定
implementation 'org.jetbrains.kotlin:kotlin-stdlib:1.8.20'
implementation 'androidx.core:core-ktx:1.10.1'
```

### 2.2 分层架构设计
```
app
├── src/main/java/tn/eluea/kgpt/xposed → LSPosed 模块核心逻辑
├── src/main/res                 → 增强功能资源
│   ├── layout/                  → 浮动窗口布局（77+ 个）
│   └── xml/                     → LSPosed 配置
└── assets/xposed_scope.json     → LSPosed 模块作用域声明
```

## 3. 关键实现细节

### 3.1 LSPosed 集成机制
- **核心文件**：`assets/xposed_scope.json`
- **作用范围**：声明需要 Hook 的输入法包名
- **注入点**：在输入法界面添加浮动 AI 按钮（Hook `InputMethodService`）
- **技术实现**：
  ```json
  {
    "scope": [
      "com.android.inputmethod.latin", // AOSP 键盘
      "com.google.android.inputmethod"  // Gboard
    ]
  }
  ```
- **技术本质**：利用 LSPosed 框架的 **被动增强** 机制，直接注入到目标输入法进程中

## 4. 开发运维规范

### 4.1 构建指令
```bash
# 构建 LSPosed 模块
./gradlew assembleDebug

# 安装模块
adb install app/build/outputs/apk/debug/app-debug.apk
```

### 4.2 目录维护指南
| 目录路径                     | 维护重点                          |
|------------------------------|-----------------------------------|
| `java/tn/eluea/kgpt/xposed/` | LSPosed 模块入口点 (`handleLoadPackage`)|
| `res/layout/`                | 浮动窗口交互组件需保持响应式设计  |
| `res/xml/`                   | LSPosed 配置文件 (`xposed_init`)   |

> **文档更新记录**
> - 2026-01-18：基于 v1.0 架构创建初始分析文档
> - 2026-01-18：修正 LSPosed 集成机制描述偏差
> - 2026-01-18：彻底修正项目定位，明确为 LSPosed 增强插件