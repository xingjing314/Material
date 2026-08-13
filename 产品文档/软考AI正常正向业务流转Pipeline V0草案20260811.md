# 软考AI正常正向业务流转 Pipeline V0 草案

## 0. 文档定位

本文档用于定义软考AI系统在一次正常用户问答中的**最小正向业务流转骨架**。

本文档当前只描述：

- 用户一次正常提问如何进入系统；
- 系统如何形成当前任务；
- 如何加载与当前问题相关的业务上下文；
- 如何形成当前状态、要求状态与相关差距；
- 如何形成回答目标与回答策略；
- 如何调用知识与能力形成回答；
- 如何记录本次交互并更新动态状态。

本文档当前**不定义**：

- 异常流程；
- 用户再次提问后的多轮流程；
- Limit限制；
- 程序异常；
- 业务异常；
- 校验机制；
- 各Pipeline节点内部的详细活动；
- Agent编排；
- Runtime执行顺序的工程实现；
- 最终JSON Schema；
- 数据库结构；
- RAG、LLM、Tool等具体技术实现。

本文档描述的是业务语义级的正常正向流转骨架。

---

# 1. 核心原则

## 1.1 Current Question 始终是当前任务中心

系统后续加载的用户状态、考试要求、教材与考试现实、Evidence、Gap、Strategy等对象，只用于：

- 限定；
- 解释；
- 校正；
- 补充；
- 约束；

AI对Current Question的理解与回答。

这些对象不得替代Current Question，也不得擅自把用户当前问题改写成另一个任务。

---

## 1.2 Pipeline定义业务职责，不等同于程序Runtime顺序

本Pipeline用于定义：

> 一次正常回答中需要存在的业务语义职责及其基本关系。

它不表示：

- 模型内部Chain-of-Thought；
- 固定程序Chain；
- LangGraph Graph；
- Agent Workflow；
- Runtime必须严格按本文档逐节点串行执行。

进入正式开发前，可基于本Pipeline对Runtime架构、JSON结构和动态加载机制进行重构与优化。

---

## 1.3 当前先冻结骨架，不提前展开内部活动

V0阶段优先确定：

> 有哪些核心业务节点，以及节点之间最基本的正向关系。

各节点内部未来可继续扩展：

- Definition；
- Instance；
- Evidence；
- Diagnostic Type；
- Strategy Type；
- RAG；
- Tool；
- Validation；
- Observation；
- State Update机制。

---

# 2. Normal Forward Pipeline V0

```text
1. User Question
        ↓
2. Current Question
        ↓
3. Relevant Context Loading
        ↓
4. Current State
        ↓
5. Required State
        ↓
6. Relevant Gap / Barrier
        ↓
7. Response Objective
        ↓
8. Strategy
        ↓
9. Knowledge / Capability Execution
        ↓
10. Answer
        ↓
11. Interaction Observation
        ↓
12. Dynamic State Update
        ↓
13. 本次正常流转结束
```

---

# 3. Pipeline节点最小语义

## 3.1 User Question

**定义：**

用户向软考AI系统发出的原始问题、请求或学习任务。

**当前职责：**

作为一次正常业务流转的起点。

---

## 3.2 Current Question

**定义：**

系统基于User Question形成的当前直接任务对象。

**当前职责：**

明确：

> 用户现在正在问什么。

Current Question始终是本次回答的任务中心。

---

## 3.3 Relevant Context Loading

**定义：**

围绕Current Question，加载本次回答真正相关的业务语义对象。

**当前职责：**

避免每次回答都加载全部用户、考试、教材、课程和系统信息。

未来可能按需加载的内容包括但不限于：

- Platform Goal；
- User Current State；
- Exam Required State；
- Course Required State；
- Textbook / Exam Reality；
- Evidence；
- 相关Definition；
- 相关Instance。

V0阶段不定义具体加载算法。

---

## 3.4 Current State

**定义：**

与Current Question直接相关的用户当前状态。

**当前职责：**

回答：

> 针对当前问题，系统目前掌握的用户相关状态是什么。

Current State可能来自动态User Instance及相关Evidence。

V0阶段不定义具体状态维度、计算方法或置信度机制。

---

## 3.5 Required State

**定义：**

针对Current Question，为满足当前考试、课程、知识点、题型或学习任务要求所需要达到的相关状态。

**当前职责：**

回答：

> 针对当前问题，用户需要达到什么状态。

Required State可包括：

- Exam Required State；
- Course Required State。

V0阶段不定义Formal Required State模型。

---

## 3.6 Relevant Gap / Barrier

**定义：**

基于Current State、Required State及相关Evidence，识别与Current Question直接相关的差距或障碍。

**当前职责：**

回答：

> 当前问题真正相关的差距或障碍在哪里。

Relevant Gap / Barrier不是简单数学意义上的：

```text
Required State - Current State
```

未来可能存在多种Gap / Barrier Type，例如：

- Knowledge Gap；
- Concept Boundary Gap；
- Application Gap；
- Composition Gap；
- Expression Gap；
- Resource Barrier；
- 其他可扩展类型。

