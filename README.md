<p align="center">
  <img src="./assets/readme/hero.svg" width="100%" alt="public-ready：上线前扫一遍用户能看到的每一句话，输出带文件行号的对账表">
</p>

<p align="center">
  <img src="https://img.shields.io/badge/License-MIT-5AD8A6?style=flat-square" alt="License MIT">
  <img src="https://img.shields.io/badge/Skill-v1.1.0-BCC5D0?style=flat-square" alt="Skill version 1.1.0">
</p>

<p align="center">
  <a href="#它在找什么">它在找什么</a> ·
  <a href="#一次扫描长什么样">扫描长什么样</a> ·
  <a href="#这是什么">这是什么</a> ·
  <a href="#工作流程">工作流程</a> ·
  <a href="#快速使用">快速使用</a>
</p>

---

## 它在找什么

代码跑通了，不代表这个页面见得了人。

<p align="center">
  <img src="./assets/readme/pain-points.svg" width="100%" alt="六组上线前原文与改写后对照：AI 味空词、emoji 功能列表、张三假评价、泛化成功提示、甩锅式报错、技术词外露">
</p>

<br>

**AI 味。** 现在项目里的文案，大半是 AI 写的。语法正确、态度积极、读起来顺——问题是把产品名换成任意一个别的产品，那句话照样成立。「让校园生活更简单」「一站式解决方案」「探索无限可能」，✨🚀💡 开头的三个功能卡片，评价区里的「产品经理 · 张三」，点完保存弹一句「太棒了！」。用户认得出来，而且认出来的那一刻就默认这个产品也是随手做的。

**没做完的痕迹。** 演示用的假数据没清干净，跟着 SSG、store 初始值、fallback 一路渲染到线上；`XXX 系统`的 XXX 还在；页面某处渲染出 `undefined`。有环境开关也不保险，得追数据流。

**技术词直出。** 「token 已过期」「参数校验失败」「接口超时」。写的人知道什么意思，看的人不知道。

**报错不说人话。** 「系统繁忙，请稍后重试」既不说发生了什么，也不说用户能做什么。后端的原始报错直接透传给用户，更糟。

**全站对不上。** 首页说「您」，个人中心说「你」；前台卖萌，管理端公文腔，错误页又是第三种语气。每一处单看都没问题，连起来就不像一个产品。

**写死的东西。** 域名、客服邮箱、版本号、活动截止日期，散在十几个组件里。

## 一次扫描长什么样

产出是一张对账表，每条发现六个字段，缺一不可：

| 位置 | 原文 | 分类 | 问题 | 建议 | 优先级 |
| --- | --- | --- | --- | --- | --- |
| `app/page.tsx:24` | `让校园生活更简单` | E | 换成任何一个产品名这句都成立，用户读完不知道能拿它干嘛 | 改为「查成绩、看课表、抢课，都在这儿」 | 🟠 上线前应改 |
| `components/pay-form.tsx:112` | `参数校验失败` | B | 付款页最后一步，用户不知道哪里填错了，大概率直接放弃 | 改为「银行卡号看起来不太对，请检查后重填」 | 🔴 上线阻塞 |
| `lib/mock/reviews.ts:8` | `产品经理 · 张三：彻底改变了我的工作方式！` | A+E | 假评价经 SSG 进入生产页面，既是 AI 味也是合规风险 | 换成拿到授权的真实用户，或去掉人名只写场景 | 🔴 上线阻塞 |

默认到此为止 —— 表交给你，改不改、怎么改你说了算。

<details>
<summary>看完整的报告结构</summary>

<br>

1. **本次扫描基于的假设**（画像没来得及请你确认时才有，放最前面）
2. **概述**：扫了哪些目录、哪些没覆盖、产品初衷、发现数（合并前 / 合并后）、各分类与各优先级计数
3. **对账表**：按优先级从高到低
4. **全站性发现**：称呼混用、语气割裂、全站 AI 味这类单个模块看不出来的问题
5. **优先级建议**
6. **改法示例**：每类给 1–2 条

发现超过 15 条会写成 `public-ready-audit-<日期>.md`（写盘前问你放哪），对话里只给概述和最高优先级的 10 条 —— 几十条表格贴在聊天框里没人读得完。

一条都没发现时也会给一份「通过」报告，写清扫了多少文件、五类分别核对了什么，而不是回你一句「没问题」让你怀疑是不是跑挂了。

</details>

## 这是什么

一个面向网站和 Web 应用的发布前文案审计 Skill。它先弄清产品定位、目标用户和语气基准，再扫描最终渲染到界面上的文本，找出不适合上线的部分。

适用于 Claude Code、Codex、Cursor、Hermes、Trae 等能读取 Agent Skill 的工具。在支持 subagent 的 Agent 上按模块并行扫描；不支持的会自动走顺序分批的降级路径，结果一样，只是慢一些。

<details>
<summary>分类与优先级</summary>

<br>

**分类说的是「这是什么毛病」**，一条发现可以同时属于多类，用 `+` 连接：

| 分类 | 覆盖范围 |
| --- | --- |
| **A** 不适合直接上线 | 未完成内容、占位、调试残留、内部信息泄露、假数据当真实数据渲染 |
| **B** 用户不友好 | 技术词外露、公文腔、模糊或甩锅式提示、报错说不清下一步 |
| **C** 与初衷不符 | 语气、称呼、自称、定位和价值主张与产品初衷矛盾，或全站彼此矛盾 |
| **D** 硬编码 | 应配置化、复用或进入 i18n 的文本 |
| **E** AI 味 | AI 生成的万能句式、模板结构、emoji 项目符号、张三李四假数据、泛化情绪词 |

