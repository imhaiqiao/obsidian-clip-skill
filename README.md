# Obsidian Clip

使用自然语言一键将网页剪藏到 Obsidian。

## 前置要求

### 必需

| 软件 | 说明 |
|----------|-------|
| **Obsidian** | 本地笔记应用，任意版本均可 |
| **Google Chrome** | 仅支持 Chrome — Safari/Firefox 不支持 |
| **Obsidian Web Clipper**（Chrome 扩展） | [从 Chrome 应用商店安装](https://chrome.google.com/webstore/detail/obsidian-web-clipper/ofdgopchbanhlkickijgiamfgkxinbbm) |

### 推荐

| 插件 | 说明 |
|--------|-------|
| **Local Images Plus**（Obsidian 社区插件） | 自动将远程图片下载到本地，防止链接失效 |

---

## 在 OpenClaw 中安装 Skill

### 通过聊天（ Telegram、微信等）

只需向你的 OpenClaw 机器人发送自然语言消息：

```
Install the obsidian-clip-skill skill from https://github.com/imhaiqiao/obsidian-clip-skill
```

### 安装后，重启网关：

```
openclaw gateway restart
```

---

## 配置依赖

### 第一步 — 安装 Obsidian Web Clipper 扩展

1. 打开 Chrome，访问 [Chrome 应用商店页面](https://chrome.google.com/webstore/detail/obsidian-web-clipper/ofdgopchbanhlkickijgiamfgkxinbbm)
2. 点击"添加到 Chrome"

### 第二步 — 设置剪藏快捷键

1. 点击扩展图标 → 点击右上角 ⚙️ 设置图标
2. 找到**键盘快捷键**设置
3. 将快捷键绑定到 **⌥⇧O**（Option + Shift + O）
   - 这会触发"保存到 Obsidian"，而非通用剪藏操作

### 第三步 — 授权 Obsidian 接收剪藏

1. 首次剪藏时，扩展会提示授权保险库
2. 选择你的 Obsidian 保险库
3. 确认保存到 **Clippings** 文件夹（或你偏好的默认位置）

### 第四步（可选）— 安装 Local Images Plus

1. 打开 Obsidian → 设置 → 社区插件
2. 搜索"Local Images Plus"
3. 安装并启用
4. 剪藏笔记中的远程图片将自动下载到 `Clippings/attachments/`

---

## 使用方法

### 触发短语

当消息包含网址**加上**以下触发词之一时，该 skill 会被激活：

```
save/clip/保存/收藏 https://example.com
https://example.com save/clip/保存/收藏
```

**工作原理：** OpenClaw 将你的消息与 `SKILL.md` 中的 `description` 字段进行匹配，自动调用此 skill。

### 常见使用场景

- 微信公众号
- 任意网页

---

## 工作流程

```
用户发送网址 + 触发短语
        ↓
  OpenClaw 识别 skill
        ↓
  打开目标页面（Chrome，profile=openclaw）
        ↓
  等待页面完全加载
        ↓
  自动滚动页面（触发懒加载图片）
        ↓
  将 Chrome 切换到前台
        ↓
  按下 ⌥⇧O 快捷键
        ↓
  Obsidian Web Clipper 将 Markdown 保存到保险库
        ↓
  向用户返回确认
```

---

## 保存位置

剪藏的笔记会保存到保险库的 `Clippings/` 文件夹：

```
你的保险库/
└── Clippings/
    ├── your-article-title.md          ← 笔记内容（Markdown）
    └── attachments/                    ← 下载的图片
        ├── image-001.png
        └── image-002.jpg
```

---

## 已知限制

- **仅支持 Chrome** — Safari、Firefox 和其他浏览器不支持
- **内容质量取决于来源网站** — 有些网站有反爬措施或大量 JS 渲染
- **快捷键冲突** — 如果 ⌥⇧O 被其他应用占用（如 Alfred、Raycast），剪藏会失败。请更换快捷键或关闭冲突应用
- **微信文章** — 微信公众号有反爬保护，图片可能无法自动下载。强烈建议安装 Local Images Plus
