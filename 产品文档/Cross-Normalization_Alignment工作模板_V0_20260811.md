# Cross-Normalization Alignment 工作模板 V0

## 0. 文档定位

本文档用于对两个或多个 Semantic Normalization（语义归一化）结果进行交叉对齐分析。

本模板属于**分析工作模板**，不是产品规范、事实清单、业务 Definition 或最终结论文档。

本模板的目标是：

> 在不提前进行事实裁决的前提下，识别不同归一化结果之间的稳定交集、粒度差异、语义边界差异、独有结构、冲突与不确定关系，为后续形成统一 Candidate Semantic Model 提供可追溯底稿。

---

# 1. Analysis Scope

## 1.1 本轮输入

### Normalizer A
- 名称：
- 文件：
- Theme 数量：
- 归一化风格简述：

### Normalizer B
- 名称：
- 文件：
- Theme 数量：
- 归一化风格简述：

如存在更多 Normalizer，可继续增加。

## 1.2 原始语料范围

- 原始材料：
- 是否相同：
- 是否存在不同背景参照材料：

## 1.3 本轮只处理

- 语义是否相同；
- Theme 之间的对应关系；
- 拆分 / 合并粒度差异；
- 语义边界差异；
- 一方是否遗漏某个独立语义；
- 表观冲突；
- 不确定对齐关系；
- 合并后是否存在语义损失风险。

## 1.4 本轮不处理

- 事实真伪裁决；
- 来源可信度排序；
- 多数投票；
- 最终业务 Definition；
- 最终“现实 V1”；
- 产品方案；
- 用户分类；
- 教学策略；
- AI能力设计；
- 最终 Theme 数量收敛。

---

# 2. Crosswalk Relation Definitions

每一组 Theme 对齐关系应尽量使用以下关系类型之一。

## 2.1 semantic_equivalent

两个 Theme 的核心语义、对象和边界基本一致。

```text
A-Txxx ≈ B-Tyyy
```

判断重点：

- 是否描述同一个现实；
- 是否承担相同语义职责；
- 边界差异是否仅为措辞差异。

---

## 2.2 one_to_many_split

一方使用一个 Theme 表达，另一方拆成多个独立 Theme。

```text
A-T001
    ↓
B-T004
B-T005
```

本轮只记录：

- 拆分结构；
- 各子语义是否可独立成立；
- 合并是否存在信息损失风险。

本轮不立即裁决最终应该拆还是合。

---

## 2.3 many_to_one_merge

一方多个 Theme 被另一方合并为一个 Theme。

```text
A-T004
A-T005
    ↓
B-T002
```

与 `one_to_many_split` 本质相同，但从反方向记录，便于完整覆盖检查。

---

## 2.4 partial_overlap

两个 Theme 有明显语义交集，但各自还包含对方没有覆盖的独立内容。

判断重点：

- 共同部分是什么；
- A 独有部分是什么；
- B 独有部分是什么；
- 为什么不能直接视为同义。

---

## 2.5 parent_child

一个 Theme 是上位抽象，另一个是其具体机制、表现、后果或子类。

```text
Parent Theme
    ↓
Child Theme
```

注意：

> 上下位关系不等于重复关系。

---

## 2.6 normalizer_unique

某个独立语义仅被一个 Normalizer 明确识别为 Theme，并且：

- 另一方没有独立 Theme；
- 另一方其他 Theme 的 description / boundary / source view 中也没有实际覆盖该语义。

只有同时满足以上条件，才可标记为：

`normalizer_unique`

注意：

> Normalizer Unique ≠ Source Unique。

`Source Unique` 指原始语料中的独有观点。

`Normalizer Unique` 指归一化模型在语义组织层面的独有发现。

---

## 2.7 uncertain_alignment

现有材料不足以可靠判断两个 Theme 属于：

- 同义；
- 上下位；
- 部分重叠；
- 粒度差异；
- 或真正不同。

此时不得强行归类。

标记：

`uncertain_alignment`

留待后续人工或业务评审。

---

# 3. Stable Semantic Intersections

本节记录两个 Normalizer 均明确识别、且语义边界基本一致的稳定主题。

## X001

- **临时语义名称**：
- **Normalizer A Theme**：
- **Normalizer B Theme**：
- **Relation**：`semantic_equivalent`
- **共同核心语义**：
- **边界差异**：
- **是否存在信息损失风险**：低 / 中 / 高
- **说明**：
- **后续动作**：暂时对齐 / 后续复核

---

# 4. Merge / Split Differences

本节专门记录两个 Normalizer 在语义粒度上的不同。

## MS001

- **临时语义名称**：
- **Normalizer A**：
- **Normalizer B**：
- **Relation**：`one_to_many_split` / `many_to_one_merge`

### A 的语义结构
- Theme：
- 核心语义：
- 边界：

### B 的语义结构
- Theme 1：
  - 核心语义：
  - 边界：
- Theme 2：
  - 核心语义：
  - 边界：

### 共同部分

### 被拆出的独立语义

### 如果采用合并结构，可能损失什么

### 如果采用拆分结构，可能产生什么冗余

### semantic_loss_risk
- 低 / 中 / 高

### 本轮结论
- 暂不裁决 / 倾向继续拆分 / 倾向允许合并

---

# 5. Boundary / Partial-overlap Differences

## BD001

- **Normalizer A Theme**：
- **Normalizer B Theme**：
- **Relation**：`partial_overlap` / `parent_child`

### 共同语义

### A 额外包含

### B 额外包含

### 为什么不能直接视为同义

### 是否存在上下位关系

### semantic_loss_risk
- 低 / 中 / 高

