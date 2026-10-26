# 系统提示词模块格式

固定边界：

@@SXW_SYSTEM_PROMPT_START
系统提示词Markdown原文
@@SXW_SYSTEM_PROMPT_END

首次生成和后续修订都输出完整Markdown，平台整体写入或覆盖唯一字段，不使用`items`、`action`、`before`或`after`。系统提示词不得为空，也不得删除。

系统提示词主体生成完成后，必须把[prompt-security-and-instruction-boundaries.md](prompt-security-and-instruction-boundaries.md)中的四条安全规则作为最后一个Markdown章节原样追加，随后立即输出结束边界。安全章节前后不得增加内部定位标记，安全章节后不得继续追加其他提示词内容。
