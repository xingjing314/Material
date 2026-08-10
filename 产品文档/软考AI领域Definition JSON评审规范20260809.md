---
document_meta:
  id: ruankao_ai_domain_definition_json_review_spec
  type: review_specification
  status: active
  version: "1.0"
  date: "2026-08-09"
  language: zh-CN

document_role:
  purpose: >
    为AI模型评审软考AI领域Definition JSON提供统一、可复用的判断标准，
    使GPT、Codex、Grok、本地LLM或其他模型在缺少原始对话上下文时，
    仍能够判断Definition JSON的语义是否清晰、职责是否纯净、结构是否稳定、
    是否容易引发误推断，以及是否适合后续RAG、引用、映射和长期演进。
  primary_reader: "AI model"
  secondary_reader: "human"
  nature: >
    本文是领域Definition JSON的评审规范，不是JSON编写模板、
    JSON Schema规范、Runtime规范、MCP规范、API规范或具体实现任务书。
  current_scope: >
    当前主要用于评审软考AI产品中的Domain Definition JSON。
    后续可在保持核心原则不变的前提下扩展到其他AI产品领域定义。

applies_to:
  - Domain Definition JSON

does_not_apply_to:
  - User Instance
  - Runtime State
  - Pattern
  - Mapping
  - Relation
  - Workflow
  - Teaching Strategy
  - JSON Schema
  - Projection
  - API DTO
  - MCP Tool Schema

source_principles:
  - "AI操作系统声明式JSON设计原则与演进边界20260809.md"

reading_rules:
  - >
    评审目标是判断Definition是否健康，而不是重新设计整个业务。
  - >
    不得因为Java、Python、MCP、某个Agent框架或某个LLM的偏好，
    要求领域Definition改变自身业务语义或结构。
  - >
    不得主动把Instance、Mapping、Runtime、Projection、评分体系、
    用户取值或教学策略加入待评审Definition。
  - >
    优先识别真正影响语义、职责、长期引用、AI理解和RAG使用的问题，
    不为了显示评审深度而制造无必要修改。
  - >
    “还可以优化”不等于“当前不合格”。

review_outcomes:
  - PASS
  - PASS_WITH_MINOR_CHANGES
  - REVISE
---

# 软考AI领域Definition JSON评审规范

## 1. 评审任务定义

本规范用于指导AI模型对软考AI领域Definition JSON进行标准化评审。

评审模型的任务包括：

1. 判断Definition所表达的领域概念是否清晰；
2. 判断AI在缺少原始对话上下文时是否能够正确理解；
3. 识别容易导致模型过度推断或错误推断的语义；
4. 检查命名、ID、结构和字段职责是否稳定一致；
5. 检查Definition是否混入Instance、Mapping、Runtime或其他非Definition职责；
6. 检查单个JSON是否具有足够的自解释能力；
7. 检查JSON被RAG局部召回后是否仍能保持基本语义完整；
8. 检查其是否具备长期引用、扩展和映射能力；
9. 当存在多个Definition时，检查维度之间是否发生职责重叠或越界；
10. 给出最小必要修改建议。

评审模型不应把评审任务理解为：

> 重新进行产品设计。

当业务含义本身已经明确、合理且符合上位原则时，不得仅因为存在另一种可行表达方式，就主动重构Definition。

---

## 2. 评审对象定义

### 2.1 什么是Domain Definition JSON

Domain Definition JSON用于声明：

- 某个领域概念是什么；
- 该概念的稳定身份是什么；
- 它包含哪些子概念或观察维度；
- 每个子概念是什么意思；
- 它自身的语义边界是什么。

典型形式例如：

```json
{
  "resource_constraints": {
    "id": "dimension.resource_constraints",
    "name": "资源约束",
    "description": "...",
    "constraint_dimensions": [
      {
        "id": "resource.available_study_time",
        "name": "可投入学习时间",
        "description": "..."
      }
    ]
  }
}
```

