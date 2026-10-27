# cn-paper-writing

一个面向中文论文写作与审校的 **Claude Code Skill**，依据全国标准信息公共服务平台（std.samr.gov.cn）上现行有效的 GB/T 国家标准，指导中文论文的撰写、编排、参考文献著录和格式审查。

> 标准信息核对日期：**2026-10-27**。标准会修订，正式引用前请以平台最新状态为准。

## 特性

- **先判类型，再选标准**：学位论文、学术论文、科技报告、数据论文分别对应不同的分标准，避免混用。
- **区分约束强度**：严格区分“应”（要求）、“宜”（推荐）、“可”（允许），审稿时不把“宜”升级为“应”。
- **可执行审校流程**：从结构核对到参考文献逐条抽查，输出“位置 → 依据条款 → 修改建议 → 示例”的问题清单。
- **附带逐条要点与清单**：`references/` 下按标准拆分的要点摘要，`assets/checklist.md` 可直接复制使用。

## 覆盖的标准

| 论文/文献类型 | 现行标准 | 实施日期 |
|---|---|---|
| 学位论文（学士/硕士/博士） | GB/T 7713.1-2025 | 2026-02-01 |
| 学术论文（期刊/会议投稿） | GB/T 7713.2-2022 | 2023-07-01 |
| 科技报告 | GB/T 7713.3-2014 | 2014-11-01 |
| 数据论文 | GB/T 7713.4-2025 | 2026-03-01 |
| 参考文献著录 | GB/T 7714-2025 | 2026-07-01 |
| 文献摘要 | GB/T 6447-2025 | 2026-02-01 |
| 标点符号 | GB/T 15834-2011 | 2012-06-01 |
| 出版物上数字用法 | GB/T 15835-2011 | 2011-11-01 |

## 目录结构

```
cn-paper-writing/
├── SKILL.md                              # 技能主文件（含触发配置）
├── assets/
│   └── checklist.md                      # 可复制的审校清单
└── references/
    ├── standards-catalog.md              # 标准清单、状态、官方链接
    ├── thesis-gb7713-1-2025.md           # 学位论文逐条要点
    ├── article-gb7713-2-2022.md          # 学术论文逐条要点
    ├── references-gb7714-2025.md         # 参考文献著录规则与示例
    ├── abstract-gb6447-2025.md           # 摘要编写规则
    └── numbers-and-punctuation.md        # 数字用法与标点符号
```

## 安装

把整个目录放到用户级 skills 目录：

```bash
# macOS / Linux
cp -r cn-paper-writing ~/.claude/skills/

# Windows (PowerShell)
Copy-Item -Recurse cn-paper-writing "$env:USERPROFILE\.claude\skills\"
```

也可以放到项目级的 `.claude/skills/` 下，仅对该项目生效。

安装后重启 Claude Code 会话，输入 `/cn-paper-writing` 即可调用。

## 触发方式：默认**不自动触发**

本 skill **默认只能手动调用**。原因在 `SKILL.md` 顶部的 frontmatter：

```yaml
---
name: cn-paper-writing
description: 中国论文书写规范助手。……仅由用户通过 /cn-paper-writing 手动调用。
disable-model-invocation: true
---
```

两处配置共同生效：

1. `disable-model-invocation: true` —— 禁止模型自动调用，该 skill 不会出现在模型的可用 skill 列表中；
2. `description` 末尾注明“仅由用户通过 /cn-paper-writing 手动调用” —— 进一步向模型说明调用边界。

因此，普通对话里提到“帮我看看论文格式”时，Claude **不会**自行启用本 skill，必须由用户显式输入斜杠命令。

### 如需改为自动触发

修改 `SKILL.md` 顶部的 frontmatter，两步：

**第一步：删除禁止自动调用的字段**

```yaml
disable-model-invocation: true      # ← 删除这一整行
```

**第二步：改写 description，写清楚“什么时候该用”**

删去“仅由用户通过 /cn-paper-writing 手动调用。”，替换为具体触发场景，例如：

```yaml
description: 中国论文书写规范助手。依据 GB/T 7713.1-2025、GB/T 7713.2-2022、GB/T 7714-2025 等国家标准，指导中文论文撰写、参考文献著录与格式审查。当用户要求撰写、检查、审校或规范化中文学位论文、期刊论文、参考文献、摘要、标点与数字用法时使用。
```

`description` 是模型判断是否调用的**唯一依据**，写得越具体（覆盖“学位论文”“参考文献”“摘要格式”“格式检查”等说法），越容易被正确触发。

**第三步：重启会话**

保存后退出（`/exit`）并重新启动 Claude Code，新会话才会读取改动。之后在对话中提出相关请求，Claude 会自动调用；`/cn-paper-writing` 手动调用方式依然可用。

### 如需恢复为仅手动调用

把 `disable-model-invocation: true` 加回 frontmatter，并在 description 末尾补回“仅由用户通过 /cn-paper-writing 手动调用。”，同样需要重启会话生效。

## 使用示例

```
/cn-paper-writing 帮我检查这篇硕士学位论文的格式，重点看参考文献和摘要
```

```
/cn-paper-writing 这篇期刊论文的表题位置对吗？图和表的编号有什么问题？
```

也可直接引用审校清单：

```
/cn-paper-writing 按 assets/checklist.md 逐项检查
```

## 说明

- 国家标准是**底线**，不是模板。学位论文的字体、字号、封面样式通常由学位授予单位另行规定；遇到学校/期刊模板与国标不一致时，先满足模板的硬性要求。
- 本 skill 只引用真实条款；需要逐条核对时，请打开 `references/` 中对应的标准摘要。
- 标准状态与日期请在 [全国标准信息公共服务平台](https://std.samr.gov.cn/) 复核。

## 许可证

本仓库暂未指定开源许可证。如需复用或转载，请先通过 Issue 联系作者。
