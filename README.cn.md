# Mintlify Astro 启动模板

使用 [Astro](https://astro.build) 构建完全自定义的文档前端，同时使用 Mintlify 管理您的内容、搜索和 AI 基础设施。

这个启动器不使用 Mintlify 的托管前端，而是让您完全掌控整个展示层——布局、组件和样式——而 `@mintlify/astro` 集成则在构建时处理内容处理、导航解析和组件渲染。

详细教程请参见[创建自定义前端](https://www.mintlify.com/docs/guides/custom-frontend)指南。

## 前提条件

- [Node.js](https://nodejs.org) v20.17.0 或更高版本（推荐 LTS）
- 一个 [Mintlify 账户](https://dashboard.mintlify.com)
- 一个 [GitHub 账户](https://github.com)

## 入门指南

### 1. 创建您的仓库

前往 GitHub 上的 [mintlify-astro-starter](https://github.com/mintlify/mintlify-astro-starter) 仓库，点击 **Use this template** 在您的账户上创建一个新仓库。然后在本地克隆它：

```bash
# git clone https://github.com/<your-username>/<your-repo>.git
git clone git@github.com:youshengyouse/mintlify-astro.git
cd mintlify-astro
```

### 2. 连接到 Mintlify

1. 在 [dashboard.mintlify.com](https://dashboard.mintlify.com) 注册或登录。

2. 在 [Git 设置](https://dashboard.mintlify.com/settings/deployment/git-settings)页面，安装 [Mintlify GitHub 应用](https://mintlify.com/docs/deploy/github)。

3. 选择您的新仓库，启用 **Set up as monorepo**，并输入 `/docs` 作为文档目录路径。

4. 点击 **Save changes**。

### 3. 配置环境变量

如果您有[专业版或企业版计划](https://mintlify.com/pricing)，请在项目根目录创建 `.env` 文件以启用搜索和 AI 助手：

```bash
PUBLIC_MINTLIFY_SUBDOMAIN=your-subdomain
PUBLIC_MINTLIFY_ASSISTANT_KEY=your-assistant-api-key
```

**子域名** — 您的仪表板 URL 中的域名部分。例如，如果您的 URL 是 `https://dashboard.mintlify.com/org-name/domain-name`，您的子域名是 `domain-name`。

**助手 API 密钥** — 在仪表板的 [API 密钥](https://dashboard.mintlify.com/settings/organization/api-keys)页面生成一个。它以 `mint_dsc_` 开头。

### 4. 安装并运行

```bash
npm install
npm run dev
```

您的网站现在在 `http://localhost:4321` 运行。

## 项目结构

```
├── docs/ # 文档内容
│ ├── docs.json # 导航和站点配置
│ ├── index.mdx # 首页
│ ├── quickstart.mdx
│ ├── customization.mdx
│ ├── components.mdx
│ └── guides/
│ └── example.mdx
├── src/
│ ├── pages/
│ │ └── [...slug].astro # MDX 页面的通配路由
│ ├── layouts/
│ │ └── Layout.astro # 根 HTML 布局
│ ├── components/
│ │ ├── Header.astro # 站点头部
│ │ ├── Footer.tsx # 页面底部
│ │ ├── SearchBar.tsx # 搜索组件
│ │ ├── Sidebar/ # 侧边栏导航
│ │ ├── Assistant/ # AI 聊天界面
│ │ └── TableOfContents.tsx
│ ├── hooks/ # 自定义 React 钩子
│ ├── icons/ # SVG 图标组件
│ ├── styles/ # 全局 CSS、排版、配色方案
│ └── utils/ # 工具函数
├── astro.config.mjs # Astro + Mintlify 集成配置
├── tsconfig.json
└── package.json
```

## 工作原理

`@mintlify/astro` 集成连接了三个部分：Astro 构建系统、您在 `docs/` 中的内容，以及处理和渲染该内容的 Mintlify 包。

### Astro 配置

该集成在 `astro.config.mjs` 中配置：

```javascript
import { defineConfig } from 'astro/config';
import react from '@astrojs/react';
import mdx from '@astrojs/mdx';
import { mintlify } from '@mintlify/astro';

export default defineConfig({
  integrations: [mintlify({ docsDir: './docs' }), react(), mdx()],
});
```

在构建时，该集成读取 `docs.json` 和您的 MDX 文件，将它们处理到 `.mintlify/docs/`，然后 Astro 的内容集合将它们拾取。

### 内容

文档以 MDX 文件形式存在于 `docs/` 中，结构类似于任何 Mintlify 项目。MDX 文件使用标准 Mintlify [frontmatter](https://mintlify.com/docs/organize/pages#page-metadata)，可以使用 Mintlify 组件而无需导入它们。导航和站点元数据在 `docs/docs.json` 中配置。

### 路由和导航

`src/pages/[...slug].astro` 处的通配路由渲染每个 MDX 页面。`@mintlify/astro/helpers` 包提供：

- `resolvePageData()` — 返回给定页面路径的标签、侧边栏导航、页脚链接和锚点。
- `unwrapNav()` — 将导航树展平为用于侧边栏渲染的列表。

## 自定义

您完全掌控展示层。需要自定义的关键文件：

| 文件 | 用途 |
| ------------------------------------ | ------------------------------------------- |
| `src/layouts/Layout.astro` | 根 HTML 布局 |
| `src/pages/[...slug].astro` | 页面模板和数据加载 |
| `src/components/Header.astro` | 站点头部 |
| `src/components/Sidebar/` | 侧边栏导航 |
| `src/components/TableOfContents.tsx` | 页面内目录 |
| `src/styles/` | 全局样式、排版和配色方案 |

## 搜索和 AI 助手

> 需要[专业版或企业版计划](https://mintlify.com/pricing)。

该启动器包含连接到 Mintlify API 的搜索和助手组件。两者都需要 `PUBLIC_MINTLIFY_SUBDOMAIN` 和 `PUBLIC_MINTLIFY_ASSISTANT_KEY` 环境变量。

- **搜索** — `src/components/SearchBar.tsx` 中的 `SearchBar` 组件查询 Mintlify 搜索 API。
- **助手** — `src/components/Assistant/` 中的 `Assistant` 组件提供一个 AI 聊天界面，使用您的文档内容回答问题。

## 可用脚本

| 命令 | 描述 |
| ------------------ | ------------------------------------ |
| `npm run dev` | 启动开发服务器 |
| `npm run build` | 为生产环境构建站点 |
| `npm run preview` | 本地预览生产构建 |
| `npm run format` | 使用 Prettier 格式化代码 |
| `npm run lint` | 运行 ESLint |
| `npm run lint:fix` | 运行 ESLint 并自动修复 |

## AI 辅助写作

设置您的 AI 编码工具以使用 Mintlify：

```bash
npx skills add https://mintlify.com/docs
```

这将为 Claude Code、Cursor、Windsurf 等工具安装 Mintlify 的文档技能——包括组件参考、写作标准和工作流程指南。

## 资源

- [创建自定义前端指南](https://www.mintlify.com/docs/guides/custom-frontend)
- [Mintlify 文档](https://mintlify.com/docs)
- [Astro 文档](https://docs.astro.build)
- [贡献指南](./CONTRIBUTING.md)
- [开发指南](./DEVELOPMENT.md)
