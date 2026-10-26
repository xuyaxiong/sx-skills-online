# 设计底稿字段与公开配置映射

仅在填写、转换或校验结构化设计底稿、兼容历史底稿时读取；日常需求澄清和自然语言方案不要求填完整对象。此文件是内部字段定义的唯一来源，不新增公开配置字段。字段允许按当前路径省略不适用部分；单角色不要求多角色状态表，单学段不要求跨学段分支。

```yaml
design_id: string
version: string
source:
  agent_name: string
  subject: string
  target_stages: [string]
  teacher_request: string
  optional_materials: []
intent:
  observable_goal: string
  agent_scope: lesson_specific | unit_or_project | recurring_course | open_long_term
  current_use_scenario: pre_class | in_class | after_class | self_study | mixed
  current_use_minutes: string | null
  current_task_load: string | null
  expected_frequency: once | several_times | recurring | on_demand
  participation: individual | group | whole_class
  known_or_likely_difficulties: []
  core_student_action: string
  final_artifact: string
  ai_unique_support: string
  ai_must_not_replace: string
field_provenance:
  agent_name: confirmed | proposed
  observable_goal: confirmed | derived | proposed | unknown
  agent_scope: confirmed | derived | proposed | unknown
  current_use_scenario: confirmed | derived | proposed | unknown
  current_use_minutes: confirmed | derived | proposed | unknown
  current_task_load: confirmed | derived | proposed | unknown
  expected_frequency: confirmed | derived | proposed | unknown
  participation: confirmed | derived | proposed | unknown
  material_source: confirmed | derived | proposed | unknown
  known_or_likely_difficulties: confirmed | derived | proposed | unknown
plan:
  plan_type: current_best | same_context_alternative | redesigned_context
  template_id: reading_comprehension | practice_mastery | explain_teach | expression_creation | situated_application | inquiry_problem_solving | experiment_analysis | discussion_collaboration | debate_argumentation | evaluation_revision | reflection_review | custom
  selection_source: default_recommendation | demand_reranked | all_templates | teacher_selected | custom
  recommendation_tier: recommended | other_available | custom
  default_rank: integer | null
  recommendation_reason: string
  adaptation_source: exact_specialization | composed_rules | custom
  specialization_id: string | null
  card_title: string
  card_description: string
  teacher_requirement_example: string
  primary_learning_form: string
  secondary_learning_form: string | null
  primary_protocol: understand | practice | revise | argue | inquire | reflect
  interaction_variant: learning_loop | collection_confirmation
  primary_paradigm: autonomous | project | immersive | inquiry | associative | collaborative
  supporting_paradigm: autonomous | project | immersive | inquiry | associative | collaborative | null
  paradigm_reason: string
  template_fit: aligned | adaptable | conflict
  conflict_resolution: none | keep_template | stage_adaptation | switch_template | pending_teacher
  conflict_note: string
  stages: []
subject_adapter:
  content_boundary: []
  typical_errors: []
  valid_evidence: []
  feedback_priority: []
  rubric: []
runtime:
  material_source: teacher | student | system | mixed
  material_visibility: student_visible | ai_only | shared | none
  material_delivery: preloaded | teacher_screen | in_chat | student_upload | mixed
  completion_condition: string
  continuation_mode: pause | repeat | advance | next_stage
  resume_rule: string
  capability_dependencies:
    - capability: text | knowledge_base | file_upload | image_understanding | speech_to_text | pronunciation_analysis | image_generation | music_generation | video_generation
      use_reason: string
      implementation_rule: string
material_answer_policy:
  mode: model_only | hybrid | strict_material
  teacher_mentioned_knowledge_base: boolean
  teacher_uploaded_in_skill: boolean
  confirmed_material_scope: []
  embedded_materials:
    - material_id: string
      title: string
      content: string
      source_kind: teacher_attachment | teacher_text
  student_source_expression: source_neutral
  insufficient_material_rule: string
evidence_contract:
  first_turn_input: string
  stage_completion:
    - stage_name: string
      required_evidence: []
      done_when: string
  final_artifact_requirements: []
  evaluation_evidence: []
  ai_contribution_boundary: string
learner_start:
  mode: not_required | student_select_stage | student_select_challenge | ai_adaptive_in_flow
  preset_context_key: string | null
  allowed_stage_options: []
  challenge_rules:
    - value: string
      first_task: string
      material_level: string
      support_level: string
  adaptive_rule: string | null
  decision_evidence: string | null
  recalibration_rule: string | null
  self_report_is_evidence: false
stage_adaptation:
  strategy: shared_goal_adaptive | primary_stage_compatible | stage_specific_branches
  primary_stage: string | null
  shared_goal: string
  stage_rules:
    - stage: string
      language_and_material: string
      support: string
      expected_artifact: string
      evaluation_boundary: string
entry_config:
  opening_mode: task_led | student_initiated | intake_first
  preset_questions:
    - question_id: string
      text: string
  preset_inputs:
    - input_id: string
      label: string
      type: single_line_text | multi_line_text | single_select | file_upload
      required: boolean
      max_length: integer | null
      options: []
      accepted_file_types: []
      max_file_count: integer | null
      context_key: string
      persistence_scope: current_conversation
      purpose: string
      affects: [task | material | difficulty | role | stage | feedback]
      consumption_rule: string
      option_rules: {}
      platform_already_knows: false
      student_can_update: true
      device_fit: any | tablet | computer
  context_injection_rule: string
security_profile:
  enabled: true
  protected_instructions: [role, goal, workflow, completion, safety, material_boundary]
  untrusted_sources: [student_input, attachment, web, knowledge_base, quoted_text, tool_output]
  ambiguous_input_action: clarify_once
  disclosure_policy: refuse_hidden_configuration
  conflict_action: refuse_conflict_and_resume_learning
role_orchestration:
  mode: single_role | sequential_multi_role
  decision: single_role | multi_role
  recommended_role_count: integer
  teaching_jobs:
    - job_id: string
      name: string
      stage_names: []
      responsibility: string
      judgment_basis: string
      student_value: string
      requires_independence: boolean
  merge_analysis:
    - job_ids: []
      decision: merge | separate
      reason: string
  minimality_reason: string
  complexity_fit: string
  # 以下旧字段只为兼容已接入的多角色配置，不再作为角色判断依据。
  necessity_decision: required | not_required
  necessity_reasons: []
  fallback_reason: string | null
  shared_goal: string
  shared_context: []
  presentation_mode: text_identity_switch | avatar_scene_switch
  initial_state_id: string | null
  roles:
    - role_id: string
      teaching_job_ids: []
      name: string
      visible_identity: string
      teaching_duty: string
      professional_stance: string
      judgment_criteria: []
      required_student_action: string
      allowed_actions: []
      prohibited_actions: []
      voice_style: string
      expression_rhythm: string
      action_cues: string
      entry_condition: string
      completion_condition: string
      exit_condition: string
      evidence_to_collect: []
  scenes:
    - scene_id: string
      name: string
      purpose: string
      role_id: string
  states:
    - state_id: string
      role_id: string
      scene_id: string
      student_action: string
      ai_action: string
      artifact_output: string
      evidence_output: []
      done_when: string
  transitions:
    - from_state: string
      to_state: string
      trigger: string
      handoff_message: string
      artifact_passed: []
      evidence_passed: []
      return_condition: string | null
      support_if_failed: string
      max_returns: integer
  evidence_map:
    - metric_id: string
      role_id: string
      state_id: string
      artifact_version: string
      support_level: independent | light_prompt | scaffolded | repeated_ai
assessment:
  teacher_priority: []
  preferred_result_types: []
  task_metrics: []
  longitudinal_metrics: []
  unevaluable_result: 未观察到
presentation:
  tone: string
  visible_role: string | null
generated_config:
  system_prompt: string
  opening_message: string
  quick_questions: []
  preset_inputs: []
  evaluation_metrics: []
  class_analysis_rules: []
revision:
  status: none | proposed | confirmed | applied
  base_version: string | null
  affected_paths: []
  proposed_changes: []
  teacher_confirmation: string | null
consistency:
  unresolved_risks: []
```