### 后续建议

---

# 6. Normalizer Unique Findings

## 6.1 Normalizer A 独有

### NU-A001

- **Theme**：
- **核心语义**：
- **是否在 Normalizer B 其他 Theme 中隐含覆盖**：是 / 否 / 不确定
- **是否是真正 normalizer_unique**：是 / 否 / 待确认
- **与原始 Source Unique 的关系**：
- **说明**：

## 6.2 Normalizer B 独有

### NU-B001

- **Theme**：
- **核心语义**：
- **是否在 Normalizer A 其他 Theme 中隐含覆盖**：
- **是否是真正 normalizer_unique**：
- **与原始 Source Unique 的关系**：
- **说明**：

---

# 7. Conflict / Uncertain Alignment

## 7.1 Apparent Conflict

### C001

- **涉及 Theme**：
- **来源 A 判断**：
- **来源 B 判断**：
- **冲突点**：
- **是否可能只是时间、范围、层级或口径不同**：
- **本轮处理**：不裁决 / 保留双方

## 7.2 Uncertain Alignment

### U001

- **涉及 Theme**：
- **当前可能关系**：
- **不确定原因**：
- **为什么不能强行合并**：
- **需要什么信息才能继续判断**：
- **本轮处理**：`uncertain_alignment`

---

# 8. Crosswalk Matrix

| Crosswalk ID | 临时语义名称 | Normalizer A Theme | Normalizer B Theme | Relation | 核心交集 | 边界差异 | semantic_loss_risk | 后续动作 |
|---|---|---|---|---|---|---|---|---|
| X001 |  |  |  | semantic_equivalent |  |  | 低 | 暂时对齐 |
| X002 |  |  |  | one_to_many_split |  |  | 中 | 后续评审 |
| X003 |  |  |  | partial_overlap |  |  | 高 | 人工评审 |

---

# 9. semantic_loss_risk 判断规则

## 9.1 Low

合并后：

- 不会改变对象职责；
- 不会丢失独立判断条件；
- 不会影响未来 AI 对不同业务对象的区分。

## 9.2 Medium

合并后可能：

- 丢失部分语义层级；
- 模糊“结构事实”和“具体表现”；
- 但仍可通过 description 或 sub-definition 恢复。

## 9.3 High

合并后可能导致：

- 不同业务对象职责混淆；
- Reality / Gap / Barrier / Capability / Strategy 等对象被混在一起；
- 不同推理条件被视为同一个条件；
- 后续 AI 无法稳定区分不同判断来源；
- 原始独立语义难以恢复。

原则：

> Theme 数量多少不是核心评价标准。  
> 是否损失未来业务推理所需的语义区分能力，才是核心标准。

---

# 10. Preservation Check

## 10.1 Theme 覆盖

- Normalizer A 的所有 Theme 是否都已进入某个 Crosswalk？
- Normalizer B 的所有 Theme 是否都已进入某个 Crosswalk？
- 是否存在未解释的 Theme？

## 10.2 隐含覆盖检查

对于看似“独有”的 Theme：

- 是否其实被另一方合并进更大的 Theme？
- 是否存在于另一方 description 中？
- 是否存在于另一方 semantic boundary 中？
- 是否存在于另一方 related / conflict / uncertain 关系中？

不得把“没有单独建 Theme”误判成“没有发现”。

## 10.3 粒度检查

- 是否把粒度不同误判成业务观点不同？
- 是否把上下位关系误判成重复？
- 是否把相关关系误判成同义？

## 10.4 语义损失检查

- 哪些合并可能造成信息损失？
- 哪些拆分只是措辞差异，没有真实业务价值？
- 哪些独立 Theme 对未来 AI 业务判断具有必要区分价值？

## 10.5 Source Unique / Normalizer Unique 检查

必须明确区分：

```text
Source Unique
=
原始材料中只有某个来源提出

Normalizer Unique
=
归一化模型独立识别出的语义组织结果
```

两者不得混用。

---

# 11. 本轮最终输出

完成后只形成以下五类结果：

## 11.1 Stable Semantic Intersections
双方稳定一致的核心语义。

## 11.2 Merge / Split Differences
双方对同一语义的粒度差异。

## 11.3 Boundary / Partial-overlap Differences
双方对语义边界、上下位关系或部分重叠的不同处理。

## 11.4 Normalizer Unique Findings
一方真正发现而另一方未覆盖的独立语义。

## 11.5 Conflict / Uncertain Alignment
双方存在表观冲突或当前无法可靠对齐的内容。

---

# 12. 本轮禁止直接形成

不得在本轮直接形成：

- 最终业务事实；
- 最终“教材与考试现实 V1”；
- 最终 Theme 数量；
- 来源可信度排名；
- 多数投票结论；
- 产品策略；
- 用户痛点分类；
- AI教学策略；
- 最终 Definition。

本轮完成后的下一步才是：

```text
Cross-Normalization Alignment
        ↓
业务语义边界裁决
        ↓
Unified Candidate Semantic Model
        ↓
证据等级与事实审查
        ↓
最终 Reality Model
```

---

# 13. 核心工作原则

> 不以 Theme 数量多少评价质量。

> 不把“相关”当成“同义”。

> 不把“没有独立建 Theme”当成“没有发现”。

> 不把上下位关系当成重复。

> 不把 Source Unique 和 Normalizer Unique 混为一谈。

> 对无法可靠对齐的内容保持不确定，不强行收敛。

> 优先保护未来 AI 业务推理需要的语义边界。

> 最终 Theme 数量由业务语义自然决定，而不是由任何预设数量决定。
