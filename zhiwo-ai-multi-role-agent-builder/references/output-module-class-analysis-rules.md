# 班级分析规则模块格式

评价对象、统计单位和缺证据显示遵循 [evaluation-design.md](evaluation-design.md) 的“评价对象与简明结果”。以下学生数口径仅适用于个人账号，小组账号使用组数并按可靠账号标识去重；不得从共用设备推断成员参与。

列表最终数量上限遵循 [platform-capabilities.md](platform-capabilities.md)，修订意图与最终状态检查遵循 [current-agent-config.md](current-agent-config.md)。

固定边界：

@@SXW_CLASS_ANALYSIS_RULES_START
{
  "items": []
}
@@SXW_CLASS_ANALYSIS_RULES_END

每项包含`question`和`analysis_rule_markdown`。同一智能体内`question`不得重复。分析规则应明确数据范围、统计或归纳方式、证据要求、呈现形式和教师可采取的干预方向。

数据范围只能是本次课堂、本次任务，或老师选择的连续一至七天。每个比例必须明确分子、分母、单位和有效样本：等级或分数分布以获得正式结果的学生数为分母；未观察到以纳入分析的全部学生数为分母并单独呈现；互斥错误类型可以计算构成占比，可能重叠的错误类型必须计算发生率并说明各项之和可能超过百分之一百。人数、次数、分数和百分比不得混用，指标名称与单位必须一致。

写入规则：

- 新增：`action`为`add`，`before`为空字符串，`after`为完整分析问题和规则，默认添加到末尾。
- 修订：`action`为`edit`，`before`只包含原`question`，`after`只包含需要覆盖的字段。
- 删除：`action`为`delete`，`before`只包含原`question`，`after`为空字符串。

首次完整配置只能使用`add`。修订或删除必须精确匹配唯一问题；零匹配或多匹配时停止写入。

标准新增骨架：

{
  "items": [
    {
      "action": "add",
      "before": "",
      "after": {
        "question": "班级分析问题",
        "analysis_rule_markdown": "## 分析依据\n根据本次有效成果归纳困难，不把\"未观察到\"解释为不会或薄弱。\n\n## 教学建议\n建议对应已观察到的具体表现。"
      }
    }
  ]
}

修订时`before`固定为`{"question":"原班级分析问题"}`；删除时`after`固定为空字符串。
