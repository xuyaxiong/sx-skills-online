# 快捷提问模块格式

首次使用时机及与开场白、系统提示词的联动遵循 [interaction-entry-configuration.md](interaction-entry-configuration.md)。

列表最终数量上限遵循 [platform-capabilities.md](platform-capabilities.md)，修订意图与最终状态检查遵循 [current-agent-config.md](current-agent-config.md)。

固定边界：

@@SXW_QUICK_QUESTIONS_START
{
  "items": []
}
@@SXW_QUICK_QUESTIONS_END

快捷提问为纯文本，最多五项，同一智能体内不得重复，也不保存空问题。

写入项目固定使用`action`、`before`、`after`：

- 新增：`action`为`add`，`before`为空字符串，`after`为完整新问题，默认添加到末尾。
- 修订：`action`为`edit`，`before`为原问题完整文字，`after`为修改后的完整文字。
- 删除：`action`为`delete`，`before`为原问题完整文字，`after`为空字符串。

首次完整配置只能使用`add`。修订或删除必须按`before`整句精确匹配唯一问题；零匹配或多匹配时停止写入。

标准项目骨架：

{
  "items": [
    {
      "action": "add",
      "before": "",
      "after": "新的快捷提问"
    }
  ]
}
