# 评价指标模块格式

列表最终数量上限遵循 [platform-capabilities.md](platform-capabilities.md)，修订意图与最终状态检查遵循 [current-agent-config.md](current-agent-config.md)。

固定边界：

@@SXW_EVALUATION_METRICS_START
{
  "items": []
}
@@SXW_EVALUATION_METRICS_END

评价指标最多十项，indicatorName不得重复。通用字段为indicatorName（名称）、indicatorDescription（描述）、indicatorType（整数类型）、evaluationCriteria（规则字符串）。

名称长度、评价对象和实际结果显示遵循 [evaluation-design.md](evaluation-design.md) 的“评价对象与简明结果”；这不是缩短后台评价规则。

## 三类指标

- 等级型：indicatorType为0。evaluationCriteria是JSON数组的字符串，每项只包含title（等级名称）和rule（判断规则），一至十级，按确认的等级顺序呈现，通常从好到差；已有配置的顺序原样保留，除非教师要求调整。不能直接输出数组、等级编号对象或把数组再次编码成字符串的字符串。
- 打分型：indicatorType为1。evaluationCriteria为Markdown规则，并包含整数fullScore（一至一百）。实际得分为零至满分之间的整数。
- 描述型：indicatorType为2。evaluationCriteria为Markdown规则，不输出fullScore。正确率、完成率和占比原则上使用此类型，并定义分子、分母、数据范围、有效样本、未观察到和整数百分比。

只有打分型输出fullScore。三种指标按需选择，不要求全部覆盖。证据不足时返回“未观察到”，不得推测学生表现。

打分规则采用分项累加、水平锚定或区间判断之一：分项最高分合计等于满分；水平锚点互斥且可观察；区间不重叠且说明区间内取分方法。计算可能产生小数时说明取整方式。指标名称、评分单位和班级分析口径一致，百分比不能称为分数。

## 写入规则

- 新增：action为add，before为空字符串，after为完整指标。
- 修订：action为edit，before只包含原indicatorName，after只包含需要覆盖的字段。
- 删除：action为delete，before只包含原indicatorName，after为空字符串。

首次完整配置只能使用add。改变indicatorType时，after必须给出新类型完整内容；从打分型切换到其他类型时清除旧fullScore，并按新类型校验evaluationCriteria。修改或删除按最新快照的原indicatorName精确匹配唯一项；零匹配或多匹配时停止。

等级型新增示例（外层JSON中内层引号必须转义）：

{
  "items": [
    {
      "action": "add",
      "before": "",
      "after": {
        "indicatorName": "推理依据",
        "indicatorDescription": "评价学生是否能说明判断依据",
        "indicatorType": 0,
        "evaluationCriteria": "[{\"title\":\"优秀\",\"rule\":\"能独立说明推理过程\"},{\"title\":\"待改进\",\"rule\":\"已有作答，但尚不能连贯说明依据\"}]"
      }
    }
  ]
}

打分型和描述型的evaluationCriteria直接写Markdown字符串，其中换行按JSON字符串规则编码。修订定位为`{"indicatorName":"原指标名称"}`；细则中的引号和换行经过外层JSON及内层数组解析后必须还原为原文。
