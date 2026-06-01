---
name: zentao-dev-workflow
description: Prepare ZenTao development records for Chinese software projects. Use when the user reports a bug to document in ZenTao, asks for bug title/type/reproduction steps, asks for a bug resolution with cause/fix/verification, asks to draft a ZenTao R&D requirement with source/name/description/optional acceptance criteria, asks to inspect code for root cause or implementation details, or asks to commit/push code to Gitee/GitHub with a ZenTao-numbered commit message.
---

# ZenTao Development Workflow

## Overview

Use this skill to create paste-ready ZenTao records for two work item types: `Bug` and `研发需求`. Keep the workflow concise, but write the final field values in Chinese because the target ZenTao forms are Chinese.

## Choose The Work Item Type

Use the Bug workflow when the user says `bug`, `报错`, `异常`, `不生效`, `没有更新`, `修复`, or describes existing behavior that is wrong.

Use the R&D requirement workflow when the user says `研发需求`, `需求`, `功能`, `新增`, `优化`, `希望`, `需要支持`, or describes desired new behavior.

If the type is unclear, ask one short question in Chinese: `这是要按 Bug 提，还是按研发需求提？`

## Bug Workflow

Before fixing code, draft the ZenTao bug unless the user explicitly asks to skip documentation.

Include these fields:

- `Bug标题`: short user-visible symptom. Prefer “operation/context + abnormal behavior”.
- `Bug类型`: choose from ZenTao options. Default implementation defects to `代码错误`. Other options: `配置相关`, `安装部署`, `安全相关`, `性能问题`, `标准规范`, `测试脚本`, `设计缺陷`, `结构缺陷`, `其他`.
- `重现步骤`: use the exact Chinese template below.

```text
Bug标题：...
Bug类型：代码错误

[步骤]
1. ...
2. ...

[结果]
...

[期望]
...
```

If reproduction details are missing, ask one short Chinese question at a time. Prioritize firmware/version, device model, menu path, input values, reboot requirement, and expected display value.

Do not invent exact values. If an example is useful, label it as an example, such as `例如：上限 130、下限 45`.

## R&D Requirement Workflow

When drafting a ZenTao `研发需求`, include:

- `来源`: required. Ask if missing. Known options: `客户`, `用户`, `产品经理`, `市场`, `客服`, `运营`, `技术支持`, `竞争对手`, `合作伙伴`, `开发人员`, `测试人员`, `其他`.
- `研发需求名称`: short outcome-oriented title.
- `描述`: explain who needs the capability, what they need, and why.
- `验收标准`: optional. Leave blank when the user does not provide it, unless a clearly marked draft is helpful.

Use this paste-ready Chinese format:

```text
来源：客户
研发需求名称：...

描述：
作为一名<用户/角色>，
我希望<完成的能力或行为>，
这样可以<业务价值或使用价值>。

补充说明：
1. ...
2. ...

验收标准：
1. ...
2. ...
```

Ask for `来源` before drafting if it is not provided and there is no standing preference.

## Code Investigation

For bugs, inspect relevant code before writing `出现原因` or `解决方案`.

For requirements that affect existing behavior, inspect relevant code before proposing an implementation plan.

Use this investigation pattern:

- Search user-mentioned UI IDs, labels, data keys, storage fields, and functions with `rg`.
- Trace data from input/save path to display/use path.
- Compare nearby working fields or similar modules.
- Cite local file paths and line numbers when reporting cause or implementation details.

Keep fixes scoped to the ZenTao record. Verify with the strongest available check: automated tests, syntax-only compile, project build, static regression search, or manual reproduction notes.

## Bug Resolution Record

After fixing or classifying a bug, write a ZenTao resolution record.

Choose `解决方案` from the ZenTao list:

- `已解决`: code or configuration fixed.
- `设计如此`: behavior matches confirmed design.
- `重复Bug`: covered by another bug.
- `外部原因`: caused by dependency or environment outside the project.
- `无法重现`: reasonable reproduction attempts failed.
- `延期处理`: explicitly postponed.
- `不予解决`: explicitly rejected.

Use this paste-ready Chinese format:

```text
解决方案：已解决

出现原因：
...

解决方案：
...

验证结果：
...
```

Back `出现原因` and `解决方案` with code evidence when code was inspected.

## ZenTao Web Handling

Default ZenTao URL: `http://192.168.0.101:8005/zentao/my.html`.

If the user asks to read or fill ZenTao directly:

- Use Chrome when login cookies/session are needed.
- Use the in-app browser when the page is accessible without personal browser state.
- Never click `保存` or submit a form without explicit user confirmation.
- If the page is unreachable or requires credentials, provide paste-ready field values instead.

If a ZenTao number is needed and missing, ask for it. Never guess it from unrelated context.

## Commit And Push Workflow

Trigger this section when the user says `提交到Gitee`, `提交gitee`, `提交到GitHub`, `提交github`, `推送`, `commit`, `提交代码`, or similar.

Before committing or pushing:

1. Confirm the target repository if more than one is possible, or if the current Git remote is not clearly the intended repository.
2. Confirm the branch when it matters.
3. Ask for the ZenTao number if missing.
4. Summarize files planned for commit.
5. Get explicit user confirmation before `git commit` or `git push`.

Commit subject format:

```text
禅道{编号}：{Bug标题或研发需求名称}
```

Example:

```text
禅道662：恢复默认设置后体温单位、血压单位及报警限值转换异常
```

If the user wants a detailed GitHub/Gitee description, provide a longer Chinese body separately from the commit subject.

## Example

Bug report:

```text
报警范围设置脉率的上限和下限，设置完成后，波形页面脉率的上限和下限没有更正调整。prompt_PulseR和prompt_PulseL
```

Bug draft:

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

Resolution draft:

```text
解决方案：已解决

出现原因：
报警限值数组中 p_save.aralmlimit_set[4] 和 [5] 保存的是脉率上限和下限，但波形页面初始化只绑定了 SpO2 上下限 prompt_PRR/prompt_PRL 到 [2]/[3]，没有将 prompt_PulseR/prompt_PulseL 绑定到脉率限值 [4]/[5]。保存报警限值后也缺少波形页面对应 prompt 的联动刷新。

解决方案：
在 AlarmLimitMenuModel.cpp 中补充 prompt_PulseR/prompt_PulseL 与 s_AlarmlimitSTRING[4]/[5] 的初始化绑定；新增保存后的波形页面报警限值刷新逻辑，使修改脉率上下限后立即同步到波形页面。

验证结果：
静态回归检查确认 prompt_PulseR/prompt_PulseL 已绑定到脉率限值 [4]/[5]，保存路径会调用波形页面刷新；armclang 对 AlarmLimitMenuModel.cpp 的 syntax-only 检查通过；git diff --check 通过。
```
