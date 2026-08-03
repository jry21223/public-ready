<p align="center">
  <img src="./assets/readme/hero.svg" width="100%" alt="public-ready：扫描用户可见文案并输出发布前对账表">
</p>

<p align="center">
  <strong>让用户看到产品，而不是看到开发过程。</strong>
</p>

<p align="center">
  <a href="#快速使用">快速使用</a> ·
  <a href="#真实使用案例">真实案例</a> ·
  <a href="#四类审计">四类审计</a> ·
  <a href="#工作流程">工作流程</a>
</p>

---

## 真实使用案例

<p align="center">
  <img src="./assets/readme/henu-kit-audit-example.svg" width="100%" alt="public-ready 对 HENU Kit 的真实审计结果节选，展示扫描概述和四类代表性发现">
</p>

上图来自 `public-ready` 对 **HENU Kit** 的一次真实扫描：覆盖 Portal、Console、旧学习平台、QuizCraft 和后端透传错误文案，同类合并后发现 **66 条**问题，其中 **12 条属于上线阻塞项**。

截图选择了四类最有代表性的发现：生产示例数据的合规风险、面向学生暴露内部配置、产品声明缺失，以及第三方地址硬编码。它们同时展示了这个 Skill 的核心能力：**理解产品背景、追踪真实渲染路径、给出文件级证据，并提供可执行修改建议。**

> 图片是对真实运行输出的适配排版，内容来自实际扫描结果；完整输出仍以 Markdown 对账表交付。

## 输出格式

`public-ready` 不只告诉你“这句话不太好”，而是给出可定位、可判断、可执行的对账结果：

| 位置 | 原文 | 分类 | 问题 | 建议 | 优先级 |
| --- | --- | --- | --- | --- | --- |
| `src/pages/Login.tsx:88` | `token 已过期` | B | 暴露内部术语，用户不知道下一步做什么 | 改为「登录已过期，请重新登录」 | 🟠 上线前应改 |

> 默认只输出对账表，不直接修改代码。用户确认建议后，Agent 才进入修改阶段。

## public-ready 是什么

`public-ready` 是一个面向网站和 Web 应用的**发布前文案审计 Skill**。它会先理解产品定位、目标用户和语气基准，再扫描最终呈现在界面上的文本，找出不适合直接上线的内容。

适用于 Claude Code、Codex、Cursor、Hermes、Trae 等能够读取 Agent Skill 的工具，不绑定特定 Agent 实现。

## 快速使用

### 1. 把 Skill 文件交给 Agent

```text
https://raw.githubusercontent.com/jry21223/public-ready/main/SKILL.md
https://raw.githubusercontent.com/jry21223/public-ready/main/references/stiff-copy-patterns.md
```

### 2. 用自然语言发起审计

```text
帮我检查这个项目的用户可见文案是否适合上线。
先建立产品画像，再按 public-ready 的规则输出对账表，不要直接修改代码。
```

不需要记住专业术语。下面这些表达也会触发审计：

<details>
<summary>查看常见触发方式</summary>

- 「帮我做一下发布前准备」
- 「这个页面能上线吗？」
- 「文案读起来像程序员写的」
- 「检查一下提示语和报错信息」
- 「看看有没有占位、测试数据或写死的内容」

</details>

## 四类审计

| 分类 | 检查什么 | 典型问题 |
| --- | --- | --- |
| 🔴 **A · 不适合直接上线** | 未完成内容、调试残留、内部信息泄露 | `TODO`、`lorem`、测试数据、`localhost`、异常堆栈直出 |
| 🟠 **B · 用户友好度** | 技术词、公文腔、模糊或甩锅式提示 | 「参数校验失败」「接口超时」「token 过期」「系统繁忙」 |
| 🟡 **C · 产品初衷一致性** | 语气、称呼、定位和价值主张是否一致 | 正式平台突然卖萌、首页只报系统名、不说明能解决什么 |
| ⚪ **D · 硬编码与可维护性** | 应配置化、复用或进入 i18n 的文本 | 固定 URL、环境名、版本号、联系方式、重复文案 |

同一问题可以组合分类，例如 `A+D`：既阻塞上线，也属于硬编码。

## 工作流程

```text
探索项目
  ↓
建立产品画像
  ↓
按用户可见面拆分扫描
  ↓
用特征库核对 A / B / C / D
  ↓
汇总为可追溯对账表
  ↓
用户确认后再修改
```

### 扫描范围

- 页面和组件中的文本节点、按钮、标题、占位符、`aria-label` 与 `alt`
- toast、空状态、错误页和用户可见报错
- i18n / locale / messages 等语言文件
- 可能透传到前端的后端错误信息
- 路由、菜单、页脚、联系方式、版本号等配置文本
- 可能被渲染为真实内容的 mock 或静态示例数据

基础设施日志、测试断言和仅开发者可见的内部代码，不会因为关键词命中就被误判为用户文案。

## 输出契约

每条发现必须包含：

```text
位置（文件:行号）
原文
分类（支持 A+D 等组合）
问题
建议
优先级
```

优先级分为：

| 等级 | 含义 |
| --- | --- |
| 🔴 上线阻塞 | 不修不应发布 |
| 🟠 上线前应改 | 直接影响用户理解或操作 |
| 🟡 建议改 | 影响产品一致性和体验 |
| ⚪ 可分批 | 主要是配置化与长期维护问题 |

## 审计原则

1. **证据优先**：每条结论必须引用具体文件、行号和原文。
2. **只看用户可见内容**：grep 命中只是线索，不等于问题。
3. **先理解产品，再判断语气**：C 类问题必须以产品画像为依据。
4. **报错必须可行动**：至少说明发生了什么，以及用户下一步能做什么。
5. **默认不静默改码**：先给清单，获得确认后再修改。
6. **并行扫描，集中汇总**：按模块交给 subagent，主 Agent 负责范围和最终判断。

## 目录结构

```text
public-ready/
├── README.md
├── SKILL.md
├── assets/
│   └── readme/
│       ├── hero.svg
│       └── henu-kit-audit-example.svg
└── references/
    └── stiff-copy-patterns.md
```

- [`SKILL.md`](./SKILL.md)：完整工作流、分类规则、subagent 机制和自查清单
- [`references/stiff-copy-patterns.md`](./references/stiff-copy-patterns.md)：关键词线索、典型问题与改写方向

## License

MIT
