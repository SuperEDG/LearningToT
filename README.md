# 技术分析：大语言模型在解决问题中的应用——“思维树”方法

## 1. 研究主题

### 1.1 应用总结

本研究探讨了大语言模型在解决需要探索、策略性预测或初始决策至关重要的任务中的应用。研究引入了一个新的框架，名为“思维树”（Tree of Thoughts, ToT），它允许大语言模型通过考虑多种不同的推理路径并自我评估选择来执行深思熟虑的决策制定，以及在必要时进行前瞻或回溯以做出全局选择。

### 1.2 技术挑战

现有的语言模型，即便是最新的GPT-4，也面临着在处理需要深层规划或搜索的任务时的挑战，例如24点游戏、创意写作和迷你纵横字谜。这些任务需要推理、数学、常识、词汇推理能力，并且需要系统性的规划或搜索。

## 2. 关键发现

### 2.1 解决的挑战

为了解决这些挑战，研究者设计了一种规划过程，灵感来自人类认知科学和早期的人工智能研究。他们提出了“思维树”框架，它通过维护一个思维树来积极解决问题，每个“思考”都是朝着解决问题的中间步骤的连贯语言序列。

### 2.2 技术框架

- （1）思维树（ToT）框架：这个框架允许语言模型自我评估不同的中间思考步骤，通过一个深思熟虑的推理过程解决问题。该方法的新颖之处在于，它通过语言模型自我评估和思考来实现搜索启发式，而以往的搜索启发式要么是编程的，要么是学习的。
- （2）核心方法：[1] 通过广度优先搜索（BFS）或深度优先搜索（DFS）等搜索算法，系统地探索思维树，并进行前瞻和回溯；[2] 结合基于语言的能力来生成和评估不同的思想，从而支持不同级别的思考、生成和评估思考的不同方式，以及适应不同问题性质的不同搜索算法。

### 2.3 实验结果

实验表明，ToT框架显著提高了语言模型在需要非平凡规划或搜索的三个新任务上的问题解决能力：24点游戏、创意写作和迷你纵横字谜。例如，在24点游戏中，使用链式思考提示的GPT-4只解决了4%的任务，而我们的方法成功率达到了74%。通过系统的消融实验，研究还分析了这些选择是如何影响模型性能的，并讨论了未来更好地训练和使用语言模型的方向。


该文档介绍了一个名为“思维树（Tree of Thoughts，ToT）”的新范式，它允许语言模型（LMs）更加深思熟虑地解决问题。ToT的设计灵感来自于人类解决问题的方式，特别是如何在组合问题空间中进行搜索。现有的利用LMs解决问题的方法存在两个关键缺点：它们既不探索思考过程中的不同延续路径，也不整合任何形式的规划或回溯来评估这些选项。ToT旨在解决这些不足。

以下是ToT方法的核心组成和步骤：

1. **思维分解（Thought Decomposition）**：ToT通过利用问题的属性来设计和分解中间思维步骤。一个“思维”应该足够“小”，以便LMs可以生成有前景和多样的样本，但也应该足够“大”，以便LMs可以评估其解决问题的前景。

2. **思维生成器（Thought Generator）**：根据树状态，使用两种策略之一来生成下一个思维步骤的候选项：通过CoT提示独立地抽样思维，或者使用“propose prompt”顺序提出思维。

3. **状态评估器（State Evaluator）**：评估器测量各个状态向解决问题方向的进展，作为搜索算法的启发式方法。这里提出了一种新方法，即使用LM有意识地对状态进行推理。它可以通过独立评估每个状态或在状态之间进行投票来实现。

4. **搜索算法（Search Algorithm）**：在ToT框架内，可以根据树的结构使用不同的搜索算法。文档探讨了两种相对简单的搜索算法：宽度优先搜索（BFS）和深度优先搜索（DFS），并保留更高级的算法（如A*和MCTS）供未来研究。

整个ToT方法的算法流程可以概括为：

- 输入问题并将其分解为可以通过LM进行管理和探索的状态。
- 通过思维生成器在每个状态中产生多个潜在的后续思维路径。
- 使用状态评估器评估这些路径，根据特定的启发式规则选择最有前景的路径。
- 通过所选的搜索算法（如BFS或DFS）在问题空间中导航，寻找可能的解决方案。
- 循环这个过程，直到找到一个可行的解决方案或达到搜索限制为止。

ToT方法具有几个概念上的优势，包括通用性、模块化、适应性和便利性，无需额外培训即可使用预训练的LM。这种方法强调了在解决问题过程中进行更多的启发式搜索和路径评估的重要性，从而在多个任务中实现更强的表现。


下面是关于“24点游戏”任务的一个实验流程示例，该游戏要求参与者使用基本算术运算（加、减、乘、除）处理4个数字，使其结果为24。