Definition负责描述：

> **“它是什么。”**

Definition原则上不负责描述：

> **“某个具体用户是什么值。”**

也不负责描述：

> **“它在当前业务流程中如何运行。”**

### 2.2 Definition不等于以下对象

#### User Instance

例如：

```json
{
  "user_id": "user_001",
  "organization_environment": "organization.public_institution"
}
```

这描述具体用户，不是Definition。

#### Runtime State

例如：

```json
{
  "current_node": "review",
  "attempt": 2
}
```

这是运行状态，不是Definition。

#### Pattern

例如：

> 高理解 + 高探究 + 低考试边界控制型用户。

这是多个特征组合后的模式，不是基础Definition。

#### Mapping / Relation

例如：

> 某组织环境与某参与目标存在某种业务关系。

这属于外部关系，不属于实体自身定义。

#### Workflow

例如：

> A执行后调用B，B失败后重试C。

这是执行编排，不属于Definition。

#### Teaching Strategy

例如：

> 对碎片时间用户每次只讲10分钟内容。

这是教学策略，不属于资源约束Definition自身。

#### JSON Schema

JSON Schema负责验证结构是否合法，不等于业务语义Definition。

---

## 3. 上位设计原则

Definition JSON评审必须服从以下原则。

### 3.1 Everything is Declarative

重要业务概念应尽量能够通过稳定、清晰的声明体系表达。

但：

> 声明化不等于所有运行逻辑都必须写进Definition。

### 3.2 Declare, not Execute

Definition声明：

- 是什么；
- 有哪些组成；
- 含义是什么；
- 边界是什么。

Definition不负责执行具体业务逻辑。

### 3.3 Self-description, External Mapping

实体主要描述自己。

以下内容原则上应由外部Mapping、Relation、Policy或其他对象表达：

- 谁使用它；
- 谁调用它；
- 它属于哪条具体业务流程；
- 它和其他Definition是什么关系；
- 它如何被组合进某个产品。

### 3.4 Contract over Implementation

领域Definition不应绑定：

- Java Class；
- Python函数；
- LangGraph Node；
- MCP方法；
- 数据库表；
- 某个具体LLM；
- 某个具体Agent框架。

具体实现可以改变，领域契约应尽量保持稳定。

### 3.5 Canonical Definition

领域Definition是业务语义的权威来源。

LLM View、MCP View、UI View、API View等应通过Adapter或Projection派生，不应独立维护成另一套业务事实。

### 3.6 Definition / Instance / Runtime分离

必须区分：

```text
Definition
    = 概念是什么

Instance
    = 某个具体对象的取值

Runtime State
    = 当前执行状态
```

三者不能因为都可以使用JSON表示而混为同一种对象。

### 3.7 领域定义不迎合消费者

评审时不得因为以下消费者需要某种结构而要求Domain Definition直接改变：

- LLM；
- MCP；
- Agent；
- Java；
- Python；
- UI；
- API；
- RAG。

消费者应适配领域Definition，而不是反过来。

---

## 4. 标准评审维度

### 4.1 AI语义可理解性

#### 评审目标

判断一个没有原始聊天上下文的模型，是否能够准确理解：

- 这个Definition是什么；
- 它解决什么语义问题；
- 每个子项是什么意思；
- 它不负责什么。

#### 正向标准

Definition应做到：

- root key语义明确；
- `name`和`description`一致；
- `description`能够解释用途和边界；
- 子项名称与父概念语义一致；
- 不依赖口语化背景才能理解；
- 不需要猜测隐含前提。

#### 典型问题

例如：

```json
{
  "type": "高级"
}
```

模型无法知道：

- 什么高级；
- 高级表示能力、成熟度还是优先级；
- 与哪个领域相关。

这类表达AI语义可理解性较差。

---

### 4.2 AI误推断风险

#### 评审目标

判断Definition是否容易诱导模型从已声明事实继续推出未声明结论。

#### 重点风险

典型错误推断包括：

