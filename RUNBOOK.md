# 新窗口 Agent 操作手册：公众号草稿箱同步

这份手册给新开的 Codex 窗口使用。目标很明确：根据 Dylan 给出的主题、文章源稿、飞书文档或本地素材，完成公众号文章写作、排版、配图处理，并同步到微信公众号草稿箱。

默认只创建草稿，不发布。

## 0. 新窗口先读什么

新窗口 Agent 启动后，先读这几个文件：

1. 本文件：`RUNBOOK.md`
2. 技能说明：`SKILL.md`
3. 流程清单：`references/wechat_draft_pipeline.md`
4. 如果本机已安装 md2wechat Skill，再读：`/Users/macos/.codex/skills/md2wechat/SKILL.md`

不要一上来就操作公众号后台。先确认源稿、素材、标题、封面和发布目标。

## 1. 适用场景

当 Dylan 说类似这些话时，使用本流程：

- “根据这个主题写一篇公众号，并同步到草稿箱”
- “飞书文档已经写好了，帮我排版后同步公众号草稿”
- “图片在这个文件夹里，帮我插进公众号文章”
- “用之前那套公众号流程”
- “用 wechat-draft-publisher / md2wechat”

如果只是要标题、选题、大纲、文案，不需要走上传流程。

## 2. 必要输入

开始前确认这些信息：

| 输入 | 必需 | 说明 |
|---|---:|---|
| 文章主题 / 标题方向 | 是 | 没有主题就先写大纲，不上传 |
| 正文来源 | 是 | 飞书链接、Markdown、本地文档、聊天中的大纲都可以 |
| 图片目录 | 建议 | 本地最终配图文件夹，优先使用用户给定路径 |
| 封面图 | 是 | 没有封面就从图片目录里选最合适的一张，或先生成候选 |
| 公众号 API 配置 | 上传时必需 | 不要在回复里明文展示 AppSecret |
| API IP 白名单 | 上传时必需 | 报错时提示用户添加当前出口 IP |

## 3. 推荐工作目录

每篇文章单独建一个工作目录，避免素材混乱。

推荐路径格式：

```text
/Users/macos/Documents/Codex/wechat-drafts/YYYY-MM-DD-文章短名/
```

目录建议：

```text
.
├── source.md                  # 原始稿或从飞书整理出的正文
├── article.final.md            # 最终 Markdown
├── article.wechat.html         # 微信公众号 HTML
├── image-map.json              # 图片占位与本地路径映射
├── assets/                     # 文章配图
├── logs/                       # 上传日志、返回结果
└── draft-result.json           # 草稿 media_id 等结果
```

## 4. Skills 使用顺序

### 第一步：使用 wechat-draft-publisher

这个仓库本身就是公众号草稿同步 Skill。新窗口 Agent 应按 `SKILL.md` 的规则处理：

- 先确认内容源
- 再整理图片顺序
- 再做标题、封面、图片、HTML 预检
- 最后上传并创建草稿

关键约束：

- 只创建草稿，不发布
- 不删除旧草稿
- 不覆盖用户没确认的内容
- API 报 IP 白名单错误时，先停下让用户加白名单

### 第二步：使用 md2wechat

当需要把 Markdown 转成公众号可用 HTML 时，调用本机已安装的 `$md2wechat` Skill。

新窗口 Agent 必须先读取：

```text
/Users/macos/.codex/skills/md2wechat/SKILL.md
```

推荐用途：

- Markdown 转微信公众号 HTML
- 选择公众号排版主题
- 生成适合粘贴 / 上传的文章结构
- 保留短段落、标题层级、引用块、表格等样式

排版原则：

- 不要做花哨网页风，公众号阅读优先
- 标题、重点句、表格要清楚
- 图片之间要有呼吸感
- 移动端阅读比桌面预览更重要

## 5. 标准执行流程

### 5.1 内容确认

先给 Dylan 一个短确认：

```text
我会按“写稿/整理源稿 -> 图片映射 -> md2wechat 排版 -> API 创建草稿”的流程走。
这次默认只同步到公众号草稿箱，不发布。
```

然后收集：

- 主标题
- 副标题或摘要
- 飞书文档 / Markdown / 大纲
- 本地图片目录
- 是否指定封面

### 5.2 文章整理

如果 Dylan 只给主题：

1. 先产出大纲或完整文案
2. 让 Dylan 确认方向
3. 再进入排版与上传

如果 Dylan 已给飞书文档：

1. 读取飞书文档内容
2. 保留用户已定稿的表达
3. 只做公众号化排版、标题层级和图片位置整理