实验目标：
- 探索和比较不同提示方法（标准IO提示、思维链提示CoT和思维树ToT）在解决“24点游戏”中的效率和成功率。

实验设置：
1. **数据收集**：从4nums.com网站上爬取了1,362个游戏实例，这些游戏按照人类解决时间的长短进行了排序。实验选择了编号为901至1,000的难度相对较大的游戏进行测试。

2. **任务配置**：每个任务都要求模型生成一个有效的等式，该等式的结果为24，且必须且仅一次使用所有输入的数字。成功率的衡量标准是在100个游戏中的成功次数。

3. **基线实验设置**：
   - 对于标准的输入输出（IO）提示，我们使用了5个上下文示例。
   - 对于思维链（CoT）提示，我们增加了每对输入输出之间的3个中间等式，每个等式作用于剩下的两个数字。

4. **思维树（ToT）设置**：
   - “24点游戏”被分解为3个步骤，每个步骤是一个中间等式。我们使用广度优先搜索（BFS），在每一步保留最佳的5个候选选项。
   - 在每个树节点，我们提取剩余的数字，并提示语言模型提出一些可能的下一步操作。

实验流程：
1. **准备阶段**：收集并准备数据，选择难度较高的游戏进行测试。配置不同的提示方法。

2. **运行基线实验**：使用标准IO提示和CoT提示，对每个游戏运行100次采样以计算平均性能。此外，还使用了一种基于IO样本的迭代细化方法，最多进行10次迭代。

3. **运行ToT实验**：
   - 实施广度优先搜索，在每一步骤根据模型的建议保留最好的5个候选解决方案。
   - 对每个思考候选进行评估，将其分类为“确定/可能/不可能”三类，以便于接近24的正确解决方案。

4. **结果对比和分析**：
   - 收集并分析所有实验方法的结果数据。
   - 发现标准IO、CoT提示方法在任务上的表现不佳，而ToT提示方法表现更好。
   - 进一步通过错误分析，确定了哪一步骤最容易导致失败，并指出了直接从左到右解码的问题。

5. **报告**：整理所有收集到的数据、发现和结论，准备完整的研究报告。

通过这个实验，研究人员展示了在解决需要搜索或规划的问题时，如何使用语言模型以及不同的提示技术。尽管在处理这类任务时遇到了挑战，但实验表明，通过ToT的深思熟虑搜索，能够大大提高问题解决的成功率。


本节内容讨论了与“思维树（Tree of Thoughts, ToT）”方法相关的不同研究领域，并对其进行了分析和比较。下面是主要内容的总结：

1. **规划和决策制定**：
   - 语言模型（LMs）因其丰富的常识知识而在提出合理计划方面显示出潜力。
   - ToT方法通过在每个解决问题的步骤中同时考虑多个潜在可行的计划，并继续执行最有前景的计划，扩展了现有的规划方法。
   - 与传统的需要专门训练奖励和策略模型的决策过程不同，ToT方法利用LM本身提供决策制定的价值估计。

2. **自我反思**：
   - 利用LLMs评估自己预测的可行性在解决问题中变得越来越重要。
   - 相关工作已经引入了“自我反思”机制，其中LMs为其生成的候选项提供反馈。
   - 与现有方法相比，ToT方法更具通用性，可以处理GPT-4在使用标准提示时准确率非常低的复杂任务。

3. **程序引导的LLM生成**：
   - 近期的一些方法通过符号程序引导来组织LM的行为。
   - 与ToT方法不同，这些方法的树通过采样外部段落而不是LM自己的思想来扩展，并且没有反思或投票步骤。

4. **经典搜索方法**：
   - ToT方法可以被视为经典搜索方法的现代演绎。
   - 它可视为一种启发式搜索算法，比如A*，其中每个搜索节点的启发式由LM的自我评估提供。

第六部分讨论了ToT方法的局限性、未来方向、更广泛的影响，以及结论。

**局限性和未来方向**：
- ToT可能不适用于GPT-4已经擅长的许多任务。
- 尽管如此，随着LMs在更多真实世界的决策应用中的部署，可能会出现更复杂的任务。
- ToT方法需要比采样方法更多的资源来提高任务性能，但其模块化的灵活性允许用户自定义性能与成本之间的权衡。

**更广泛的影响**：
- ToT框架提升了LMs自主智能决策和解决问题的能力。
- 尽管目前的任务限于推理和搜索问题，未来与外部环境或人类的互动应用可能带来潜在的危险。

**结论**：
- LMs的“系统1”可以通过基于搜索问题解决方案的可能路径的“系统2”得到有益的增强。
- ToT框架为将古典关于问题解决的见解转化为现代LMs的实际方法提供了一种方式。