```text
高校研究生
→ 学习能力强
```

```text
事业单位
→ 一定为了晋升参加考试
```

```text
战略/生态认知
→ 开发、架构、项目管理都很强
```

```text
备考时间6个月
→ 知识掌握更好
```

```text
信息化能力提升
→ 平台应进入深层技术培训
```

#### 正向标准

Definition应：

- 区分背景与个人能力；
- 区分认知层次与具体知识；
- 区分资源投入与学习结果；
- 区分参与目标与平台核心目标；
- 必要时在`description`中明确禁止常见错误推断。

#### 评审原则

> 对AI系统而言，减少错误推断的重要性不低于增加信息量。

---

### 4.3 命名与结构一致性

#### 评审目标

检查JSON在命名和结构层面是否稳定、统一、可长期维护。

#### 检查内容

重点检查：

- root key；
- `id`；
- `name`；
- `description`；
- namespace；
- `snake_case`；
- 数组字段命名；
- 子对象结构；
- 同类Definition之间的一致性。

#### 推荐原则

领域JSON key统一优先使用：

```text
snake_case
```

自然语言语义说明统一优先使用：

```text
description
```

具有独立身份、可被引用、可独立修改或有独立生命周期的对象应具有稳定`id`。

#### ID评审重点

应检查：

- ID是否具有稳定语义；
- ID是否过度依赖中文显示名称；
- ID是否可以在名称修改后继续保持；
- namespace是否清楚；
- 不同领域是否存在冲突风险。

---

### 4.4 Definition职责纯度

#### 评审目标

判断Definition是否只描述“它是什么”。

#### 不应混入的内容

包括但不限于：

- 某个具体用户的取值；
- 分数；
- 权重；
- 用户状态；
- 课程要求；
- 用户缺口；
- 教学策略；
- 调用顺序；
- Workflow；
- Runtime State；
- Provider；
- Java/Python实现；
- MCP绑定；
- API地址；
- UI展示要求；
- 业务挂接关系。

#### 典型错误

```json
{
  "organization_environment": {
    "name": "高校研究生",
    "learning_ability": "high",
    "available_time": "high"
  }
}
```

问题：

> 组织环境越权定义了学习能力和资源约束。

---

### 4.5 单文件自解释能力

#### 评审目标

假设只把当前一个JSON文件交给本地LLM，而不给其他Definition和原始聊天记录：

> 模型是否仍能理解它的核心语义和边界？

#### 正向标准

单个Definition应尽量做到：

- root object含有明确`name`和`description`；
- 父级`description`说明该维度的用途；
- 父级`description`说明容易混淆的边界；
- 子项描述不需要依赖大量外部背景才能理解。

#### 典型风险

如果一个Definition只有：

```json
{
  "types": [
    "陌生",
    "工具",
    "系统",
    "组织"
  ]
}
```

本地LLM很难独立确定这些词表达的是：

- 认知成熟度；
- 学习等级；
- 课程阶段；
- 技术能力。

因此自解释能力不足。

---

### 4.6 RAG局部召回友好性

#### 评审目标

考虑JSON进入RAG后可能被切片、局部召回。

需要判断：

> 即使只召回某个子对象，它是否仍具有足够语义？

#### 重点检查

避免过度使用：

- “该项”；
- “上述”；
- “此类”；
- “前者”；
- “后者”；
- “如前所述”。

因为局部chunk被单独召回时，这些指代可能失去上下文。

#### 正向标准

子对象的`description`应尽量包含：

- 自身是什么；
- 自身属于什么语义范围；
- 自身最重要的边界。

但不要求每个子项重复父Definition全部内容。

目标是：

> **局部可理解，而不是机械重复。**

---

### 4.7 长期扩展与稳定引用能力

#### 评审目标

判断Definition是否适合作为未来：

- Course；
- Persona；
- Mapping；
- Relation；
- Agent；
- Policy；
- Registry；
- RAG；
- Control Plane

