# user:

我们现在 不讨论 SN350 安装 ubuntu，进入学习。你提供这个模板 未来甚至 可以程序化，自动生成，提示词 是吧？

# GPT-5.6 Sol high:

对，老师。**这个模板天然就适合以后程序化自动生成提示词**，而且我认为这正是它现在参数化设计的价值之一。

现在实际上已经隐约形成了一个很简单的“提示词编译”结构：

```text
评审提示词模板
        +
本次任务参数
        +
待评审对象
        +
评审依据文档
        ↓
生成本次完整 Prompt
        ↓
交给评审 AI
        ↓
生成独立评审报告
```

比如模板里已经有这些变量：

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

以后程序根本不需要人工复制、修改一大段提示词，只需要维护一次任务配置，例如概念上：

```yaml
workspace_dir: /data/projects/test

target:
  path: multi_domain_document_test.json
  stem: multi_domain_document_test

review_sources:
  review_spec: 软考AI领域Definition JSON评审规范20260809.md
  architecture_principles: AI操作系统声明式JSON设计原则与演进边界20260809.md
  business_spec: 软考AI平台核心目标与产品边界规范20260808.md

reviewer:
  id: reviewer_a

output:
  dir: /data/projects/test/reviews
```

程序拿这个配置去填模板，就可以生成：

```text
reviewer_a_prompt.md
```

换一个评审 AI，只改：

```yaml
reviewer:
  id: reviewer_b
```

就可以自动生成另一份提示词和另一个输出文件名。

---

更进一步，其实未来可以做到：

```text
Definition 文件进入评审目录
        ↓
程序识别任务
        ↓
读取 Prompt Template
        ↓
读取 Review Spec
        ↓
生成 reviewer_a Prompt
生成 reviewer_b Prompt
生成 reviewer_c Prompt
        ↓
调用不同模型
        ↓
得到三份独立 Review
        ↓
再交给“评审汇总能力”
        ↓
提取：
    共识问题
    分歧问题
    单模型意见
        ↓
最终由我们决定是否修改
```

所以这里已经不是简单的：

> “保存了一段以后复制粘贴的提示词。”

更准确地说，它开始变成：

> **Prompt Definition + Task Parameters → Prompt Instance**

这和我们前面学到的 `Definition / Instance` 分离其实是一脉相承的。

例如：

```text
多Definition领域JSON独立评审提示词模板
=
Prompt Definition

某次：
TARGET_JSON_PATH = xxx
REVIEWER_ID = reviewer_a
...
=
Prompt Instance
```

甚至以后连这个“提示词实例”都不一定需要永久保存。程序可以在 Runtime 根据模板和参数即时生成。

---

而且还有一层很重要：

现在我们是：

```text
Human
→ 填模板
→ 生成Prompt
```

以后完全可以变成：

```text
Review Task Definition
        ↓
Prompt Builder
        ↓
Prompt Template
        ↓
Model-specific Projection
        ↓
具体评审AI
```

例如同一个评审任务：

```text
评审这7个Definition
```

面对不同模型时，未来甚至可以投影出不同版本：

```text
强模型版本
→ Prompt较短，更多依赖评审规范

小型本地LLM版本
→ Prompt更显式，增加上下文和约束

Agent版本
→ 拆成“读取→识别→独立评审→交叉评审→输出”
```

但是它们背后的**业务评审任务仍然是同一个 Definition**。

这就非常接近我们之前说的 AI 操作系统思想：

> **业务定义稳定，消费者通过 Projection / Adapter 获取适合自己的形式。**

所以我的判断是：

> **是的，这份模板未来完全可以程序化，而且非常适合程序化。**

甚至再往后，它很可能不应该由人直接维护“最终提示词”，而应该逐渐演化成：

```text
Review Task Definition
+
Review Prompt Template
+
Model Projection
+
Runtime Parameters
=
最终 Prompt
```

不过现在先停在“模板可以参数化生成 Prompt”这一层就很好。**不用现在去开发 Prompt Builder。**我们现在最有价值的是继续推进软考 AI 产品设计，而不是提前把这套辅助基础设施做完。


# user:


# GPT-5.6 Sol high:

# user:


# GPT-5.6 Sol high:

# user:


# GPT-5.6 Sol high:

# user:


# GPT-5.6 Sol high:

# user:


# GPT-5.6 Sol high:

# user:


# GPT-5.6 Sol high: