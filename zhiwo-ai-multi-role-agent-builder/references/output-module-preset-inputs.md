# 预置输入项模块格式

首次使用时机及与开场白、系统提示词的联动遵循 [interaction-entry-configuration.md](interaction-entry-configuration.md)。

列表最终数量上限遵循 [platform-capabilities.md](platform-capabilities.md)，修订意图与最终状态检查遵循 [current-agent-config.md](current-agent-config.md)。

固定边界：

@@SXW_PRESET_INPUTS_START
{
  "items": []
}
@@SXW_PRESET_INPUTS_END

预置输入项最多十项，`title`不得重复。每项必须实际改变第一轮任务、材料、难度、角色、阶段或反馈，并在系统提示词中具有明确消费规则。

支持四种类型：

- `single_line_text`：包含`title`、`required`、`max_characters`，最大一百字。
- `multi_line_text`：包含`title`、`required`、`max_characters`，最大一万字。
- `single_select`：包含`title`、`required`、`options`，使用`option_1`至`option_10`，最多十项。
- `file_upload`：包含`title`、`required`、`allow_document`、`allow_image`、`max_file_count`；至少允许文档或图片之一，所有上传入口合计最多三个文件。

写入规则：

- 新增：`action`为`add`，`before`为空字符串，`after`为完整输入项，默认添加到末尾。
- 修订：`action`为`edit`，`before`只包含原`title`，`after`只包含需要覆盖的字段。
- 删除：`action`为`delete`，`before`只包含原`title`，`after`为空字符串。

首次完整配置只能使用`add`。修改`type`时，`after`必须输出新类型所需的完整配置，平台删除旧类型专属字段后再写入。

标准新增骨架：

{
  "items": [
    {
      "action": "add",
      "before": "",
      "after": {
        "title": "输入项标题",
        "type": "single_line_text",
        "required": false,
        "max_characters": 100
      }
    }
  ]
}

修订时`before`固定为`{"title":"原输入项标题"}`；删除时`after`固定为空字符串。