等对象引用的稳定基础。

#### 重点检查

应判断：

- 独立概念是否有稳定ID；
- 中文显示名称修改后是否影响引用；
- 是否把当前课程特征写死；
- 是否把当前产品关系写死；
- 是否绑定具体技术实现；
- 是否把未来Mapping职责提前塞进Definition；
- 是否允许在不修改本体的情况下被不同业务引用。

#### 核心原则

> Definition应尽量比具体课程、具体产品、具体实现活得更久。

---

## 5. 跨维度一致性评审

当同时评审多个Definition时，除了单文件质量，还必须检查职责边界是否重叠。

当前软考AI基础用户维度包括：

```text
参与目标
组织环境
信息化认知成熟度
信息化知识结构
学习/认知特征
资源约束
```

评审模型应重点检查：

### 5.1 参与目标

回答：

> 用户为什么参加软考，以及希望获得什么现实价值。

不应直接定义：

- 用户组织环境；
- 用户学习能力；
- 课程要求；
- 教学深度。

### 5.2 组织环境

回答：

> 用户长期处于什么外部工作、学习和组织背景。

不应直接推断：

- 学习能力；
- 知识水平；
- 参与目标；
- 可投入时间。

### 5.3 信息化认知成熟度

回答：

> 用户理解信息化时能够稳定采用什么认知尺度和抽象层次。

不应直接等同于：

- 技术能力；
- 知识掌握范围；
- 学习路线；
- 教学升级阶段。

### 5.4 信息化知识结构

回答：

> 用户已经具备哪些信息化相关知识和经验。

不应直接定义：

> 用户“缺什么”。

用户缺口应由：

```text
用户知识结构
×
具体课程/考试知识要求
→
知识差距
```

通过未来Mapping得到。

### 5.5 学习/认知特征

回答：

> 用户在理解加工、记忆保持、深度探究和学习调节方面具有哪些观察维度。

不应在Definition层直接定义：

- 用户评分；
- 用户Pattern；
- 用户类别；
- 教学策略。

### 5.6 资源约束

回答：

> 用户当前可用于备考的外部时间条件。

不得推断：

- 知识掌握程度；
- 学习能力；
- 考试能力；
- 用户意愿。

---

## 6. 高风险错误模式

### 6.1 Definition混入Instance

错误示例：

```json
{
  "learning_cognition": {
    "user_score": 85
  }
}
```

如果该JSON的目的只是定义学习/认知特征，则具体用户分数属于Instance。

### 6.2 Definition混入Mapping

错误示例：

```json
{
  "organization_environment": {
    "public_institution": {
      "recommended_goal": "职位晋升"
    }
  }
}
```

组织环境与参与目标之间的关系应由Mapping表达。

### 6.3 用环境属性推断个人属性

错误：

```text
高校研究生
→ 学习能力强
```

环境只能提供解释背景，不能直接定义个体能力。

### 6.4 用时间推断知识掌握

错误：

```text
备考6个月
→ 掌握程度高
```

资源约束和知识结构必须分离。

### 6.5 把认知成熟度当学习路径

错误：

```text
陌生认知
→ 工具使用认知
→ 概念认知
→ 系统认知
```

并把它设计成必须依次完成的教学路线。

认知成熟度描述当前认知尺度，不等于课程升级路径。

### 6.6 把知识结构写成Persona

错误：

```text
开发型用户
规划型用户
项目经理型用户
```

如果目的是定义Knowledge Structure，则应优先描述知识域，而不是直接定义典型人物。

### 6.7 把专业方向错误排列成成熟度等级

错误：

```text
懂开发
→ 懂架构
→ 懂规划
→ 懂管理
```

这些属于不同专业知识方向，不天然形成成熟度顺序。

### 6.8 为适配技术协议污染领域Definition

错误：

```json
{
  "mcp_method": "tools/call",
  "java_class": "GoalService",
  "python_handler": "handle_goal"
}
```