`material_answer_policy`、`evidence_contract`以及预置输入项中的`purpose`、`affects`、`consumption_rule`、`option_rules`、`platform_already_knows`、`student_can_update`和`device_fit`属于设计底稿内部字段。它们用于方案确认、提示词生成和语义校验，不新增公开配置模块。材料策略与证据契约写入`system_prompt`并约束评价规则；预置输入只输出平台支持的公开字段，其消费规则同步写入`system_prompt`。

不另建教学行为字段：在现有学生动作、互动与支架描述中记录提交媒介、学生新增加工、AI已提供内容及帮助无效后的替代动作；在材料策略和完成接续描述中记录材料身份、下一任务来源、切换与暂停规则；在证据契约和评价描述中记录等价能力证据与帮助归属。将这些描述编译进教学正文及评价规则，不只保留于内部底稿。具体判断统一引用通用教学互动规则。


## 入口与自适应的兼容映射

以下是内部工具映射，不是教师或学生需要填写、选择或看到的内容；教学决策分别以进入配置、通用教学互动和跨学段适配规则为准。

- `entry_config.opening_mode`：任务直接开始映射为 `task_led`，学生主动发起为 `student_initiated`，先提交信息或成果为 `intake_first`。
- `learner_start.mode` 只记录主要起点入口：不额外设置入口为 `not_required`，学段选择为 `student_select_stage`，挑战选择为 `student_select_challenge`。保留 `ai_adaptive_in_flow` 兼容旧的“无预选、互动内调整”底稿；不把它作为自适应的唯一入口，也不要求多学段才允许使用。
- `adaptive_rule` 可用于上述任一模式；有值时同时写明 `decision_evidence` 和 `recalibration_rule`。`not_required` 只表示没有专门的起点入口，不禁止动态教学。旧底稿三个值均为空时仍可校验；新方案所需的动态支持必须写入实际系统提示词，不能以内部空值免除教学规则。
- `preset_context_key` 定位主要选择项。有学段和挑战两个选择项时，主要入口记录学段，另一个挑战入口及其组合处理由现有 `entry_config.preset_inputs` 的用途、影响与处理规则表达，不新增模式或公开字段。每项必须有可执行的 `option_rules`，组合顺序写入 `consumption_rule` 与系统提示词。
- 学段选择的 `allowed_stage_options` 来自适用学段；挑战选择的 `challenge_rules` 对应选项。无主要选择的两种模式中，`preset_context_key` 为空，专用选项列表为空。不因已有选择而要求 `adaptive_rule` 为空。
- 跨学段的共同目标、主要学段兼容、分学段任务，分别映射到现有 `stage_adaptation.strategy` 的三个值。首项任务必须区分学段的分支方案，使用学段入口取得信息，并保留真实的 `stage_rules`。

