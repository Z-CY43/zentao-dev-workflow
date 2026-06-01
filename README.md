# ZenTao Dev Workflow

![Codex Skill](https://img.shields.io/badge/Codex-Skill-111827?style=for-the-badge)
![ZenTao](https://img.shields.io/badge/ZenTao-Bug%20%26%20Requirement-2563EB?style=for-the-badge)
![License](https://img.shields.io/badge/License-MIT-16A34A?style=for-the-badge)

`zentao-dev-workflow` 是一个面向 Codex 的禅道研发流程技能，用于把用户口头描述整理成可直接粘贴到禅道的 Bug、研发需求、解决方案记录，并在需要提交代码时生成带禅道编号的 Git 提交说明。

技能本体遵循 Codex Skill 规范：`SKILL.md` 使用英文元信息保证稳定触发，实际输出给禅道和 Git 的字段模板保留中文。

## 适用场景

- 用户提出 Bug，需要先整理 `Bug标题`、`Bug类型`、`重现步骤`、`结果`、`期望`。
- Bug 修复完成后，需要整理禅道解决方案，包括 `解决方案`、`出现原因`、`解决方案说明`、`验证结果`。
- 用户提出研发需求，需要整理 `来源`、`研发需求名称`、`描述`、可选的 `验收标准`。
- 修复或实现功能时，需要读取代码并把真实代码依据写入原因、方案或实现说明。
- 提交到 Gitee/GitHub 时，需要确认仓库、分支、禅道编号，并生成规范提交说明。

## 核心能力

| 能力 | 说明 |
| --- | --- |
| Bug 草稿 | 根据用户对话整理标题、类型、复现步骤、结果和期望。 |
| 问答补齐 | 复现步骤或需求字段缺失时，一次只问一个关键问题。 |
| 代码溯源 | 读取代码、追踪数据流，把原因和方案写得有依据。 |
| 解决方案记录 | 根据禅道选项整理 `已解决`、`设计如此`、`外部原因` 等结论。 |
| 研发需求草稿 | 输出来源、研发需求名称、描述和可选验收标准。 |
| 提交说明 | 生成 `禅道{编号}：{标题}` 格式的提交说明。 |

## 安装方式

将仓库克隆到 Codex 技能目录：

```powershell
git clone https://github.com/Z-CY43/zentao-dev-workflow.git C:\Users\Z-CY\.codex\skills\zentao-dev-workflow
```

如果本地已存在同名目录，先确认里面没有未保存修改，再决定是否更新：

```powershell
cd C:\Users\Z-CY\.codex\skills\zentao-dev-workflow
git pull
```

## 使用方式

在 Codex 中直接描述 Bug 或需求即可。也可以显式指定技能：

```text
使用 $zentao-dev-workflow 帮我整理这个 Bug，并在修复后写禅道解决方案。
```

```text
使用 $zentao-dev-workflow 帮我提交一个研发需求，来源是客户，需求是希望报警记录支持导出筛选。
```

## Bug 输出模板

```text
Bug标题：报警范围设置脉率上下限后波形页面未同步更新
Bug类型：代码错误

[步骤]
1. 进入报警范围设置页面。
2. 修改脉率报警上限和下限并保存。
3. 返回波形页面查看脉率上限 prompt_PulseR 和下限 prompt_PulseL。

[结果]
波形页面脉率上限和下限仍显示修改前的值，未与报警范围设置保持一致。

[期望]
保存脉率报警上限和下限后，波形页面 prompt_PulseR 和 prompt_PulseL 立即显示新的脉率报警限值。
```

## 解决方案输出模板

```text
解决方案：已解决

出现原因：
报警限值数组中 p_save.aralmlimit_set[4] 和 [5] 保存的是脉率上限和下限，但波形页面初始化只绑定了 SpO2 上下限 prompt_PRR/prompt_PRL 到 [2]/[3]，没有将 prompt_PulseR/prompt_PulseL 绑定到脉率限值 [4]/[5]。

解决方案：
在 AlarmLimitMenuModel.cpp 中补充 prompt_PulseR/prompt_PulseL 与 s_AlarmlimitSTRING[4]/[5] 的初始化绑定；新增保存后的波形页面报警限值刷新逻辑。

验证结果：
静态回归检查通过；armclang 单文件语法检查通过；git diff --check 通过。
```

## 研发需求输出模板

```text
来源：客户
研发需求名称：报警记录支持按类型筛选导出

描述：
作为一名设备维护人员，
我希望报警记录导出时可以按报警类型进行筛选，
这样可以更快定位某一类问题并减少人工整理时间。

补充说明：
1. 支持按生理报警、技术报警等类别筛选。
2. 导出内容保持现有格式不变。

验收标准：
1. 进入报警记录导出页面后，可以选择报警类型筛选条件。
2. 导出的文件只包含所选类型的报警记录。
3. 未选择筛选条件时，保持当前全量导出逻辑。
```

## 提交说明规范

提交到 Gitee/GitHub 前，技能会先确认仓库、分支、禅道编号和提交文件范围。

提交说明格式：

```text
禅道{编号}：{Bug标题或研发需求名称}
```

示例：

```text
禅道662：恢复默认设置后体温单位、血压单位及报警限值转换异常
```

## 仓库结构

```text
zentao-dev-workflow/
├── SKILL.md
├── agents/
│   └── openai.yaml
├── README.md
├── LICENSE
└── .gitignore
```

## 设计原则

- 技能触发描述保持英文，降低 Codex 识别歧义。
- 面向禅道和 Git 的最终输出保持中文，减少复制粘贴后的二次修改。
- Bug 和需求都优先补齐关键信息，再进入实现或提交。
- 解决方案必须尽量基于代码证据，避免只写表面现象。
- 提交和推送前必须确认仓库、分支、范围和禅道编号。

## License

MIT License. See [LICENSE](./LICENSE).