如果这些字段进入业务Definition，则实现层污染领域层。

### 6.9 ID随中文名称修改

如果：

```text
name:
单位投标
```

以后改为：

```text
企业投标/项目人员资质
```

只要概念核心语义未改变，稳定ID原则上不应跟着变化。

### 6.10 description偷偷加入业务规则

`description`应解释概念和边界。

不应偷偷加入没有在领域设计中确认过的：

- 权重；
- 自动路由规则；
- 教学行为；
- 用户推断规则；
- Runtime动作。

---

## 7. 评审严重程度

### critical

表示：

> 存在违反上位架构原则、严重职责混淆或会导致领域模型长期错误的问题。

例如：

- Definition与Runtime混合；
- User Instance写入Definition；
- 领域Definition直接绑定具体实现；
- 核心业务语义错误。

处理：

> 必须修改后才能通过。

### major

表示：

> 当前可以理解，但存在明显误推断、职责边界、长期扩展或跨维度冲突风险。

例如：

- 组织环境直接暗示学习能力；
- 知识结构定义“缺什么”；
- 认知成熟度混入专业方向。

处理：

> 建议修改后通过。

### minor

表示：

> 不影响核心业务含义，但存在命名、自解释性、RAG友好性或局部一致性问题。

例如：

- `remark`与`description`混用；
- 某个ID namespace不够统一；
- 某个description可以更独立。

处理：

> 可以在不改变核心结构的情况下轻量修正。

### info

表示：

> 不是问题，仅属于可选建议或未来考虑事项。

不得把`info`级建议作为Definition不通过的依据。

---

## 8. Definition JSON通过条件

Definition JSON满足以下条件时，可以判定通过：

1. 核心业务语义清晰；
2. 没有critical问题；
3. 没有会导致明显错误解释的major问题；
4. Definition职责保持纯净；
5. 不依赖具体开发语言和实现框架；
6. 关键独立概念具有稳定ID；
7. 单文件能够基本自解释；
8. RAG局部召回时主要子项仍具有基本语义；
9. 与已知其他Definition不存在明显职责冲突；
10. 当前结构没有堵死未来Mapping、Relation、Course、Persona或其他消费者的合理引用。

需要特别强调：

> **“存在可选优化”不等于“不通过”。**

如果业务含义正确、职责清晰、结构稳定，则不应为了追求形式上的完美不断重构。

---

## 9. 标准评审输出格式

评审模型应尽量按照以下结构输出。

```text
评审对象：
<Definition名称或root key>

总体结论：
PASS / PASS_WITH_MINOR_CHANGES / REVISE

总体评价：
<简短说明>

1. AI语义可理解性
   结论：
   问题：
   严重程度：

2. AI误推断风险
   结论：
   问题：
   严重程度：

3. 命名与结构一致性
   结论：
   问题：
   严重程度：

4. Definition职责纯度
   结论：
   问题：
   严重程度：

5. 单文件自解释能力
   结论：
   问题：
   严重程度：

6. RAG局部召回友好性
   结论：
   问题：
   严重程度：

7. 长期扩展与稳定引用能力
   结论：
   问题：
   严重程度：

跨维度一致性：
<如果没有其他Definition上下文，明确写“无法评估”>

必须修改：
- ...

建议修改：
- ...

可选优化：
- ...

无需修改：
- ...

最终结论：
<一句话>
```

如果不存在某类问题，应明确写：

```text
无
```

而不是为了填充格式强行制造问题。

---

## 10. 修改建议原则

评审模型给出修改建议时，应遵循：

> **最小必要修改原则。**

优先级：

```text
能通过修改description解决
→ 不改结构

能通过修改一个key解决
→ 不重构整个Definition

能通过统一ID解决
→ 不重新分类业务

业务语义正确
→ 不因为模型个人偏好重新设计
```

修改建议必须区分：

### 必须修改

不修改会导致语义错误、职责混淆或严重长期风险。

### 建议修改

