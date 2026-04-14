# AIRI + Gemma 4 桌面宠物部署指南

基于 [Project AIRI](https://github.com/moeru-ai/airi) 搭建本地 AI 桌面宠物，使用 Google Gemma 4 作为大脑，完全本地运行，无需付费 API。

## 效果展示

- 桌面宠物角色（Live2D / VRM 模型）
- 文字对话（Gemma 4 驱动）
- 本地语音合成（Kokoro TTS）
- 支持 Windows / macOS

## 前置要求

| 项目 | 要求 |
|------|------|
| 操作系统 | Windows 10/11 或 macOS |
| Node.js | >= 18 |
| Git | 已安装 |
| 显卡 | NVIDIA（推荐 8GB 显存以上） |
| 磁盘空间 | 项目约 2GB + 模型约 3-8GB |

## 快速开始

### 1. 安装 pnpm

```bash
npm install -g pnpm
```

国内用户建议切换镜像：

```bash
pnpm config set registry https://registry.npmmirror.com
```

### 2. 克隆并安装项目

```bash
git clone https://github.com/Iroha-P/airi.git
cd airi
pnpm install
```

> **已知问题：** `canvas` 包可能编译失败（需要 Visual Studio C++ 工具），该包仅 Minecraft 服务使用。可跳过：
> ```bash
> pnpm install --filter "!@proj-airi/minecraft"
> ```

### 3. 安装 Ollama

1. 访问 https://ollama.com/download 下载安装
2. 安装完成后重新打开终端（环境变量需要刷新）
3. 验证：`ollama --version`

**修改模型存储路径（可选）：**

默认存储在 C 盘，如需更改：

- Windows：添加系统环境变量 `OLLAMA_MODELS`，值为目标路径（如 `F:\ollama\models`）
- 必须在拉取模型之前设置

### 4. 下载 Gemma 4 模型

根据你的显卡选择合适的模型：

| 模型 | 显存需求 | 说明 |
|------|----------|------|
| `gemma4:e4b` | ~3-4 GB | **推荐**，8GB 显卡流畅运行 |
| `gemma4:e2b` | ~2 GB | 轻量版，显存极小的设备 |
| `gemma4` (26B MoE) | ~10-14 GB | 效果最好，需要 12GB+ 显卡 |
| `gemma4:31b` | ~16 GB+ | 需要高端显卡 |

```bash
# 推荐（8GB 显卡最佳选择）
ollama pull gemma4:e4b
```

测试模型：

```bash
ollama run gemma4:e4b
# 输入文字测试，/bye 退出
```

### 5. 启动桌面宠物

```bash
pnpm dev:tamagotchi
```

## 配置 AIRI

### 首次启动

启动后弹出欢迎页面，点击右边的 **"配置您自己的 AI 服务来源"**（不需要登录）。

### 配置 LLM 大脑（意识模块）

进入 **设置 -> 机体模块 -> 意识**：

1. 选择 **Ollama** 作为提供商
2. API 地址：`http://localhost:11434/v1/`
3. 模型：`gemma4:e4b`
4. API Key：留空或随意填写

### 配置语音合成（发声模块）

进入 **设置 -> 机体模块 -> 发声**：

1. 选择 **Kokoro TTS（本地）**
2. 模型选择 **FP32 (WebGPU)**（GPU 加速，速度最快）
3. 如果不支持 WebGPU，选 **FP32 (WASM)**

### 配置语音识别（听觉模块，可选）

进入 **设置 -> 机体模块 -> 听觉**：

- 目前无免费本地 STT 内置
- 可跳过，先用键盘文字聊天

### 打开聊天窗口

聊天窗口是独立浮窗，不在主窗口中：

1. 鼠标移到角色模型上，出现控制岛按钮
2. 点击 **聊天气泡图标** 打开聊天窗口
3. 在输入框中打字，Enter 发送

## 常见问题

### 端口冲突 (EADDRINUSE 6121)

之前的进程没有完全退出：

```bash
# Windows
taskkill /F /IM electron.exe
# 然后重新启动
pnpm dev:tamagotchi
```

### Ollama 命令无法识别

安装后需要**重新打开终端**，或使用完整路径：

```powershell
& "$env:LOCALAPPDATA\Programs\Ollama\ollama.exe" --version
```

### pnpm install 下载慢或超时

切换国内镜像后重试：

```bash
pnpm config set registry https://registry.npmmirror.com
pnpm install
```

### 桌面宠物无法对话

1. 确认 Ollama 正在运行：`curl http://localhost:11434`
2. 确认模型已下载：`ollama list`
3. 检查 AIRI 设置中 API 地址是否正确（末尾需要 `/v1/`）

## 常用命令

```bash
# 启动桌面宠物
pnpm dev:tamagotchi

# 启动网页版
pnpm dev

# 查看已下载的模型
ollama list

# 删除模型
ollama rm <模型名>

# 更新模型
ollama pull <模型名>
```

## 技术栈

- **AIRI**：基于 Vue.js + Electron 的桌面宠物框架
- **Gemma 4**：Google 开源大语言模型（Apache 2.0 许可）
- **Ollama**：本地模型运行工具，提供 OpenAI 兼容 API
- **Kokoro TTS**：本地语音合成
- **xsai**：AIRI 的 LLM 接入层，兼容 OpenAI API 格式

## 致谢

- [Project AIRI (moeru-ai/airi)](https://github.com/moeru-ai/airi) - 原始项目
- [Ollama](https://ollama.com) - 本地模型运行
- [Google Gemma](https://ai.google.dev/gemma) - 开源大语言模型
