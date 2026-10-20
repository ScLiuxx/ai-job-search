<p align="center">
  <img src="assets/mascot/pip_flight_loop.gif" alt="Pip, the courier bird" width="200">
</p>

# AI 求职（AI Job Search）

*运行在你自己电脑上的求职助手。*

<p align="center">
  <a href="https://trendshift.io/repositories/43622?utm_source=trendshift-badge&amp;utm_medium=badge&amp;utm_campaign=badge-trendshift-43622" target="_blank" rel="noopener noreferrer"><img src="https://trendshift.io/api/badge/trendshift/repositories/43622/daily" alt="MadsLorentzen%2Fai-job-search | Trendshift" width="250" height="55"/></a>
</p>

[![CI](https://github.com/MadsLorentzen/ai-job-search/actions/workflows/ci.yml/badge.svg)](https://github.com/MadsLorentzen/ai-job-search/actions/workflows/ci.yml)

一个基于 [Claude Code](https://claude.com/claude-code) 构建的 AI 求职申请框架。Fork 它，填好你的个人资料，然后让 Claude 评估招聘信息、定制简历、撰写求职信，并帮你准备面试。

> 注意：这是一个独立的开源项目，与 Anthropic 无任何关联，也未得到其背书、赞助或维护。提及 Anthropic 和 Claude Code 仅用于说明本工作流所使用的工具链。
>
> 本项目**没有任何相关的加密货币、代币或付费赞助计划**。任何声称与此相关的内容均未经授权，应视为诈骗。支持本项目的唯一方式是下方的 Ko-fi 链接以及在 GitHub 上贡献代码。

## 它真的有用吗？

我是一名地球物理学科班出身。2025 年底我的职位被裁撤后，我搭建了这个框架来开展自己的求职——就是本仓库中这套 `/scrape`、`/apply`、`/interview` 工作流，我每周都用它来经营自己的职业。我对接触过的每位雇主都坦诚说明了这一点，它非但没有成为我的减分项，反而常常引发一场真正的技术对话。

在投递了 69 份定制申请、参加了 20 场初试、签下一份合同之后，我于 2026 年 6 月入职，成为一名 AI 工程师。总有人问我这东西到底有没有用。它帮我找到了工作。现在它也属于你。

*更完整的经过，包括完整的申请漏斗数据，发布在 [LinkedIn](https://www.linkedin.com/in/mads-lorentzen/) 上。*

<p align="center">
  <i>它是否帮你省下了一个写求职信的周日？可以请我喝杯咖啡。<br>
  它是否帮你拿到了 offer？也许可以请两杯。</i> ☕
</p>

<p align="center">
  <a href="https://ko-fi.com/madslorentzen">
    <img src="https://storage.ko-fi.com/cdn/kofi3.png?v=6" alt="Buy me a coffee at ko-fi.com" height="40">
  </a>
</p>

## 这是什么

一个将 Claude Code 变成全栈求职申请助手的结构化工作流。核心工作流（自我画像、匹配度评估、起草者—审阅者申请流水线）**与语言和国家无关**。招聘网站搜索技能是为丹麦市场构建的（Jobindex、Jobnet、Akademikernes Jobbank 等），但其设计模式可以替换为你本地的招聘网站。

```
/setup          /scrape              /apply <url>
  |                |                     |
  v                v                     v
填写            搜索招聘             评估匹配度
个人资料        网站                 打分并给出建议
  |                |                     |
  v                v                     v
资料文件        展示匹配结果         起草简历 + 求职信
准备就绪        并附匹配度评分       （LaTeX，量身定制）
                   |                     |
                   v                     v
               选定一个匹配项       审阅者 agent 提出批评
               -> /apply            -> 修改 -> 输出最终成品
```

该框架融入了职业指导的最佳实践，包括结构化的评估标准、面向未来的求职信表述框架，以及可选的薪资基准对比。

## 前置条件

- [Claude Code](https://claude.com/claude-code)（命令行工具）。Claude Code 没有免费额度：你需要 Claude Pro/Max/Team 订阅，或 Anthropic API 额度（按 token 付费，偶尔使用通常更便宜）。使用其他 agent 工具（Codex、Antigravity、Gemini CLI）？请从 [`AGENTS.md`](AGENTS.md) 开始——招聘网站搜索技能在这些工具中开箱即用，[社区 fork](https://github.com/MadsLorentzen/ai-job-search/discussions/78) 也已适配完整工作流。
- Python 3.10+
- [Bun](https://bun.sh)（用于求职搜索命令行工具）
- 带有 `lualatex` 和 `xelatex` 的 LaTeX 发行版：[TeX Live](https://tug.org/texlive/)、[MacTeX](https://tug.org/mactex/)、[TinyTeX](https://yihui.org/tinytex/) 或 [MiKTeX](https://miktex.org/)。简历用 `lualatex` 编译（现代 MiKTeX 安装中 pdflatex 常因 `fontawesome5` 字体扩展错误而失败）；求职信用 `xelatex` 编译，因为 `cover.cls` 依赖 `fontspec`。如果使用 TinyTeX 或 BasicTeX 这类最小化 TeX 安装，请额外安装 [SETUP.md](SETUP.md#minimal-tex-install-tinytexbasictex) 中列出的额外宏包。
- 可选：`pip install pypdf`，用于 `/apply` 的 ATS（简历筛选系统）可解析性检查（BSD 协议；无需 Poppler）。Poppler 的 `pdftotext` 仍作为后备方案（macOS：`brew install poppler`，Debian/Ubuntu：`apt install poppler-utils`，Windows：`choco install poppler`）。如果两者都没有，该检查会降级为可视化的关键词审查。

## 快速开始

> 🎥 **想先看实际演示？** [The Next New Thing 做了一期实操演示](https://www.youtube.com/watch?v=HoVxjMNFYv4)，展示了从初始设置到完成一份申请的真实使用过程（录制于 2026 年 8 月——此后命令可能已有更新）。

### 1. Fork 并克隆

```bash
gh repo fork MadsLorentzen/ai-job-search --clone
cd ai-job-search
```

> [!IMPORTANT]
> **对本仓库的 fork 始终是公开的**——GitHub 不允许将公开仓库 fork 为私有——而 `/setup`（下方第 3 步）会把你的个人数据（姓名、联系方式、工作经历、薪资期望）写入**被 Git 跟踪**的文件。
> 如果这份副本是用于你自己的求职、而非向原项目贡献代码，请改用一个以本仓库为 `upstream` 的**私有仓库**——两分钟的操作步骤见 [SETUP.md 第 8 节](SETUP.md#8-pulling-upstream-updates-into-your-fork)，所有更新流程的用法完全相同。只有为了贡献代码时才需要 fork。

### 2. 安装求职搜索工具

```bash
python3 tools/install_portal_clis.py   # Windows: python tools\install_portal_clis.py
```

它会在 `.agents/skills/*/cli/` 下的每个招聘网站 CLI 目录中运行 `bun install`——采用自动发现而非硬编码列表，因此你通过 `/add-portal` 新增或从其他 fork 复制的招聘网站，在全新克隆后也会被包含。若某个 CLI 的 `package.json` 声明了安装时生命周期脚本，则会跳过并报告，而不会执行它。`--dry-run` 可预览将要安装的内容。

对于 `linkedin-search` 和 `freehire-search`，安装是可选的：两者都没有运行时依赖，用纯 `bun` 即可运行；`bun install` 只会拉取 TypeScript 开发类型定义。

### 3. 设置你的个人资料

```bash
claude
# 然后在 Claude Code 中运行：
/setup
```

`/setup` 提供三种路径：读取你已放入内容的 `documents/` 文件夹（简历 PDF、LinkedIn 导出、文凭、推荐信、过往申请材料），导入在聊天中粘贴的单份简历，或通过一场访谈来完成。它会自动检测你已有什么并进行询问。文档文件夹模式是幂等的，随着你添加更多材料可以安全地重复运行；目录布局见 `documents/README.md`。

### 4. 搜索职位

```bash
/scrape
```

它会在多个招聘网站搜索与你资料匹配的职位，对结果去重，并按匹配度排序后展示。选定一个匹配项即可直接对其运行 `/apply`；或者，当一次抓取返回的职位多到你不想逐个查看时，可以运行 `/rank`，按匹配度框架对它们批量打分，先得到一份排名候选名单。

### 5. 申请职位

```bash
/apply https://jobindex.dk/job/1234567
```

如果该 URL 无法抓取（部分招聘网站会阻止自动化访问），你可以直接粘贴职位描述：

```bash
/apply <在此粘贴完整的职位描述>
```

这会运行完整工作流：评估匹配度、起草简历与求职信、由第二个 agent 审阅、修改，并展示最终成品。招聘信息首先要通过一道确定性的完整性检查（隐藏文本、不可见字符、指令形态的语句、隐藏链接），其一句话结论会作为评估的开头；负责抓取和审阅的 agent 均为只读。

招聘信息被视为不可信输入（工作流不会执行其中嵌入的任何指令，也不会抓取其正文中的链接），但基于 agent 的防御是指令层面的，并非沙箱——在不熟悉的招聘网站上，发送前请快速浏览一下抓取和写出的内容。详见 [SECURITY.md](SECURITY.md)。

## 其他命令

`/setup`、`/scrape` 和 `/apply` 构成核心工作流。资料就绪后，还有另外十个命令对其进行扩展：

- **`/interview`** 为你在已跟踪申请上安排的面试做准备。它会根据该申请的归档材料（原始招聘信息、面试官实际看到的简历和求职信、前几轮记录的反馈）构建一份分阶段的备考包，遵循“先核实后使用”的原则调研公司和面试官，将可能的问题映射到你的 STAR 案例上，并按照 `07-interview-prep.md` 中的角色扮演协议提供一次模拟面试。对于经历缺口，会给出诚实的衔接性回答，绝不虚构经验。
- **`/outcome`** 记录一份申请的结果——面试阶段、offer、拒信、石沉大海。它会把已提交的简历、求职信和招聘正文归档到 `documents/applications/<公司>_<职位>/`，按 `/setup` 路径 A 可解析的格式维护 `outcome.md`，并更新跟踪表。它还负责“尚无结果可记录”阶段的跟进：`/outcome followup` 会找出已沉寂的在途申请（默认 10 天），仅使用你已提交材料中的表述、以你的写作风格起草一段简短且符合渠道特点的跟进消息（只起草、绝不发送；每份申请最多两次），并在记录某一面试阶段的同一轮对话中提供一封感谢信。当一些申请有了结果后，它会引导你回到 `/setup`，根据实际获得面试的情况校准匹配度框架。
- **`/notion-sync`** 通过官方 Notion MCP 服务器（OAuth，无需 API 密钥）将求职流水线的单向只读视图发布到 Notion 数据库——每个已排名职位和每份已跟踪申请各占一行，每行附带一个只写一次的简报页面。仓库文件仍是唯一的事实来源：不会有任何内容同步回来，文档仅以文件名形式同步。它与 `/html-report` 互补：后者是你在电脑前重新生成的深度离线仪表盘；前者则是在任何能运行 Notion 的设备（桌面端、网页、手机）上可随时查看的实时视图。
- **`/gmail-sync`**（通过 Gmail 连接器）读取你的 Gmail，获取在途申请的状态信号——面试邀请、测评链接、offer、拒信——并将其作为一批建议提交给你审批，之后才会写入跟踪表或 `outcome.md`，每条建议的变更都注明来源邮件。对于 offer，它不会直接建议标记为 `hired`（已录用）/`offer_declined`（已拒绝 offer），因为这由你决定；相互冲突或无法匹配的信号会被标记，交由你手动通过 `/outcome` 处理，而不是靠猜测。
- **`/rank`** 衔接 `/scrape` 和 `/apply`：它按匹配度框架对所有新抓取的招聘信息批量打分（并行 agent 抓取每条招聘信息并对五个评估维度打分），返回一份排名候选名单，并如实列出每个职位的优势与差距。一票否决项会直接否决，临近截止日期会标记紧急，已失效的招聘信息会标记为过期。选定编号后，它会交接给完整的 `/apply` 工作流。
- **`/expand`** 通过扫描你在资料中已链接的公开来源（GitHub 仓库、作品集网站、Kaggle、Google Scholar）并查询指定课程和认证的教学大纲，来丰富你的个人资料。新发现的能力会带来源标签添加到你的资料中。适合在 `/setup` 之后立即使用，以呈现仅凭文档无法明确体现的技能。
- **`/upskill`** 分析你的个人资料、已跟踪的招聘信息以及已排名但未跟踪的招聘信息（`/rank` 记录在 `job_scraper/seen_jobs.json` 中的差距）之间的缺口——也可通过 `/upskill <URL>` 针对单条招聘信息分析。它会生成一份按优先级排序的技能缺口热力图，以及一份附带网络检索学习资源和时间估算的学习计划。适合在两次申请之间做职业规划。
- **`/html-report`** 根据 `job_search_tracker.csv` 和申请归档生成一个自包含的 HTML 仪表盘——统计卡片、状态/行业/渠道/漏斗图表（内联 SVG，无外部依赖）以及可筛选的申请表。可在浏览器中直接打开，完全离线可用。在 `/apply` 或 `/outcome` 添加新条目后随时可重新生成。
- **`/add-template`** 用你自己的简历或求职信模板（LaTeX、Typst 或其他工具链）替换自带模板。它会采集模板的说明（源文件扩展名、编译命令、字体、样式规则、页数限制），运行一次强制的测试编译，并将模板接入 `/apply`。见下方[自定义模板](#自定义模板)。
- **`/add-portal`** 为你所在市场的招聘网站生成一个招聘网站搜索技能。它会调研该网站（搜索 URL 模式、结果结构、访问规则），按照与自带技能相同的结构搭建 CLI 技能，并在注册前进行一次真实查询的试运行。见下方[求职搜索工具](#求职搜索工具)。

另外还提供 `/reset`，见下方[重新开始](#重新开始)。

## 文件结构

```
ai-job-search/
├── CLAUDE.md                          # 主要的候选人资料 + 工作流规则
├── .claude/
│   ├── commands/
│   │   ├── apply.md                   # /apply 工作流（起草者—审阅者）
│   │   ├── setup.md                   # /setup 引导（文档文件夹、简历导入或访谈）
│   │   ├── expand.md                  # /expand 从文档和网络形象中补充能力
│   │   ├── add-template.md            # /add-template 注册自定义模板（LaTeX、Typst……）
│   │   ├── add-portal.md              # /add-portal 为你所在市场生成招聘网站搜索技能
│   │   ├── rank.md                    # /rank 将抓取的职位筛选为排名候选名单
│   │   ├── outcome.md                 # /outcome 记录申请结果、归档材料
│   │   ├── gmail-sync.md              # /gmail-sync 从 Gmail 自动检测申请状态
│   │   ├── interview.md               # /interview 分阶段备考包 + 模拟面试
│   │   ├── html-report.md             # /html-report 生成申请跟踪仪表盘
│   │   ├── notion-sync.md             # /notion-sync 在 Notion 数据库中展示单向流水线视图
│   │   └── reset.md                   # /reset 清除资料数据或文档文件夹
│   ├── skills/
│   │   ├── job-application-assistant/  # 核心申请技能
│   │   │   ├── SKILL.md               # 技能定义
│   │   │   ├── 01-candidate-profile.md # 你的教育、经历、技能
│   │   │   ├── 02-behavioral-profile.md# PI/DISC/性格测评
│   │   │   ├── 03-writing-style.md    # 语气、结构、注意事项
│   │   │   ├── 04-job-evaluation.md   # 职位匹配度打分框架
│   │   │   ├── 05-cv-templates.md     # LaTeX 简历结构 + 定制规则
│   │   │   ├── 06-cover-letter-templates.md # LaTeX 求职信模板
│   │   │   └── 07-interview-prep.md   # STAR 案例 + 面试框架
│   │   ├── job-scraper/               # 求职搜索编排
│   │   └── upskill/                   # /upskill 技能缺口分析与学习计划
│   └── settings.json                  # Claude Code 权限（共享、限定范围）
├── .agents/skills/                    # 招聘网站 CLI 工具
│   ├── jobbank-search/                # Akademikernes Jobbank（丹麦）
│   ├── jobdanmark-search/             # Jobdanmark.dk（丹麦）
│   ├── jobindex-search/               # Jobindex.dk（丹麦）
│   ├── jobnet-search/                 # Jobnet.dk（丹麦，政府门户网站）
│   ├── linkedin-search/               # LinkedIn 公开招聘信息（与国家无关）
│   └── freehire-search/               # freehire.me 技术岗位聚合器（多市场，REST API）
├── cv/
│   └── main_example.tex               # moderncv LaTeX 模板
├── cover_letters/
│   ├── cover.cls                      # 自定义求职信 LaTeX 类
│   ├── cover_example.tex              # 求职信示例（结构参考 + CI 冒烟测试）
│   └── OpenFonts/                     # Lato + Raleway 字体
├── templates/                         # 通过 /add-template 注册的自定义模板
│   └── README.md                      # 文件夹布局说明
├── documents/                         # 供 /setup 路径 A 和 /expand 使用的职业源材料
│   ├── README.md                      # 文件夹布局说明
│   ├── cv/                            # 主简历（PDF 或 .tex）
│   ├── linkedin/                      # LinkedIn 个人资料导出（PDF）
│   ├── diplomas/                      # 学位证书和成绩单
│   ├── references/                    # 推荐信
│   └── applications/                  # 过往申请记录（<公司>_<职位>/）
├── .github/workflows/ci.yml           # CI：LaTeX 冒烟编译、技能 lint、CLI 类型检查
├── salary_lookup.py                   # 薪资基准对比工具（自带数据）
├── tools/
│   ├── check_framework_version.py     # CI 检查：技能文件变更时需提升 framework_version
│   ├── check_upstream_updates.py      # 预览上游更新会影响哪些你的个性化文件
│   ├── convert_salary_excel.py        # 将薪资 Excel 转换为 JSON
│   ├── fetch_page.py                  # 针对 403 的仅 GET 浏览器请求头重试（遵守 robots.txt）
│   ├── install_portal_clis.py         # 安装所有发现的招聘网站 CLI（bun install）
│   ├── lint_skills.py                 # CI 对技能、命令、settings.json 的 lint
│   ├── posting_guard.py               # 完整性检查：剥离隐藏文本、标记注入形态的语句
│   ├── robots_check.py                # 针对 robots.txt 对浏览器请求头重试进行把关
│   ├── security_guards.py             # CI 安全守卫：权限 allow/ask/deny、技能授权、gitignore、清单
│   ├── upstream_triage.py             # 将上游提交分为“值得审查”与“大概可跳过”
│   ├── verify_layout.py               # 测量编译后 PDF 的页面布局（空洞、孤行、页脚冲突）
│   ├── verify_pdf.py                  # 验证编译后 PDF 的页数和可提取文本
│   └── README_SALARY_TOOL.md          # 薪资工具设置说明
├── job_scraper/                       # 抓取器状态（已见职位、结果）
├── gmail_sync/                        # /gmail-sync 状态（已处理消息 ID、上次同步日期）
├── upskill/                           # /upskill 报告输出（每次运行的 markdown 报告）
├── job_search_tracker.csv             # 申请跟踪表格
└── SETUP.md                           # 详细设置指南
```

## `/apply` 的工作方式

`/apply` 命令运行一个带有强制 PDF 编译的**起草者—审阅者工作流**：

1. **解析**招聘信息（URL 或文本）
2. 对照你的资料**评估匹配度**（技能、经验、文化、地点、职业方向契合度）
3. 用 LaTeX **起草**量身定制的简历和求职信
4. **生成一个审阅者 agent**，由它调研公司并对草稿提出批评
5. 根据审阅者的反馈**修改**
6. **编译并检查**两份 PDF：简历用 lualatex，求职信用 xelatex。Claude 会读取渲染后的页面并迭代修改 LaTeX，直到简历恰好 2 页、没有孤立的条目标题，求职信恰好 1 页、签名可见且字体一致。
7. **对简历做 ATS 检查**：提取 PDF 的文本层（`pdftotext`，可选依赖），按 ATS 解析器的视角进行验证——联系方式以字面文本存在、无乱码字形、阅读顺序合理——然后对照提取结果评估招聘信息的关键词覆盖率。你的资料确实具备的关键词会被加入；确实存在的差距会保留可见，绝不堆砌。
8. **展示**最终成品并附验证清单。

简历和求职信中的所有表述都会对照你的真实资料进行核实。系统绝不会捏造技能或经验。

### 该工作流的不同之处

- **PDF 验证循环。** 大多数 LaTeX 简历模板产出的结果“.tex 里看着没问题”，到了 PDF 却出问题：职位标题孤行到下一页、求职信溢出到第 2 页、项目符号字体悄悄回退为正文字体。`/apply` 命令会编译并目视检查每一份 PDF，施加针对性修复（`\needspace`、`\enlargethispage`、为列表项匹配字体的包装命令），直到版式干净。每次申请都会自动执行。
- **基于 PDF 文本层的 ATS 验证。** ATS 读取的是 PDF 内嵌文本，而非渲染后的页面——而 LaTeX 可能悄悄生成文本提取出来是乱码的 PDF（本应是邮箱的位置出现图标字形、多栏布局导致行交错）。`/apply` 用 `pdftotext` 提取编译后简历的文本层，按解析器实际所见验证联系方式、阅读顺序和招聘关键词覆盖率。诚实规则被强制执行：资料不具备的关键词会被承认为差距，绝不硬塞进去。
- **按相关性加权的简历裁剪。** 当简历超过 2 页时，工作流不会机械地从“最旧”的部分开始删。它会按 (a) 与目标招聘信息的相关性、(b) 在文档中的独特性、(c) 求职信是否依赖它，给每一行候选内容打分，优先删除总分最低的行。一条命中招聘关键词的较早职位条目，会比一条不命中关键词的近期职位条目更优先保留。
- **起草者—审阅者分离。** 起草者负责写作；另一个以全新上下文生成的 Claude agent 负责调研公司并批评草稿。然后由起草者修改。这能捕捉到单轮处理常遗漏的关键词、薄弱的表述框架和泛泛之谈。
- **节省 token 的审阅者调度。** 审阅者 agent 直接内联接收草稿，而无需重新读取；验证清单在工作流末尾只运行一次，而不是由两个 agent 重复执行。注意：第 5 步新增的编译与检查步骤会把省下的一部分开销花在 PDF 渲染和版式迭代上——该工作流以一定的端到端 token 成本，换取了真正减少有问题的 PDF 到达用户手中。

## 自定义

### 哪些文件需要手动编辑

如果你更愿意直接编辑文件而不是使用 `/setup`：

| 文件 | 修改内容 |
|------|---------------|
| `CLAUDE.md` | 你的完整资料（姓名、教育、经历、技能、目标） |
| `01-candidate-profile.md` | 你简历数据的结构化版本 |
| `02-behavioral-profile.md` | 你的行为测评或自我评估 |
| `04-job-evaluation.md` | 技能匹配领域、职业目标、动机筛选条件 |
| `05-cv-templates.md` | 不同职位类型的个人简介模板 |
| `07-interview-prep.md` | 你来自真实经历的 STAR 案例 |
| `search-queries.md` | 针对你的技能和地点的求职搜索词 |

### 更新搜索词

随着你的优先级变化，你可以只重新配置求职搜索，而不必重新运行完整的资料设置：

```
/setup --section search
```

这会重新运行搜索配置访谈：目标职位、要搜索的技能、地点以及招聘网站。它还会根据你的资料推荐你可能未曾考虑过的职位类型。

### 自定义模板

简历使用 [moderncv](https://ctan.org/pkg/moderncv)（banking 风格）。求职信使用带有 Lato/Raleway 字体的自定义 `cover.cls`。两者都是 LaTeX——这是本仓库自带并维护的参考引擎。

若要改用你自己的模板——LaTeX、[Typst](https://typst.app/)，或任何可从命令行编译为 PDF 的工具链——请运行：

```
/add-template
```

将它指向你的源文件（一个 `.tex` 文件及任何 `.cls`/`.sty` 文件或附带字体；一个 `.typ` 文件及任何本地包；或其他工具链的等价物）。该命令会通过访谈了解模板的说明——源文件扩展名、编译命令、字体及其位置、需保留的样式规则、硬性页数限制——将所有内容存储在 `templates/` 下，运行一次强制的测试编译，并激活该模板，使 `/apply` 基于它起草和编译。模板以 `[PLACEHOLDER]` 标记代替个人数据存储，因此可以安全地提交和分享。

- `/add-template --list` 显示已注册的模板
- `/add-template --use <名称>` 在模板之间切换
- `/add-template --use default` 恢复为自带的 moderncv / cover.cls 模板

如果你更愿意手动操作，手动方式依然可行：更新 `05-cv-templates.md` 和 `06-cover-letter-templates.md` 中的指引。

### 求职搜索工具

`.agents/skills/` 中的四个丹麦 CLI 工具（Jobbank、Jobdanmark、Jobindex、Jobnet）演示了为特定市场构建招聘网站集成的模式。如果你在其他国家，请运行：

```
/add-portal
```

提供你本地招聘网站的 URL。该命令会调研该网站（搜索 URL 模式、结果页结构、robots.txt/访问规则），搭建一个与自带工具具有相同结构、命令和输出契约的 CLI 技能，并在注册前进行一次真实查询的试运行。需要登录的网站会被拒绝，条款限制严格的网站会在生成的技能中附上醒目的“仅供个人使用”警告。生成的技能是特定于市场的，存在于你的 fork 中；生成器本身才是通用的部分。

在维护一个适配你所在市场或语言的 fork？请把它添加到 [社区 fork 与适配](https://github.com/MadsLorentzen/ai-job-search/discussions/78) 讨论帖中，方便他人找到。

对于丹麦以外**与国家无关**的起点，仓库在丹麦示例之外还自带两个招聘网站技能：

- **`linkedin-search`** —— 基于 LinkedIn 公开的、无需认证的 `jobs-guest` 端点。与领域无关，**零运行时依赖**（仅需 `bun` 即可运行），并将搜索地点作为显式参数，因此开箱即用地适用于任何市场（`-l "Berlin, Germany"`、`-l "Mumbai, Maharashtra, India"`、`-l "Remote"`……）。**仅供个人使用**——自动化访问违反 LinkedIn 的服务条款，因此请保持低访问量。见 `.agents/skills/linkedin-search/SKILL.md`。
- **`freehire-search`** —— 查询 [freehire.me](https://freehire.me) 聚合器的公开 REST API（JSON，无需 API 密钥）。聚焦技术领域（软件、数据、工程、DevOps、远程），可通过 facet 参数（`--region`、`--country`、`--remote`）覆盖多个市场，且**零运行时依赖**。与抓取 HTML 的丹麦网站不同，返回结果是结构化的（技能、资历、类别）。后端采用 MIT 协议且[可自托管](https://github.com/strelov1/freehire)——如果你愿意，可将 `FREEHIRE_API_URL` 指向你自己的实例。见 `.agents/skills/freehire-search/SKILL.md`。

### 扩展框架：招聘网站、模板、标准——以及借鉴其他 fork

以上内容合起来构成了一个扩展模型，这里把它明确说明。框架有三个扩展点，都不需要改动上游：

1. **招聘网站技能** —— 招聘网站的模块系统。每个 `*-search` 技能都是 `.agents/skills/` 下一个自包含的文件夹，遵循相同的契约（一个 `search`/`detail` CLI、`--format json|table|plain` 输出、其 `SKILL.md` 中的 `enabled:` 标志、自带测试）。`/scrape` 会自动发现任何遵循该契约的已安装技能——无需注册，无需接线。`/add-portal` 可生成新技能；[社区招聘网站索引](https://github.com/MadsLorentzen/ai-job-search/discussions/78)收录了其他 fork 构建的技能。
2. **文档模板** —— `/add-template` 可注册任何能从命令行编译为 PDF 的简历或求职信工具链，无论是否为 LaTeX。
3. **评估标准** —— 你资料中的一票否决项和偏好项是自由格式的，评估准则会对照你填写的任何内容打分。“优厚的育儿假条款”、“按工会标准薪资不低于 X”、“不接受值班待命”——每一项只需一行资料、无需代码，且在 `/rank` 和 `/apply` 的匹配度评估中具有实际权重。语言是唯一拥有专门结构化处理的否决项类型：`/setup` 会把你使用的每门语言及水平（直接询问，或从你的简历/LinkedIn 导出中推断）录入一张 `Languages` 表，语言门槛（`04-job-evaluation.md`）会硬性否决要求一门你完全未申报语言的招聘信息；而对于你确实使用的语言、但要求的水平高于你申报水平的情况，则只标记而不自动否决，这样临界情况（比如严格的“流利”门槛对照你自己的 B1/B2）会交由你判断，而不是被悄悄淘汰。

**从其他 fork 借鉴招聘网站技能**是获取上游未自带网站的预期方式：在[招聘网站索引](https://github.com/MadsLorentzen/ai-job-search/discussions/78)中找到它，打开对应 fork，把那一个文件夹复制到你自己的 `.agents/skills/` 中。运行之前：

- **阅读代码。** 全部读完——这些 CLI 会在你的机器上（`.claude/settings.json` 将它们加入白名单）针对你的职业数据预先批准运行。检查其网络调用是否只发往它声称搜索的招聘网站，`package.json` 是否没有 `dependencies` 且没有生命周期脚本（`postinstall` 等），以及是否没有任何读写操作超出其自身文件夹。
- **离线运行其测试**（在技能的 `cli/` 目录中运行 `bun test`）——一个构建良好的技能，其测试无需联网即可通过。
- 检查 `enabled:` 标志和该技能自己的服务条款说明。

复制步骤有意设计为手动完成。你的设置已允许已安装的招聘网站技能无需每次询问即可运行——因此，一个替你从第三方仓库获取它们的安装器，会跳过唯一重要的检查：你本人先阅读代码。这样的安装器并不存在，这是一个安全决策，而非功能缺失。

特定市场的*数据源*（国家级薪资数据库、本地的薪资标准表）遵循与招聘网站相同的模式：它们应放在市场 fork 中，通过 [#78](https://github.com/MadsLorentzen/ai-job-search/discussions/78) 分享，而不是进入上游。

### 薪资基准对比

薪资工具可配合你提供的任何薪资数据使用（工会统计数据、Glassdoor 导出、个人调研等）。预期格式和设置见 `tools/README_SALARY_TOOL.md`。如果你没有薪资数据，薪资步骤将直接跳过。

### 重新开始

要清除你的资料数据并重新开始：

```
/reset profile    # 清除技能文件，保留框架规则
/reset documents  # 删除 documents/ 文件夹中的文件
/reset all        # 两者都执行
```

`/reset` 会明确显示将要删除的内容，并要求你输入 `RESET` 确认。在你确认之前不会删除任何东西。

### 保持更新

上游迭代很快。与其拉取原始的 `master` 然后听天由命，不如把你的 fork 更新到一个带标签的[发布版本](../../releases)——这是 [CHANGELOG.md](CHANGELOG.md) 中说明的、经过审核的检查点。合并前，`python3 tools/check_upstream_updates.py` 可精确预览更新会影响你的哪些个性化文件；`python3 tools/upstream_triage.py` 会把你落后的提交分为“值得审查”与“大概可跳过”（每周执行的工作流可以把它发布到一个滚动更新的 issue 中）。完整操作说明见 [SETUP.md 第 8 节](SETUP.md#8-pulling-upstream-updates-into-your-fork)。

## 获得更好结果的建议

### 资料深度至关重要

影响输出质量的最大单一因素，是你在资料中投入的细节多少。单薄的资料产出千篇一律的申请；详尽的资料才能实现真正量身定制的结果。

- **职位描述：** 不要只罗列职位名称。描述你在每个职位上实际做了什么：具体项目、使用的工具、承担的职责和可量化的成就。你提供的素材越多，系统就越能精确地针对不同职位重新组织你的经历。
- **结合语境的技能：** 不要只列出“Python”或“项目管理”，而要描述你在何处、如何运用它们。“用 Python 基于 scikit-learn 构建客户流失预测的机器学习流水线”，远比“Python、机器学习”给系统提供更多可用素材。
- **所有引导路径都有效：** 无论你让 `/setup` 读取 `documents/` 文件夹、粘贴单份简历，还是通过访谈完成，原则都一样：更丰富的输入产出更出色的输出。

### 职业路径发现

框架支持两种不同的求职模式：

- **显式目标定位：** 你清楚自己想要哪些职位或行业。系统帮助你根据匹配度进行细化和优先级排序。
- **潜在机会发现：** 通过分析你的完整经历（不仅是职位名称，还包括你实际做过的工作），系统可以呈现你未曾考虑过的职业路径。映射到意想不到行业的可迁移技能、你曾享受或擅长之事的规律，或是结合你的领域专长与新技术的新兴职位。

要充分利用这一点，请在 `/setup` 时花时间不仅描述你的经历，还要描述什么让你充满干劲、什么让你疲惫消耗，以及你希望更多地接触什么。这些背景信息会直接影响系统评估匹配度的方式，以及它在 `/scrape` 中呈现的职位。

## 贡献

考虑提交 PR？请先阅读 [CONTRIBUTING.md](CONTRIBUTING.md)——它说明了什么内容会被合并、什么应该放在 fork 中，以及背后的原因。

## 致谢

- [Mikkel Krogholm](https://github.com/mikkelkrogsholm)（[skills 仓库](https://github.com/mikkelkrogsholm/skills)）提供的求职搜索 CLI 技能
- 由 [Anthropic](https://anthropic.com) 的 [Claude Code](https://claude.com/claude-code) 构建

## 许可证

MIT