## 兼容与映射

- 推荐入口依次映射到 `selection_source` 的 `default_recommendation`、`demand_reranked`、`all_templates`；完全自定义时，`template_id` 与 `selection_source` 均为 `custom`。这些值不作为教师可见名称。
- 角色判断使用 `role_orchestration.mode` 与 `decision`：单角色分别为 `single_role`、`single_role`，角色数为1，`initial_state_id` 为空，`scenes`、`states`、`transitions`、`evidence_map` 为空；多角色分别为 `sequential_multi_role`、`multi_role`，角色数为2—4，并填入职责和交接关系。旧必要性字段不参与新决策。
- 材料策略的中文名称“模型知识／混合策略／严格材料策略”，依次对应 `material_answer_policy.mode` 的 `model_only`、`hybrid`、`strict_material`。

`generated_config`现在直接保存六类公开模块的最终值快照，列表不是增改删补丁。历史的`generated_config.entry_config/student_feedback_rules/material_config`只是旧工具数据，不作为当前公开字段；不能凭简略`assessment`自动造出完整量规。

已有智能体修订时，平台当轮回传配置的字段与优先依据见 [current-agent-config.md](current-agent-config.md)；这里的历史底稿不是修订的前置材料。评价指标最终值采用 [output-module-evaluation-metrics.md](output-module-evaluation-metrics.md) 的当前字段。

- 正式开场白来自 `generated_config.opening_message`；历史测试底稿的顶层 `opening` 仅作兼容别名。两者同时存在而不一致时停止转换，不任选一个。
- `entry_config.preset_questions[].text` 映射到公开 `quick_questions` 文本列表；问题ID不外显。
- `preset_inputs[].label` 映射为 `title`，`max_length` 映射为 `max_characters`；单选列表按顺序映射 `option_1` 等。文件类型转为既有文档/图片布尔值，总量遵循平台限制。
- `context_key` 只在底稿中定位输入；`purpose`、`affects`、`consumption_rule`、`option_rules` 的教学含义须转成系统提示词中的自然语言，使用入口可见名称，不输出字段名或内部对象，不增加公开字段。
- 学段、学科、材料事实边界、评价问题、最低证据及帮助归属写入既有各类型规则Markdown；不得只保留在底稿，也不得把评分细则塞入指标简述。班级规则用已确认指标名称关联。
- `material_source`描述提供方；`material_answer_policy.mode`描述使用边界；知识库提及和上传布尔值只记录事实，不决定模式。纯文本可以作为严格材料。
- 单次时长和任务量择一明确即可；仅用任务量时，时长为空，不伪造分钟数。来源标记记录在现有 `field_provenance` 对应键中。
- 公开结构、数量硬上限和增改删规则只以 [output-contracts.md](output-contracts.md) 及其模块模板为准；底稿的建议数量不替代公开上限。
