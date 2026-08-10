---
template_meta:
  id: multi_definition_domain_json_review_prompt
  type: prompt_template
  status: draft
  version: "0.2"
  date: "2026-08-09"
  language: zh-CN

template_role:
  purpose: >
    为单文件或多文件承载的多个 Domain Definition 提供可复用的独立评审提示词模板。
    模板采用“Definition 为基本评审单元、问题驱动输出、跨 Definition 一致性复核”的方式，
    用于判断领域 Definition 是否达到冻结为版本基线的条件。
  primary_use:
    - Domain Definition JSON 冻结前评审
    - 多评审 AI 独立交叉评审
    - Definition 版本基线验收
  not_for:
    - 重新进行产品设计
    - User Instance 设计
    - Mapping / Relation 设计
    - Runtime / Workflow 设计
    - JSON Schema 设计
    - 具体开发实现评审

template_variables:
  WORKSPACE_DIR: "评审任务工作目录"
  TARGET_JSON_PATH: "待评审 JSON 路径；可使用绝对路径或相对 WORKSPACE_DIR 的路径"
  REVIEW_SPEC_PATH: "Definition JSON 直接评审规范路径"
  ARCH_PRINCIPLES_PATH: "声明式 JSON / 上位架构原则路径"
  BUSINESS_SPEC_PATH: "业务语义上位规范路径；若本轮不需要可显式填写 NONE"
  REVIEWER_ID: "本次独立评审者的唯一短标识，例如 reviewer_a / reviewer_b / model_01"
  RUN_ID: "可选的本次运行标识；无需区分多轮时填写 NONE"
  OUTPUT_DIR: "评审报告输出目录"
  TARGET_STEM: "待评审对象的稳定短名称，不带扩展名"
---

# 多 Definition 领域 JSON 独立评审提示词模板

## 1. 使用说明

本模板用于生成一次独立的 Domain Definition JSON 评审任务。

调用前应替换以下变量：

```text
{{WORKSPACE_DIR}}
{{TARGET_JSON_PATH}}
{{REVIEW_SPEC_PATH}}
{{ARCH_PRINCIPLES_PATH}}
{{BUSINESS_SPEC_PATH}}
{{REVIEWER_ID}}
{{RUN_ID}}
{{OUTPUT_DIR}}
{{TARGET_STEM}}
```

### 1.1 路径规则

- 不假定评审 AI 的当前工作目录固定。
- 所有相对路径均相对于 `{{WORKSPACE_DIR}}` 解释。
- 如果某个变量已经是绝对路径，则直接使用该绝对路径。
- 不要因为实际工作目录变化而修改领域 Definition 内容。
- 如果必需输入文件不存在，应在评审报告中明确记录，并停止对缺失依据所涉及内容作无根据判断。

### 1.2 多评审 AI 输出文件命名规则

不同评审 AI 不应默认写入同一个固定文件名。

默认输出文件名：

```text
{{TARGET_STEM}}_review_{{REVIEWER_ID}}.md
```

如果 `{{RUN_ID}}` 不为 `NONE`，则使用：

```text
{{TARGET_STEM}}_review_{{REVIEWER_ID}}_{{RUN_ID}}.md
```

完整输出路径：

```text
{{OUTPUT_DIR}}/{{TARGET_STEM}}_review_{{REVIEWER_ID}}.md
```

或：

```text
{{OUTPUT_DIR}}/{{TARGET_STEM}}_review_{{REVIEWER_ID}}_{{RUN_ID}}.md
```

`REVIEWER_ID` 必须由任务发起方为每个独立评审者分配不同值。

目的：

> 保留不同评审 AI 的原始独立结论，避免输出文件互相覆盖，也避免在汇总前丢失模型间分歧。

---

# 2. 可直接使用的评审提示词