**优先级说的是「这有多急」**，独立判定，不照分类字母套：

| 优先级 | 什么情况 |
| --- | --- |
| 🔴 上线阻塞 | 泄露内部信息、假数据被当真的、用户看不懂就走不下去、合规风险、页面渲染 `undefined` |
| 🟠 上线前应改 | 用户看得懂但会困惑或做错动作、报错缺「能做什么」、关键路径上的 B 类或 E 类 |
| 🟡 建议改 | 不影响理解，影响一致性和观感 |
| ⚪ 可分批 | 用户无感的维护性问题 |

分开的原因很实际：付款页上一句「参数校验失败」是 B 类，但它是上线阻塞。照字母套优先级，这条就会被排到第二梯队。

</details>

## 工作流程

<p align="center">
  <img src="./assets/readme/workflow.svg" width="100%" alt="工作流程七个阶段：探索项目、建立画像、拆分扫描、五类核对、跨模块比对、汇总对账表，最后的确认后修改为虚线，表示默认不执行">
</p>

其中**跨模块比对**这步容易被忽略但不能省：重复文案要在全项目计数，称呼和语气要跨模块比才看得出不一致 —— 分片扫描的每个 subagent 只看得见自己那块目录，结构上就发现不了这类问题。所以各分片除了回报命中项，还要回一份「全局指纹」（重复串、称呼用词、语气样本、产品自称），由主 Agent 合起来判断。

### 扫描范围

- 页面和组件中的文本节点、按钮、标题、占位符、`aria-label` 与 `alt`
- toast、空状态、错误页和用户可见报错
- Hero 标题、功能卡片、CTA、about 页 —— AI 味的重灾区
- i18n / locale / messages 等语言文件
- 可能透传到前端的后端错误信息
- 路由、菜单、页脚、联系方式、版本号等配置文本
- 可能被渲染为真实内容的 mock 或静态示例数据

基础设施日志、测试断言和仅开发者可见的内部代码，不会因为关键词命中就被误判为用户文案。

## 快速使用

### 1. 安装

**推荐：npx skills（自动适配 70+ 种 Agent：Claude Code、Cursor、Codex、Zed、Trae……）**

```bash
# 全局安装（所有项目可用）
npx skills add jry21223/public-ready -g

# 只装到当前项目
npx skills add jry21223/public-ready

# 指定目标 Agent（如只装给 Claude Code）
npx skills add jry21223/public-ready -a claude-code
```

CLI 会自动检测你已安装的 Agent 并装到对应 skills 目录（加 `-y` 跳过交互确认），装完直接用自然语言发起审计即可。

**手动安装（git clone）**

<details>
<summary>Claude Code / 直接读取文件</summary>

<br>

**Claude Code（全局可用）**

```bash
git clone https://github.com/jry21223/public-ready.git ~/.claude/skills/public-ready
```

**Claude Code（只在当前项目可用）**

```bash
git clone https://github.com/jry21223/public-ready.git .claude/skills/public-ready
```

**其他 Agent**：把下面两个文件直接交给它读取。

```text
https://raw.githubusercontent.com/jry21223/public-ready/main/SKILL.md
https://raw.githubusercontent.com/jry21223/public-ready/main/references/stiff-copy-patterns.md
```

</details>

### 2. 用自然语言发起审计

```text
帮我检查这个项目的用户可见文案是否适合上线。
先建立产品画像，再按 public-ready 的规则输出对账表，不要直接修改代码。
```

不需要记住专业术语。下面这些表达也会触发审计：

<details>
<summary>查看常见触发方式</summary>

<br>

- 「这个页面的文案 AI 味太重了，帮我看看哪些要改」
- 「帮我做一下发布前准备」
- 「这个页面能上线吗？」
- 「文案读起来像程序员写的」
- 「检查一下提示语和报错信息」
- 「看看有没有占位、测试数据或写死的内容」

</details>

## 审计原则

1. **只看用户可见内容**：grep 命中只是线索，不等于问题。测试、构建配置和内部日志不算发现。
2. **报错必须可行动**：至少说清发生了什么，以及用户下一步能做什么。后端透传的原始错误不直出给用户。
3. **AI 味按「换名测试」判**：把产品名换成竞品，这句话还成立吗？成立才算。刻意选择的活泼语气不是 AI 味 —— AI 味的特征是模板化，不是热情。
4. **语气以画像为准**：判断「和产品初衷不符」必须有依据，不靠感觉。画像会先给你确认；确认不了就标成假设写进报告，而不是卡在那里等你。
5. **不动你的东西**：默认只出表，不改代码。画像文件和报告文件要落盘，也会先问你一句 —— 跑一次只读审计不该多出一个 git diff。
6. **并行扫描、集中汇总**：按模块分给 subagent，主 Agent 只负责定范围、跨模块比对和最终判断，避免大量文案挤进同一个上下文影响分类。

## 目录结构

```text
public-ready/
├── README.md
├── SKILL.md
├── CHANGELOG.md
├── LICENSE
├── assets/
│   └── readme/
│       ├── hero.svg
│       ├── pain-points.svg
│       └── workflow.svg
└── references/
    └── stiff-copy-patterns.md
```

- [`SKILL.md`](./SKILL.md)：完整工作流、分类与优先级规则、subagent 机制、跨模块比对和自查清单
- [`references/stiff-copy-patterns.md`](./references/stiff-copy-patterns.md)：五类特征库，关键词线索、典型问题与改写方向
- [`CHANGELOG.md`](./CHANGELOG.md)：版本变更

## License

[MIT](./LICENSE) © jry21223
