SynapseRound(猛猛开会）
SynapseRound 是一个中文多角色讨论与用户访谈模拟工作台。它可以帮助产品经理、创业团队、设计师和学生围绕一个议题进行结构化思考：你可以召集六顶思考帽角色，或配置模拟用户角色，让不同视角接续发言；也可以上传资料、查看观点来源、整理讨论成果并从历史话题继续讨论。

AI 生成的发言和用户观点都是模拟内容，不是真实用户研究或专业意见。重要结论请自行核实。

适合做什么
用六顶思考帽从事实、感受、风险、收益、创意和过程等角度讨论议题。
模拟不同类型的用户访谈，探索需求、顾虑与可能的使用情境。
在讨论中提出追问，整理洞察、分歧和下一步行动。
上传参考资料，在相关讨论中查阅和追溯材料来源。
保存讨论历史、回看已有发言，或在原记录后继续讨论。
普通用户：如何使用
打开项目网页；不需要安装软件或编写代码。
选择「六顶思考帽」或「用户访谈」模式，填写一个具体议题。
按需要选择或调整参与角色、讨论时长及发言设置。
开始讨论。你可以阅读不同角色的发言，并在讨论过程中提出追问或补充。
查看讨论成果，手动记录值得保留的洞察和下一步行动；也可以在历史讨论中回看或继续之前的话题。
如需结合文档讨论，在参考资料区域上传支持的文件并查看其使用情况。
提示：问题越具体，越容易得到有用的讨论。例如，与其问「怎么做好产品」，不如讨论「首次使用本产品的自由职业者为什么会在注册步骤退出」。

开发者：下载和启动项目
本项目使用 Next.js 16、React 19、TypeScript、Tailwind CSS、Neon/PostgreSQL 和 Vercel Blob。

你需要先安装：

Node.js：20.9 或更高版本，建议使用当前受支持的 LTS 版本。
pnpm：项目指定使用 pnpm 12；如使用 Corepack，可运行 corepack enable，然后运行 corepack prepare pnpm@12.3.4 --activate。
GitHub：把本仓库克隆到电脑，或将下载的项目文件解压到一个文件夹。
在项目文件夹打开终端，依次运行：

pnpm install
配置下面的环境变量之后，再启动开发服务：

pnpm dev
在浏览器打开终端显示的本地地址，通常是 http://localhost:3000。

其他可用命令：

pnpm build
pnpm start
pnpm start 用于启动已通过 pnpm build 生成的生产版本。

配置环境变量
项目依赖服务端环境变量才能完整运行。先复制项目自带的 .env.example，另存为项目根目录下的 .env.local，再在 .env.local 中填写自己的凭据。.env.example 是空白模板，可以安全上传 GitHub；真实凭据只填 .env.local：

STEPFUN_API_KEY=在StepFun获取的密钥
DATABASE_URL=在Neon获取的连接字符串
BLOB_READ_WRITE_TOKEN=在Vercel获取的读写令牌
macOS/Linux 用户可以在项目目录的终端运行 cp .env.example .env.local，然后用代码编辑器打开 .env.local 并填入实际值；Windows 用户可以在文件管理器中复制 .env.example 并将副本重命名为 .env.local。请勿把示例文字当成凭据使用。

首次启动前，务必填入真实凭据；各项用途如下：

变量	用途	必需情况
STEPFUN_API_KEY	服务端调用 StepFun 模型，生成讨论发言和总结。	AI 讨论必需
DATABASE_URL	连接 Neon/PostgreSQL，保存和读取讨论历史。	讨论历史必需
BLOB_READ_WRITE_TOKEN	使用 Vercel Blob 私有存储上传资料及其相关数据。	资料上传必需
将 .env.local 中的值替换为服务提供方实际签发的凭据。不要把密钥写进源码、发送到公开聊天或提交到 GitHub。.env.local 已列入忽略规则；如需分享项目，请分享 .env.example 这样的空白模板，而不是实际配置文件。修改环境变量后，重新启动 pnpm dev。

获取服务凭据
StepFun：在 StepFun 开发者平台创建 API 密钥，并将它填入 STEPFUN_API_KEY。切勿使用带 NEXT_PUBLIC_ 前缀的名称；模型密钥只应保留在服务器端。

Neon：创建 PostgreSQL 数据库，复制 Neon 控制台中的**带连接池（pooled）**数据库连接字符串作为 DATABASE_URL。首次连接后，在 Neon 控制台打开该数据库的 SQL Editor，执行下面的建表语句；本项目暂未提供自动迁移命令。数据库连接字符串含有密码，应按密钥保护。

CREATE TABLE IF NOT EXISTS discussion_history (
  id uuid NOT NULL,
  "userId" text NOT NULL,
  snapshot jsonb NOT NULL,
  notes jsonb NOT NULL DEFAULT '[]'::jsonb,
  "createdAt" timestamp with time zone NOT NULL DEFAULT now(),
  "updatedAt" timestamp with time zone NOT NULL DEFAULT now(),
  PRIMARY KEY ("userId", id)
);
该表对应项目的 lib/db/schema.ts，用于保存讨论历史和手动整理的洞察。将 SQL 粘贴到 SQL Editor 后点击运行即可。

Vercel Blob：在 Vercel 项目中连接 Blob 存储，并将该项目对应的读写令牌配置为 BLOB_READ_WRITE_TOKEN。

如果在 Vercel 部署而不是本机运行，请在 Vercel 项目设置的 Environment Variables 中配置相同变量，并确保所需的部署环境都已启用，然后重新部署。不要将凭据写入 GitHub 仓库。

常见问题
提示 STEPFUN_API_KEY 未配置：检查根目录是否存在 .env.local，变量拼写和密钥是否正确，然后重新启动开发服务器。
讨论历史不可用：检查 DATABASE_URL、Neon 数据库是否可访问，以及数据库是否已初始化对应数据表。
文件上传或资料读取失败：检查 BLOB_READ_WRITE_TOKEN 是否有效、Blob 是否已连接，以及本机或 Vercel 环境配置是否生效。
更改环境变量后页面仍报错：停止当前开发服务器并重新运行 pnpm dev；Next.js 不会自动重新读取进程已启动后修改的环境变量。
安全提示
.env.local 仅保存在自己的开发环境，不要提交、上传或截图分享。
如果密钥曾被意外公开，请立即到对应服务撤销并重新生成。
项目使用匿名工作区能力保存资源与记录；这不等于完整的用户登录或账户体系。仅在你信任的环境中保存敏感资料。
AI 生成结果可能不准确，不应直接当作真实访谈结论或未经验证的事实。
技术信息
Web 应用：Next.js App Router、React 19、TypeScript。
样式与组件：Tailwind CSS、项目内的 shadcn/ui 组件。
讨论生成：服务端调用 StepFun OpenAI-compatible API。
数据库：PostgreSQL，使用 Drizzle ORM。
文件与私有资料存储：Vercel Blob。
历史数据在服务端存储；清理浏览器 Cookie 或更换浏览器后，匿名工作区可能无法继续访问原有数据。
