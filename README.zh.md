<div align="center">

**中文** · [English](./README.md)

# 🎯 Job Hunt List

**把一份简历或目标方向，变成可搜索的职位数据库。**

发现 · 去重 · 核验 · 排序 · 持续更新

[![License](https://img.shields.io/badge/LICENSE-MIT-4c8bf5?style=flat-square&labelColor=333)](./LICENSE)
[![Version](https://img.shields.io/badge/VERSION-1.0.0-2ea44f?style=flat-square&labelColor=333)]()
[![Output](https://img.shields.io/badge/OUTPUT-HTML-f59e0b?style=flat-square&labelColor=333)]()
[![Stars](https://img.shields.io/github/stars/yanliudesign/job-hunt-list?style=flat-square&label=STARS&color=e37f2c&labelColor=333)](https://github.com/yanliudesign/job-hunt-list/stargazers)

[![Claude Code](https://img.shields.io/badge/Claude_Code-Skill-d97757?style=flat-square&labelColor=1a1a1a&logo=anthropic&logoColor=white)](https://claude.ai/code)
[![Codex](https://img.shields.io/badge/Codex-Skill-2ea44f?style=flat-square&labelColor=1a1a1a)]()
[![OpenCode](https://img.shields.io/badge/OpenCode-Skill-4c8bf5?style=flat-square&labelColor=1a1a1a)]()
[![OpenClaw](https://img.shields.io/badge/OpenClaw-Skill-8b5cf6?style=flat-square&labelColor=1a1a1a)]()
[![Hermes](https://img.shields.io/badge/Hermes-Skill-e879a8?style=flat-square&labelColor=1a1a1a)]()

</div>

> 📦 也包含在完整的 **[offer-toolkit-skill](https://github.com/yanliudesign/offer-toolkit-skill)** 里，一次安装即可串起 Search · JD · Resume · BQ 全流程。

一个专门用来建立和维护求职岗位清单的 agent skill。给它你的简历、目标方向、一份种子 JD，或者一批已有职位链接，它会搜索公开来源、去重、区分事实与推断、根据真实简历证据排序，并生成一份可以在本地搜索筛选的单文件 HTML 报告。

它和普通 shortlist 最大的区别是：**不只留下 5–10 个赢家，而是保留所有通过硬条件的唯一岗位。**这份清单可以持续维护：今天广泛发现，明天深评重点岗位，下周把新结果继续合并进来。

---

## 怎么工作

```text
简历 / 目标方向 / 种子 JD / 已有职位链接
                      ↓
                   搜索画像
                      ↓
              6–12 组互补公开查询
                      ↓
          采集 → 标准化 → 去重 → 证据核验
                      ↓
             完整 JD 岗位做深度匹配
                      ↓
             可搜索的单文件 HTML 清单
```

1. **建立搜索画像** — 目标 title、相邻 title、level、地点、工作方式、核心能力、领域和排除项。
2. **执行查询矩阵** — 精确 title、相邻 title、能力导向、领域导向、scope 导向和公司导向。
3. **采集公开证据** — 公司招聘页、可访问的 LinkedIn 页面和公开搜索结果。
4. **严格去重** — 优先 canonical job ID / URL；缺少 ID 时使用公司 + title + 地点。
5. **区分事实和推断** — 每个字段独立判断证据状态，不因为链接能打开就默认整行可信。
6. **排序但不删候选池** — JD 完整的岗位做深评，其余只做方向性排序。
7. **生成并验证 HTML** — 搜索、筛选、领域标签、适配理由、Gap、具体职位链接和行数一致性检查。

---

## 最终会拿到什么

默认交付物是一份单文件 HTML：

```text
job-hunt-list-<候选人或主题>-<YYYYMMDD>.html
```

双击即可本地打开，不需要 server、前端框架、远程字体或 build step。

| 报告区域 | 包含内容 |
|---|---|
| **搜索上下文** | 候选人方向、地点、时间范围、默认假设、来源和生成时间 |
| **总览** | 唯一职位数、已深评职位数 |
| **搜索与筛选** | 全文搜索，以及已深评、优先关注、AI / Agentic、Principal、Staff 等适用筛选 |
| **职位行** | 职位、公司、领域标签、推荐理由、主要 Gap、发布日期、地点、工作方式、薪资和匹配度 |
| **证据说明** | “未深评不等于不匹配”，未知字段没有被猜测补齐 |
| **搜索日志** | 执行过的 query、来源、核验时间和登录墙 / 限流情况 |

领域标签紧邻公司名并且可搜索。默认不在职位行显示 Tier A / Tier B 标签，但内部优先级仍可用于排序和筛选。

---

## 先看证据，再谈信心

公开职位页经常不完整：有的能看到薪资却看不到工作方式，有的只有 title 和公司，完整 JD 被登录墙挡住。这个 skill 不会因为一个页面能打开，就把整行信息全部当作事实，而是**逐字段判断证据**。

| 状态 | 含义 | 输出规则 |
|---|---|---|
| `verified` | 公开职位页或公司页直接可见 | 正常展示 |
| `partial` | 只能看到截断摘要 | 必要时标注证据有限 |
| `inferred` | 根据 title、公司或邻近信号推断 | 明确标为方向性推断 |
| `unknown` | 没有可靠证据 | 留空或显示“待核验”，绝不补猜 |

未知薪资留空；未知发布日期、地点和工作方式标“待核验”。Title 已核验，不代表薪资和工作方式也自动核验。

### 深度匹配公式

只有 JD 要求足够完整的岗位才会显示匹配区间：

```text
匹配度 = 0.60 × Must Have + 0.20 × Nice to Have + 0.20 × Hidden Signal Fit
```

报告展示区间，不展示伪精确小数。拿不到完整 JD 的岗位保持“未深评”，只给方向性理由，不伪造匹配百分比。

完整方法见 [`references/evidence-ranking.md`](references/evidence-ranking.md)。

---

## 直接这样说

不需要记命令，下面这些自然语言都会触发完整流程：

- “根据我的简历做一份 job hunt list。”
- “帮我找湾区或美国 remote 的 Principal / Staff Product Designer，整理成可搜索报告。”
- “根据这份 JD 和我的简历，找类似的 AI 设计岗位。”
- “把这些 LinkedIn 链接整理成一份去重职位清单。”
- “把这周的新岗位合并进上周的清单，保留首次发现时间。”
- “LinkedIn 被登录墙挡住了，继续查公开来源，但不要猜缺失信息。”

你可以从简历、目标方向、种子 JD、一批职位链接或旧版 Job Hunt List 中的任何一种材料开始。如果缺少的信息会明显改变搜索结果，skill 会一次只问一个关键问题。

---

## 搜索边界

这个 skill 负责搜索和分析，不替用户成为申请人。

- 不点击 Apply、不填写申请表、不发送消息
- 不索取密码、验证码、Cookie 或 session token
- 不绕过登录墙、CAPTCHA、限流、robots 或其他访问控制
- 不杜撰薪资、发布时间、工作方式、签证政策、岗位状态或 JD 要求
- 不因为一次访问失败就断言职位已经关闭

LinkedIn 不可访问时，会切换到公司招聘页、公开搜索结果，或生成可点击查询链接，并在报告里写明限制。

---

## 持续更新旧清单

Job Hunt List 不是一次性交付物，可以反复运行：

1. 解析旧清单里的 job ID 和 canonical URL。
2. 只新增真正的新岗位。
3. 把更强的证据合并到已有记录。
4. 保留 `first_seen`，更新 `last_checked`。
5. 汇总新增、更新和待复核数量。

某次来源暂时打不开时，不会仅凭这一点把岗位标为关闭。

---

## 安装

### 单独安装

克隆或下载这个仓库，然后把完整目录放进 agent 的 skills 目录：

```bash
git clone https://github.com/yanliudesign/job-hunt-list.git
```

请让 `SKILL.md`、`assets/`、`references/` 和 `evals/` 保持在同一目录结构下，确保相对引用正常工作。

### 作为 Offer Toolkit 使用

安装 **[offer-toolkit-skill](https://github.com/yanliudesign/offer-toolkit-skill)** 可以串起完整求职链路：

```text
Job Hunt List → 选中目标岗位
       ↓
JD Skill → 解码并决定该不该投
       ↓
Resume Skill → 定向改写和美化
       ↓
BQ Skill → 准备故事和行为面试
```

---

## 文件结构

```text
job-hunt-list/
├── SKILL.md                          # 流程、边界和输出契约
├── README.md                         # English documentation
├── README.zh.md                      # 中文文档
├── LICENSE                           # MIT
├── assets/
│   └── report-spec.md                # HTML 数据、交互和验证规范
├── references/
│   └── evidence-ranking.md           # 证据状态和排序公式
└── evals/
    └── evals.json                    # 回归测试场景
```

---

## 设计原则

1. **保留候选池，不只保留赢家。**今天待核验的岗位，补到 JD 后可能成为最强匹配。
2. **证据属于字段，不属于整行。**一个字段可见，不能让其他未知字段跟着“自动可信”。
3. **新鲜度只影响排序，不冒充匹配度。**刚发布但明显不相关的岗位，不该压过稍早但高度匹配的岗位。
4. **Unknown 是合法结果。**“待核验”比一个看似完整的猜测更有用。
5. **报告是工作工具。**高密度、可搜索、可持续更新，比装饰性 dashboard 更重要。

---

## 配套 Skill

- [offer-toolkit-skill](https://github.com/yanliudesign/offer-toolkit-skill) — Search · JD · Resume · BQ 完整工具包
- [job-description-skill](https://github.com/yanliudesign/job-description-skill) — 深度解码选中的单个岗位
- [resume-builder-skill](https://github.com/yanliudesign/resume-builder-skill) — 定向简历与打印级模板
- [Behavior-question-skill](https://github.com/yanliudesign/Behavior-question-skill) — 故事库与行为面试准备

---

## License

MIT — fork it, remix it, ship your own version.

Created by [Dreameryanyan](https://www.linkedin.com/in/yanliudesign/) ·
[LinkedIn](https://www.linkedin.com/in/yanliudesign/) ·
[X](https://x.com/yanliudreamer) ·
[小红书](https://www.xiaohongshu.com/user/profile/5b2afdf311be104ac3c22931)