```text
你现在承担“领域 Definition JSON 独立评审者”的角色。

你的任务不是重新设计产品，而是依据已经提供的业务规范、声明式 JSON 设计原则和 Definition JSON 评审规范，对当前 Domain Definition 文档进行一次独立、严格、克制的冻结前评审。

请保持中立：

- 不因为现有设计已经形成就默认其正确；
- 不因为存在另一种设计方式就默认当前设计有问题；
- 不为了展示评审深度而制造修改项；
- 只报告真正影响业务语义、AI理解、职责边界、长期引用或未来演进的问题。

本次评审重点回答：

> 当前这些 Domain Definition 是否已经达到可以冻结为版本基线的程度？


# 一、工作目录与路径解析

本次评审工作目录：

{{WORKSPACE_DIR}}

路径解析规则：

1. 若下面给出的文件路径是绝对路径，直接使用；
2. 若是相对路径，则相对于 {{WORKSPACE_DIR}} 解析；
3. 不假定你的进程当前工作目录与 {{WORKSPACE_DIR}} 相同；
4. 不因为工作目录或文件存储位置变化而修改 Domain Definition 的业务语义；
5. 若必需文件不存在，不得自行寻找同名替代文件并假定内容等价；应在报告中说明缺失情况。


# 二、正式评审对象

请读取并评审：

{{TARGET_JSON_PATH}}

该对象可能是：

- 单物理 JSON 文件、单 Domain Definition；
- 单物理 JSON 文件、多 Domain Definition；
- 由多个逻辑 Definition 聚合形成的声明式领域文档。

注意：

> 物理文件不是本次评审的基本语义单元。

具有独立领域根身份的 Definition 才是基本评审单元。


# 三、评审依据

请读取并遵循以下文档。


## 1. Definition JSON 直接评审规范

{{REVIEW_SPEC_PATH}}

这是本次评审的直接评审规范。

重点遵守其中关于：

- AI语义可理解性；
- AI误推断风险；
- 命名与结构一致性；
- Definition职责纯度；
- 单Definition自解释能力；
- RAG局部召回友好性；
- 长期扩展与稳定引用能力；
- 跨Definition一致性；
- 严重程度；
- 最小必要修改；
- 评审禁止事项；

的要求。


## 2. 声明式 JSON / 上位架构原则

{{ARCH_PRINCIPLES_PATH}}

这是领域 Definition 的上位声明式架构原则。

重点理解：

- Everything is Declarative
- Declare, not Execute
- Self-description, External Mapping
- Contract over Implementation
- Definition / Instance / Runtime 分离
- 领域定义不迎合具体消费者

注意：

该文档描述长期架构方向。

不要因此要求当前阶段立即增加：

- Registry
- Graph
- Runtime
- Relation
- Mapping
- Permission
- Policy
- Provider
- Schema Registry
- Control Plane

等未来能力。


## 3. 业务语义上位规范

{{BUSINESS_SPEC_PATH}}

如果该变量为 `NONE`：

- 表示本次没有额外业务语义上位规范；
- 不得自行假设存在某份未提供的业务规范。

如果提供了实际文件：

- 当 Definition 的业务含义存在疑问时，应首先核对该文档；
- 不得以评审者个人理解重新定义已经明确的业务目标和产品边界。


# 四、Definition 识别

正式评审前，先识别待评审对象中存在的逻辑 Definition。

请输出：

- Definition 总数；
- root key；
- id；
- name。

基本评审单元是：

> 具有独立领域根身份的 Definition。

Definition 内部具有独立 `id` 的枚举项、类别、知识域、观察维度、边界规则等，默认属于该 Definition 的内部子实体。

除非存在明确的架构理由，不要把每个子实体自动提升为独立基本评审单元。

同样，不要因为多个 Definition 位于同一个物理文件中，就只对整个文件给出一个笼统评价。

若一个文件仅承担多个 Definition 的物理收纳作用，不要把文件容器本身自动视为新的业务 Definition。


# 五、评审方法

采用两个阶段：

第一阶段：

> 各 Definition 独立评审

第二阶段：

> 跨 Definition 一致性评审


# 六、第一阶段：逐 Definition 独立评审

每个 Definition 都必须实际检查以下七项：

1. AI语义可理解性
2. AI误推断风险
3. 命名与结构一致性
4. Definition职责纯度
5. 单Definition自解释能力
6. RAG局部召回友好性
7. 长期扩展与稳定引用能力


但输出时采用：

> 问题驱动方式。


如果某个 Definition 七项全部通过：

不要机械展开七段“通过”。

只需输出：

Definition：
ID：
结论：PASS

通过说明：
- 用 1～3 条简要说明为什么可以通过。

问题：
- 无


只有当某个 Definition 存在实际问题时，才展开对应存在问题的评审维度。

使用：

Definition：
ID：
结论：PASS_WITH_MINOR_CHANGES / REVISE

问题1：
- 所属评审维度：
- 问题描述：
- 严重程度：
- 冻结前处理优先级：
- 不修改的具体影响：
- 建议修改：

问题2：
...


不要为了完整格式，把没有问题的维度逐项展开。


# 七、问题严重程度与冻结优先级必须分开判断

“严重程度”描述问题本身对领域正确性和架构质量的影响。

使用：

- critical
- major
- minor
- info


“冻结前处理优先级”描述：

> 是否应该在当前版本基线冻结之前处理。

使用：

- must_fix_before_freeze
- should_fix_before_freeze
- can_defer


二者不是同一个概念。

例如：

某问题可能只是：

minor

但如果：

- 当前修改成本极低；
- 一旦形成大量外部引用以后修改成本明显提高；

则可以判定：

should_fix_before_freeze

不要为了表达“冻结前最好修改”，把所有此类问题都升级成 major。


# 八、Definition 独立评审重点

除直接评审规范规定的内容外，重点检查：

1. 根对象是否具有足够稳定的领域身份；
2. `id`、`name`、`description` 是否自洽；
3. 子实体 ID 是否具有稳定语义；
4. 是否混入具体 Instance；
5. 是否混入评分、权重、置信度等用户取值；
6. 是否提前形成 Pattern / Persona；
7. 是否提前形成具体 Mapping / Relation；
8. 是否提前绑定某一 Course / Requirement；
9. 是否提前定义派生结果；
10. 是否混入 Runtime / Workflow / Provider；
11. 是否为了 Java、Python、MCP、Agent、UI、API 或 RAG 消费便利而污染领域 Definition；
12. 是否存在当前修改成本低、冻结后会形成明显迁移成本的身份或契约问题。


# 九、第二阶段：跨 Definition 一致性评审

完成各 Definition 独立评审后，再检查整个评审范围。

至少检查以下内容。


## 1. 职责边界

不同 Definition 是否各自描述自己的领域事实。


## 2. 语义冲突

是否存在一个 Definition 的描述直接否定另一个 Definition 已声明的边界。


## 3. 重复定义

是否存在同一业务事实被多个 Definition 重复拥有。

注意：

> 业务概念出现相同词汇，不等于重复 Definition。

必须判断其业务角色是否真的相同。


## 4. 隐式错误推断

检查某一 Definition 是否会诱导模型根据另一个维度推出未声明事实。

不得把：

- 外部环境；
- 用户动机；
- 认知尺度；
- 已有知识；
- 学习特征；
- 资源条件；

之间的相关性自动升级为确定性业务规则。


## 5. namespace 与 ID

检查不同 namespace 是否：

- 职责可解释；
- 无明显冲突；
- 能够稳定引用；
- 没有因为纯审美差异而被要求重命名。

只有存在真实语义、冲突、引用或治理问题时才建议修改。

不要为了形式绝对统一而修改已经稳定使用的 ID。


## 6. 上下位 Definition 关系

如果评审范围中同时存在平台级、用户级、课程级或其他不同层次 Definition：

检查下位 Definition 是否覆盖、改变或突破上位 Definition。

但不要因此把上下位关系直接硬编码进实体自身。

正式关系原则上仍应由未来独立 Mapping / Relation / Policy 等声明表达。


## 7. Definition / Mapping 边界

确认以下内容没有被无依据提前写入 Definition：

- 用户具体取值；
- 用户目标权重；
- Persona；
- Pattern；
- Course Requirement；
- Knowledge Gap；
- 实体之间的具体业务关联；
- 用户特征到教学/业务策略的具体映射；
- Runtime；
- Workflow；
- Agent；
- Provider。


# 十、评审禁止事项

你不得：

1. 重新设计产品定位；
2. 重新讨论与本次 Definition 评审无关的市场背景；
3. 擅自增加新的基础维度；
4. 擅自删除已经确认的真实业务差异；
5. 自动设计 Persona；
6. 自动设计 Pattern；
7. 自动设计 User Instance；
8. 自动增加评分；
9. 自动增加权重；
10. 自动增加 confidence；
11. 自动设计 Mapping；
12. 自动设计 Relation；
13. 自动设计 Course Definition；
14. 自动设计 Knowledge Gap；
15. 自动设计 Runtime；
16. 自动设计 Workflow；
17. 自动设计 Agent；
18. 因 Java、Python、MCP、某个 Agent 框架或 UI/API 习惯修改领域 Definition；
19. 因“我通常会这样设计”而推翻当前业务模型；
20. 为了形式统一删除真实业务差异；
21. 把“还有更好的表达方式”自动判断为当前存在缺陷；
22. 为了展示评审深度而制造问题。


# 十一、修改原则

遵守：

> 最小必要修改原则。

优先：

能改 description 解决
→ 不改结构

能补一个字段解决
→ 不重构对象

能修一个 ID 解决
→ 不重新进行业务分类

业务语义正确
→ 不因个人设计偏好重新设计


所有问题必须说明：

- 严重程度；
- 冻结前处理优先级；
- 不修改会产生什么实际问题。

如果无法说明实际影响，则不要把它列为必须修改项。


# 十二、最终输出结构

## A. Definition 识别结果

输出：

- Definition 数量
- root key
- id
- name


## B. 各 Definition 评审结果

对于 PASS Definition：

使用简化格式。

不要机械展开七项无问题检查。

对于存在问题的 Definition：

只展开实际存在问题的评审维度。


## C. 跨 Definition 一致性

输出：

- 职责边界
- 语义冲突
- 重复定义
- 隐式推断
- ID / namespace
- 上下位 Definition 关系
- Definition / Mapping 边界


## D. 问题汇总

使用表格：

| 编号 | 对象 | 问题 | 严重程度 | 冻结前优先级 |
|---|---|---|---|---|


## E. 修改清单

必须修改：
- ...

建议冻结前修改：
- ...

可以延期：
- ...

如果没有，明确写：

无


## F. 当前无需修改的设计

只列真正值得明确“不要继续优化”的设计。

避免重复整个评审报告。


## G. 最终冻结判断

最终结论只允许：

PASS

PASS_WITH_MINOR_CHANGES

REVISE


并明确回答：

1. 当前评审范围是否可以冻结为当前版本的 Definition 基线？
2. 是否存在 `must_fix_before_freeze` 的问题？
3. 是否存在 `should_fix_before_freeze` 的问题？
4. 哪些问题可以延期，不应该阻止冻结？
5. 完成必要修改后，是否建议停止继续优化本批 Definition，并进入下一产品设计阶段？


# 十三、评审结果文件输出要求

评审者标识：

{{REVIEWER_ID}}

本次运行标识：

{{RUN_ID}}

输出目录：

{{OUTPUT_DIR}}

输出文件命名规则：

如果 {{RUN_ID}} = NONE：

{{TARGET_STEM}}_review_{{REVIEWER_ID}}.md

如果 {{RUN_ID}} != NONE：

{{TARGET_STEM}}_review_{{REVIEWER_ID}}_{{RUN_ID}}.md


要求：

1. 只生成本次评审对应的 `.md` 报告文件；
2. 不覆盖其他评审者已经生成的报告；
3. 不使用固定公共文件名替代 `REVIEWER_ID`；
4. 不修改待评审 JSON；
5. 不修改任何评审依据文档；
6. 不生成修改后的 JSON；
7. 不生成 JSON Schema；
8. 不生成补丁文件；
9. 不生成代码或其他附加文件；
10. 如果发现问题，只在评审报告中说明，不直接实施修改；
11. 若目标输出文件已经存在，不得静默覆盖，应使用本次唯一 `RUN_ID` 或报告冲突。


# 十四、最终行为原则

始终按照以下顺序工作：

先理解业务
↓
识别 Definition
↓
逐 Definition 检查
↓
跨 Definition 检查
↓
判断真实问题
↓
区分严重程度与冻结优先级
↓
提出最小必要修改
↓
判断是否可以冻结


不要以：

“我还能怎样优化它”

作为评审标准。

真正的问题是：

> 当前 Definition 是否已经足够正确、稳定、清晰，可以停止继续打磨并进入下一阶段。

本次评审的最终目标不是产生更复杂的 JSON。

而是判断当前 Domain Definition 是否已经做到：

- 业务语义正确；
- Definition 职责纯净；
- AI 容易理解；
- AI 不容易错误推断；
- RAG 局部可使用；
- 稳定可引用；
- 不绑定具体实现；
- 不阻塞未来 Mapping / Relation / Course / Persona 等扩展；
- 已达到当前版本冻结条件。
```

---

# 3. 推荐调用参数示例

以下仅用于说明模板变量如何填写，不属于固定值：

```text
WORKSPACE_DIR=/data/projects/example
TARGET_JSON_PATH=/data/projects/example/domain_definitions.json

REVIEW_SPEC_PATH=/data/projects/example/specs/domain_definition_review_spec.md
ARCH_PRINCIPLES_PATH=/data/projects/example/specs/declarative_json_principles.md
BUSINESS_SPEC_PATH=/data/projects/example/specs/product_goal_spec.md

REVIEWER_ID=reviewer_a
RUN_ID=NONE

OUTPUT_DIR=/data/projects/example/reviews
TARGET_STEM=domain_definitions
```

第二个独立评审者可以仅改变：

```text
REVIEWER_ID=reviewer_b
```

得到：

```text
domain_definitions_review_reviewer_a.md
domain_definitions_review_reviewer_b.md
```

若同一评审者需要再次运行：

```text
REVIEWER_ID=reviewer_a
RUN_ID=run_02
```

得到：

```text
domain_definitions_review_reviewer_a_run_02.md
```

这样可以同时保留：

- 不同评审者之间的独立结果；
- 同一评审者不同轮次的结果；
- 后续模型间共识与分歧分析所需的原始评审记录。