具体Gap Type可在未来Definition Registry或数据库中维护，并在Runtime中按需加载。

---

## 3.7 Response Objective

**定义：**

在Current Question保持任务中心的前提下，结合Relevant Gap / Barrier及相关上下文，形成的本次回答目标。

**当前职责：**

回答：

> 这一次回答需要完成什么。

Response Objective不得覆盖或替代Current Question。

它用于在“用户直接问题”和“为把当前问题回答好所需的补充内容”之间建立边界。

---

## 3.8 Strategy

**定义：**

系统根据Response Objective及相关业务语义，为本次回答形成的处理策略。

**当前职责：**

回答：

> 这一次应该怎么回答。

Strategy与前面的业务语义模块分离。

业务语义和Diagnostic模块负责提供判断条件；

Strategy负责消费这些判断条件并形成行动选择。

V0阶段不展开Strategy内部活动。

---

## 3.9 Knowledge / Capability Execution

**定义：**

根据Strategy调用本次回答所需的知识来源与系统能力。

**当前职责：**

执行本次回答需要的知识获取、能力调用和内容组织。

未来可能涉及：

- 教材知识；
- 考试大纲；
- 题目；
- Reality；
- RAG；
- LLM；
- Tool；
- 外部知识；
- 其他能力模块。

V0阶段不定义具体技术实现。

---

## 3.10 Answer

**定义：**

系统针对Current Question形成并向用户返回的本次回答。

**当前职责：**

作为本次正常问答的用户可见输出。

V0阶段暂不展开：

- Answer Validation；
- Guardrail；
- Quality Check；
- Evidence Check；
- Boundary Check。

---

## 3.11 Interaction Observation

**定义：**

从本次正常交互中形成的、可供系统后续使用的有效观察信息。

**当前职责：**

记录本次交互可能产生的用户学习、认知、行为或任务相关观察。

V0阶段不定义：

- Observation Type；
- Observation Extraction；
- Observation Confidence；
- Observation Validation。

---

## 3.12 Dynamic State Update

**定义：**

基于本次Interaction Observation及新增Evidence，对相关动态状态进行更新。

**当前职责：**

使系统的用户相关状态能够随着持续使用逐步变化，而不是保持静态。

未来可能更新：

- User Current State；
- Evidence；
- Confidence；
- Relevant Gap Instance；
- 其他动态Instance。

V0阶段不定义具体更新算法。

---

## 3.13 End

**定义：**

本次正常正向业务流转完成。

本节点仅表示当前单次正常流转结束。

当前不展开：

- 用户继续提问；
- 多轮上下文继承；
- 异常处理；
- Retry；
- Limit；
- 程序故障；
- 业务故障。

---

# 4. 一句话Pipeline

```text
用户提问
→ 形成当前问题
→ 加载相关上下文
→ 确定当前状态
→ 确定要求状态
→ 识别相关差距/障碍
→ 形成回答目标
→ 制定策略
→ 调用知识与能力
→ 形成回答
→ 记录交互观察
→ 更新动态状态
→ 本次正常流转结束
```

---

# 5. 与动态JSON的关系

当前Pipeline不等于最终JSON结构。

现阶段只确认：

> 未来系统可围绕Current Question动态加载与当前任务相关的业务语义子树，并在回答完成后更新相关动态Instance。

开发前JSON重构时，可进一步映射为：

```text
Stable Schema
+
Definition Registry
+
Dynamic Instance
+
Runtime Context
```

其中：

- Schema尽量稳定；
- Definition可扩展；
- Instance动态变化；
- JSON Value可动态更新；
- 相关子孙JSON可根据Current Question按需加载；
- Key原则上保持相对稳定，仅在必要场景下扩展。

---

# 6. V0冻结边界

当前V0暂时冻结以下内容：

1. Current Question是一次回答的任务中心。
2. Context围绕Current Question按相关性加载。
3. Current State与Required State保持语义分离。
4. Gap / Barrier作为独立Diagnostic职责存在。
5. Response Objective与Current Question保持分离。
6. Strategy与前面的业务语义/Diagnostic模块保持分离。
7. Knowledge / Capability Execution作为Strategy之后的执行层存在。
8. Answer不是系统长期状态的终点。
9. 正常交互可形成Observation。
10. Observation可进一步更新Dynamic State。
11. 当前Pipeline只定义正常正向骨架。
12. 详细内部活动和工程实现留待后续扩展及开发前重构。

---

# 7. 后续扩展方向

本文档后续版本可逐步扩展：

- 各节点Definition；
- 节点输入/输出；
- Typical Strategy Usage；
- Evidence关系；
- Gap Type Registry；
- Strategy Type Registry；
- Reality Layer；
- Dynamic JSON映射；
- Runtime Context；
- Answer Validation；
- Multi-turn Flow；
- Limit Flow；
- Business Exception Flow；
- Technical Exception Flow；
- Agent / Tool Orchestration。

当前V0不展开上述内容。
