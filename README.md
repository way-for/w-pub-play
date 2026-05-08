# 📖 墨卷 · 免费小说阅读器

> 免费阅读全网小说，支持电脑/手机，个人账号同步阅读进度。
> 基于 GitHub Pages + Cloudflare Workers + Supabase，全程零费用。

---

## 效果预览

- **首页**：搜索框 + 最近阅读列表
- **书籍页**：封面、简介、完整目录
- **阅读器**：可调字号/行距/主题（暖纸/夜间/护眼/水墨）
- **账号页**：书架收藏 + 阅读历史，支持多设备同步

---

## 部署步骤（约 30 分钟）

### 第一步：部署 Cloudflare Worker（必须）

Worker 是核心，负责从小说网站抓取内容并返回 JSON。

1. 注册 [Cloudflare](https://dash.cloudflare.com)（免费，无需信用卡）
2. 左侧菜单 → **Workers & Pages** → **创建应用程序** → **创建 Worker**
3. 给 Worker 起个名字，例如 `novel-proxy`
4. 点击 **编辑代码**，把 `worker/index.js` 的全部内容粘贴进去
5. 点击右上角 **保存并部署**
6. 复制 Worker URL，格式如：
   ```
   https://novel-proxy.你的用户名.workers.dev
   ```

**免费额度**：每天 10 万次请求，个人使用绰绰有余。

---

### 第二步：配置 Supabase（可选，用于账号同步）

如果只想本机用，跳过此步。注册登录和多设备同步需要此步骤。

1. 访问 [supabase.com](https://supabase.com)，注册并创建新项目（免费）
2. 等待项目初始化完成（约 1 分钟）
3. 左侧菜单 → **SQL Editor** → 新建查询，把 `supabase_schema.sql` 内容粘贴进去运行
4. 左侧菜单 → **Project Settings** → **API**，复制：
   - `Project URL`（形如 `https://xxxx.supabase.co`）
   - `anon public` key（长字符串）

---

### 第三步：修改 index.html 配置

用文本编辑器打开 `index.html`，找到顶部的 `CFG` 配置块：

```javascript
const CFG = {
  // 替换为第一步得到的 Worker URL
  worker: 'https://novel-proxy.你的用户名.workers.dev',
  // 替换为第二步得到的 Supabase URL（没有Supabase就保留原样）
  sbUrl: 'https://YOUR_PROJECT.supabase.co',
  // 替换为第二步得到的 anon key
  sbKey: 'YOUR_SUPABASE_ANON_KEY',
}
```

---

### 第四步：上传到 GitHub Pages

1. 登录 GitHub，创建一个**新的公开仓库**（例如 `novel-reader`）
2. 上传本项目所有文件：
   - 直接拖拽文件到 GitHub 仓库页面，或
   - 使用 git 命令：
     ```bash
     git init
     git add .
     git commit -m "初始化墨卷阅读器"
     git remote add origin https://github.com/你的用户名/novel-reader.git
     git push -u origin main
     ```
3. 仓库页面 → **Settings** → **Pages**
4. Source 选 **Deploy from a branch**，Branch 选 **main**，目录选 **/ (root)**
5. 点击 **Save**，等约 1 分钟

访问地址：`https://你的用户名.github.io/novel-reader/`

---

## 项目结构

```
novel-reader/
├── index.html           # 主应用（单页面，CSS+JS 全内置）
├── worker/
│   └── index.js         # Cloudflare Worker 代理脚本
├── supabase_schema.sql  # 数据库建表 SQL（在 Supabase 中运行）
├── _config.yml          # GitHub Pages 配置
└── README.md            # 本文档
```

---

## 功能说明

| 功能 | 说明 |
|------|------|
| 🔍 搜索小说 | 输入书名/作者名搜索，数据来自互联网小说站 |
| 📖 在线阅读 | 干净阅读界面，无广告干扰 |
| 📚 书架收藏 | 收藏喜欢的书，书架本地保存 |
| 📝 阅读记录 | 自动记录读到哪章，下次继续 |
| 🎨 主题切换 | 暖纸/夜间/护眼/水墨，右上角一键切换 |
| ⚙️ 阅读设置 | 字号、行距、字体，阅读中点击⚙️调整 |
| ☁️ 多设备同步 | 注册账号后书架+进度同步到云端 |
| ⌨️ 键盘快捷键 | ← → 翻章节，Esc 返回目录（桌面端） |

---

## 常见问题

**Q：搜索没有结果？**
A：检查 Worker URL 是否填写正确；Cloudflare 控制台可查看请求日志排查错误。

**Q：章节内容乱码或为空？**
A：目标小说网站结构可能有变动。可在 `worker/index.js` 顶部换用 `SITES.primary.base` 备用域名。

**Q：不想用 Supabase，只本地用可以吗？**
A：完全可以！阅读记录和书架都会保存在浏览器 localStorage，不影响核心功能，只是不能多设备同步。

**Q：如何换小说网站？**
A：修改 `worker/index.js` 顶部 `SITES.primary.base` 的域名，再根据目标网站调整正则表达式。

**Q：访问速度慢怎么办？**
A：Cloudflare Worker 内置 5 分钟缓存，重复访问同一章节会很快。首次加载取决于上游网站速度。

---

## 技术栈

- **前端**：原生 HTML/CSS/JavaScript，无需构建工具
- **后端**：Cloudflare Workers（Edge Computing）
- **数据库**：Supabase（PostgreSQL + Auth）
- **部署**：GitHub Pages（静态托管）

---

## 免责声明

本项目仅供个人学习使用，不得用于商业目的。
小说内容版权归原作者及出版方所有，请支持正版。