如果 Dylan 已给 Markdown：

1. 直接进入结构清理
2. 标记图片位置
3. 生成最终 Markdown

### 5.3 图片处理

图片规则：

- 优先使用 Dylan 给出的本地图片目录
- 按文章出现顺序建立 `image-map.json`
- 每张图都检查文件是否存在
- 封面图单独标记
- 不要把平台真实 UI 截图伪造成 AI 生成图

`image-map.json` 示例：

```json
{
  "cover": "/absolute/path/to/cover.png",
  "body": [
    {
      "placeholder": "{{image_01}}",
      "path": "/absolute/path/to/image-01.png",
      "caption": "四个平台首页对比"
    }
  ]
}
```

### 5.4 生成公众号 HTML

用 `$md2wechat` 将 `article.final.md` 转成 `article.wechat.html`。

生成后检查：

- 正文标题没有和公众号标题重复得太难看
- 表格在手机端可读
- 图片位置正确
- 重点句没有过度加粗
- 没有明显 AI 味套话

### 5.5 上传素材

上传前检查 API 配置：

- `WECHAT_APP_ID`
- `WECHAT_APP_SECRET`
- 公众号 API IP 白名单

不要把 AppSecret 写进 GitHub、README、文章正文或聊天最终回复。

上传顺序：

1. 获取 access_token
2. 上传正文图片，拿到微信图片 URL
3. 上传封面图为 thumb media
4. 替换 HTML 里的本地图片路径
5. 创建草稿
6. 保存返回结果到 `draft-result.json`

### 5.6 草稿验收

草稿创建成功后，最终回复 Dylan：

- 草稿标题
- 草稿 media_id
- 使用的封面图
- 正文图片数量
- 是否已在草稿箱生成
- 需要 Dylan 手动检查的点

不要说“已经发布”。除非用户明确要求发布，否则只说“已创建草稿”。

## 6. 常见故障处理

### API IP 白名单错误

表现：微信 API 返回 IP not in whitelist。

处理：

1. 查当前出口 IP
2. 告诉 Dylan 把这个 IP 加到公众号后台 API IP 白名单
3. 等 Dylan 确认后重试上传

不要改文章，不要重新生成内容，只重试上传阶段。

### GitHub / Skill 权限问题

这个仓库已经配置过本机 GitHub CLI 授权。下次需要更新仓库时，先检查登录状态。

如果 GitHub CLI 仍可用，Agent 可以直接提交并推送。

如果认证失效，使用 GitHub device login，让 Dylan 在浏览器授权一次。不要让 Dylan 手动上传文件，除非本机认证完全不可用。

### 飞书文档无法编辑

如果飞书文档只读：

1. 先读取内容
2. 在本地生成最终 Markdown / HTML
3. 上传公众号草稿

不要卡在“不能编辑飞书文档”。飞书只是内容源，不是必须编辑的中间站。

### 浏览器后台能登录，但 API 失败

浏览器登录态和微信 API 不是同一套权限。

浏览器适合人工检查草稿，API 负责自动创建草稿。API 失败时优先看 AppSecret、access_token、IP 白名单和素材上传限制。

## 7. 新窗口可直接复制的启动提示词

给新窗口 Agent 可以这样发：

```text
你现在要接手 Dylan 的公众号草稿同步流程。

先读取这个仓库：
/Users/macos/Documents/Codex/2026-05-08/chrome-plugin-chrome-openai-bundled-chrome/wechat-draft-publisher

重点读：
1. RUNBOOK.md
2. SKILL.md
3. references/wechat_draft_pipeline.md
4. /Users/macos/.codex/skills/md2wechat/SKILL.md

任务目标：
根据我给出的主题 / 飞书文档 / Markdown / 本地图片目录，完成公众号文章写作或整理、md2wechat 排版、图片上传，并创建到微信公众号草稿箱。只创建草稿，不发布。

执行时先确认源稿、图片目录、封面图；然后生成最终 Markdown 和微信公众号 HTML；再走微信公众号 API 创建草稿；最后告诉我草稿 media_id 和检查事项。
```

## 8. 最终交付标准

一次合格交付必须包含：

- 本地最终 Markdown
- 本地公众号 HTML
- 图片映射记录
- 上传结果记录
- 微信公众号草稿箱里可见的一篇草稿
- 给 Dylan 的简短结果说明

不合格情况：

- 只写了文章，没有同步草稿
- 图片顺序错乱
- 缺封面
- API 报错后没有明确告诉 Dylan 下一步要做什么
- 把 AppSecret 明文写进公开文件

