# Screen Assistant - 屏幕监控智能助手

基于大模型视觉能力的屏幕监控助手，通过持续记录和分析用户操作，提供智能工作辅助。

## 功能特性

- **屏幕监控**: 定时截屏并使用 AI 视觉模型分析屏幕内容
- **智能跳帧**: 通过感知哈希对比，跳过无变化的画面，节省 Token 消耗
- **错误检测**: 主动检测屏幕上的错误信息并推送提示
- **自然语言查询**: 支持询问"刚才做了什么"、"最近10分钟的操作"等
- **两层存储**: 原始记录 + 5分钟聚合，平衡详细度和存储效率
- **双模型支持**: 支持云端 API (OpenAI/Claude) 和本地 Ollama

## 技术栈

- **前端**: Vue 3 + TypeScript + Naive UI + Pinia
- **后端**: Tauri 2 (Rust)
- **AI**: OpenAI GPT-4 Vision / Claude / Ollama (llava)

## 环境要求

- Node.js 18+
- Rust 1.70+
- 可选: Ollama (用于本地模型)

## 安装步骤

### 1. 克隆仓库

```bash
git clone https://github.com/mypengpengli/screen-assistant.git
cd screen-assistant
```

### 2. 安装依赖

```bash
# 安装前端依赖
npm install

# Rust 依赖会在首次构建时自动安装
```

### 3. 开发运行

```bash
npm run tauri dev
```

### 4. 生产构建

```bash
npm run tauri build
```

## 配置说明

首次运行后，在应用内的"设置"页面进行配置。

### 模型配置

#### 使用云端 API (推荐)

1. 选择 **模型来源**: `API (云端)`
2. 选择 **API 类型**: `OpenAI` / `Claude` / `自定义`
3. 填写 **API 地址**:
   - OpenAI: `https://api.openai.com/v1`
   - Claude: `https://api.anthropic.com`
   - 或使用兼容的第三方 API 地址
4. 填写 **API Key**: 你的 API 密钥
5. 填写 **模型名称**:
   - OpenAI: `gpt-4-vision-preview` 或 `gpt-4o`
   - Claude: `claude-3-opus-20240229`

#### 使用本地 Ollama

1. 先安装并运行 [Ollama](https://ollama.ai/)
2. 拉取视觉模型: `ollama pull llava`
3. 在设置中选择 **模型来源**: `Ollama (本地)`
4. **Ollama 地址**: `http://localhost:11434`
5. **模型名称**: `llava`

### 截屏配置

| 设置项 | 说明 | 默认值 |
|--------|------|--------|
| 启用监控 | 是否开启屏幕监控 | 开启 |
| 截屏间隔 | 每次截屏的间隔时间 | 1000ms |
| 压缩质量 | 截图压缩质量 (10-100) | 80% |
| 跳过无变化 | 画面无变化时跳过识别 | 开启 |
| 变化敏感度 | 相似度阈值 (0.5-0.99) | 0.95 |

**说明**:
- `跳过无变化`: 启用后，当画面相似度超过阈值时跳过 AI 识别，大幅节省 Token
- `变化敏感度`: 数值越高越容易跳过，0.95 表示 95% 相似就跳过

### 存储配置

| 设置项 | 说明 | 默认值 |
|--------|------|--------|
| 保留天数 | 历史数据保留时间 | 7 天 |
| 上下文大小 | 对话时加载的最大字符数 | 10000 字符 |

## 使用方法

### 开始监控

1. 打开应用，进入"对话"页面
2. 点击 **开始监控** 按钮
3. 应用会在后台定时截屏并分析

### 查询历史

在对话框中输入自然语言问题：

- "刚才我在做什么？"
- "最近10分钟的操作"
- "今天用了哪些应用？"
- "有没有遇到什么错误？"

### 错误提醒

当 AI 检测到屏幕上有错误信息时，会自动在对话窗口中推送提示，包括：
- 错误类型
- 错误信息摘要
- 解决建议

### 查看历史

进入"历史"页面，可以按日期查看详细的操作记录时间线。

## 数据存储

数据存储在本地应用数据目录：

```
Windows: %LOCALAPPDATA%\screen-assistant\data\
macOS: ~/Library/Application Support/screen-assistant/data/
Linux: ~/.local/share/screen-assistant/data/
```

目录结构：
```
data/
├── config.json              # 配置文件
└── summaries/
    └── YYYY-MM-DD.json      # 每日记录
```

## 隐私说明

- 所有数据仅存储在本地，不会上传到任何服务器
- 截图不会保存，仅保存 AI 分析后的文字摘要
- API 调用时图片会发送到对应的 AI 服务商

## 常见问题

### Q: 为什么截图分析很慢？

A: 视觉模型分析需要一定时间，建议：
1. 启用"跳过无变化"功能减少不必要的分析
2. 适当增加截屏间隔
3. 使用本地 Ollama 模型减少网络延迟

### Q: Token 消耗太快怎么办？

A:
1. 启用"跳过无变化"功能（默认已启用）
2. 提高"变化敏感度"数值
3. 增加截屏间隔时间
4. 减少"上下文大小"设置

### Q: 如何使用国内 API？

A: 在 API 地址中填写兼容 OpenAI 格式的国内服务商地址，如：
- 智谱 AI: `https://open.bigmodel.cn/api/paas/v4`
- 通义千问: 参考阿里云文档

## License

MIT
