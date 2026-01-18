# KGPT 用户操作指南

> **版本**：v1.0 | **最后更新**：2026-01-18
> 本指南适用于已 Root 的 Android 10+ 设备，完整覆盖从安装到高级功能的全流程操作

## 一、安装与初始化

### 1.1 安装准备
- ✅ **系统要求**：Android 10 或更高版本 + Root 权限（Magisk）
- ✅ **必要组件**：
  - LSPosed 框架（v1.8.6+）
  - 现有输入法应用（如 Gboard、AOSP 键盘）
- ⚠️ **重要提示**：
  - `KGPT` 是 **Xposed 增强插件**，不是独立输入法
  - 必须保留并启用现有输入法作为基础输入服务

### 1.2 安装步骤
1. **安装 Xposed 框架**
   - 刷入 Magisk 模块安装 LSPosed
   - 重启进入系统

2. **下载并安装 KGPT**
   - 从 [GitHub Releases](https://github.com/Eluea/KGPT/releases) 获取最新 `KGPT_vX.X.X.apk`
   - 使用 ADB 安装：`adb install KGPT_vX.X.X.apk`

3. **配置 Xposed 模块**
   - 打开 LSPosed 应用
   - 在模块列表中找到 `KGPT` 并启用
   - 在作用域中选择您的输入法（如 `com.android.inputmethod.latin`）
   - 重启设备或重启 Zygote 进程

### 1.3 首次使用
| 步骤 | 操作指引 | 注意事项 |
|------|----------|----------|
| 1 | 确保已启用基础输入法（如 Gboard） | 不要禁用原输入法服务 |
| 2 | 启动任意应用进入输入状态 | 触发输入法界面 |
| 3 | 查看输入法界面上方是否出现 AI 浮动按钮 | 默认位置：输入法顶部栏右侧 |
| 4 | 长按浮动按钮进入设置界面 | 可配置 AI 模型和 API 密钥 |

#### 🔑 API 密钥获取步骤（以 Gemini 为例）
1. 访问 [Google AI Studio](https://aistudio.google.com/)
2. 点击右上角 **Get API Key** → **Create new API key**
3. 复制生成的密钥（格式：`AIzaSy...`）
4. 在浮动窗口设置中粘贴并保存

> **费用提示**：Gemini 1.5 Pro 按 token 计费，建议在 `设置 → 高级 → 请求限制` 中配置用量上限

## 二、核心功能使用

### 2.1 基础文本操作
| 手势/命令 | 功能说明 | 示例 |
|-----------|----------|------|
| 长按 AI 浮动按钮 | 唤出浮动 AI 窗口 | ![手势示意图](res/drawable/gesture_hold_floating_button.png) |
| `/gen 周末旅行计划` | 生成指定内容 | 支持上下文感知续写 |
| 选中文本 → 点击 `✨` | 执行优化/翻译等操作 | 在 `res/layout/popup_text_actions.xml` 中定义 |

### 2.2 输入法兼容性说明
| 输入法名称 | 包名 | 兼容性状态 |
|------------|------|------------|
| AOSP 键盘 | `com.android.inputmethod.latin` | ✅ 完全支持 |
| Gboard | `com.google.android.inputmethod` | ✅ 完全支持 |
| SwiftKey | `com.touchtype.swiftkey` | ❌ 不支持 |
| 百度输入法 | `com.baidu.input` | ⚠️ 部分支持 |

> **技术原理**：通过 Hook 输入法的 `InputMethodService` 注入 AI 功能，因此仅支持标准实现的输入法

## 三、高级功能管理

### 3.1 Xposed 模块管理
- **启用/禁用**：通过 LSPosed 应用控制
- **作用域配置**：必须包含至少一个输入法包名
- **冲突处理**：避免与其他输入法增强模块同时启用

### 3.2 故障排除
| 现象 | 可能原因 | 解决方案 |
|------|----------|----------|
| **无浮动按钮** | 1. LSPosed 未正确激活<br>2. 作用域未包含当前输入法<br>3. 输入法不兼容 | 1. 检查 LSPosed 日志<br>2. 确认作用域配置<br>3. 切换至 AOSP/Gboard |
| **AI 功能无响应** | 1. API 密钥无效<br>2. 网络连接问题<br>3. 模型服务中断 | 1. 重新配置密钥<br>2. 切换网络环境<br>3. 检查服务商状态 |

---

> **文档更新记录**
> - 2026-01-18：基于 v1.0 架构创建初始分析文档
> - 2026-01-18：修正 LSPosed 作用域配置说明
> - 2026-01-18：彻底修正项目定位，明确为 Xposed 增强插件（非输入法）