不修改仍能使用，但存在明显误推断、可维护性或一致性风险。

### 可选优化

属于表达、格式或未来扩展层面的非必要改进。

---

## 11. 评审禁止事项

评审模型不得：

1. 擅自改变已经确认的业务含义；
2. 因个人偏好重新进行业务分类；
3. 为了“更工程化”提前引入Runtime；
4. 自动增加评分、权重、状态、置信度等Instance字段；
5. 自动增加Mapping、Relation或Workflow；
6. 把未来能力当成当前必须实现能力；
7. 因Java、Python、MCP、Agent框架惯例强制修改Domain Definition；
8. 把领域Definition直接改成Tool Schema；
9. 把RAG消费格式直接写入领域Definition；
10. 为了追求统一而删除真实存在的业务差异；
11. 把“更简洁”自动视为“更正确”；
12. 把“更详细”自动视为“更正确”；
13. 把“还可以优化”视为“当前不合格”；
14. 在没有业务证据时主动增加新的领域概念；
15. 将评审演变为完整产品重构。

---

## 12. 参考示例

### 12.1 合格示例：资源约束子维度

```json
{
  "id": "resource.study_time_stability",
  "name": "学习时间稳定性",
  "description": "描述用户可投入学习时间在不同日期或阶段中的稳定程度和可预测程度，包括较稳定、周期性波动或高度不稳定等可能状态。该维度用于反映学习资源供给在时间轴上的变化特征，不评价用户的学习意愿或自律程度。"
}
```

为什么较好：

- 语义清晰；
- 有稳定ID；
- 描述自身；
- 明确了“不评价自律程度”的边界；
- 单独召回仍基本可理解。

### 12.2 错误示例：组织环境越权推断个人能力

```json
{
  "id": "organization.graduate_student",
  "name": "高校研究生",
  "learning_ability": "high",
  "available_time": "high"
}
```

问题：

- `learning_ability`属于学习/认知相关属性；
- `available_time`属于资源约束；
- 组织环境不能直接定义这两个值。

建议：

> 删除越权字段，只描述高校研究生所处的教育组织背景。

### 12.3 错误示例：知识结构提前定义课程缺口

```json
{
  "id": "knowledge.software_development",
  "name": "软件开发",
  "missing": [
    "信息化规划",
    "项目管理",
    "政策法规"
  ]
}
```

问题：

> “缺什么”必须相对于具体课程要求才能成立。

正确关系应是：

```text
User Knowledge Structure
×
Course Knowledge Requirement
→
Knowledge Gap
```

因此Knowledge Structure Definition自身只描述已有知识域。

### 12.4 错误示例：认知成熟度被写成教学路线

```json
{
  "maturity_path": [
    "陌生认知",
    "工具使用认知",
    "概念认知",
    "局部系统认知",
    "系统级综合认知"
  ],
  "must_progress_sequentially": true
}
```

问题：

> 信息化认知成熟度描述当前能够稳定采用的认知尺度，不构成必须依次完成的课程学习路径。

### 12.5 错误示例：技术实现污染领域定义

```json
{
  "id": "goal.salary_growth",
  "name": "薪资提升",
  "java_handler": "SalaryGoalService",
  "mcp_tool": "evaluate_salary_goal"
}
```

问题：

> 领域Definition依赖具体实现。

建议：

> 保留业务定义；实现绑定由独立Provider / Adapter / Mapping层负责。

---

## 13. 评审模型最终行为原则

当模型使用本文评审Definition JSON时，应始终遵循：

> **先理解业务，再判断边界，再检查结构，最后才提出修改。**

不得按照：

> “我通常会怎么设计JSON”

替代：

> “当前业务究竟需要表达什么”。

最终评审目标不是制造一份形式上最复杂、最工程化的JSON，而是保证：

> **领域Definition语义清晰、职责纯净、AI可理解、RAG可使用、长期可引用，并且不会提前绑定具体课程、流程或技术实现。**
