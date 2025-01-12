正如你在第9章中所看到的，代码审查是软件开发的重要组成部分，特别是在大规模工作时。代码审查的主要目标是提高代码库的可读性和可维护性，评审过程从根本上支持这一点。然而，拥有一个定义明确的代码审查过程只是代码审查流程的一个部分。支持该过程的工具在其成功中也起着重要作用。

在本章中，我们将通过Google深受喜爱的内部系统Critique，来看看成功的代码审查工具的模样。Critique明确支持代码审查的主要功能，为审查者和作者提供审查的视图和对修改的评论能力。Critique还支持对哪些代码被检入代码库进行把关，这一点在 "评分 "更改一节中讨论。评论中的代码评审信息在进行代码考古时也很有用，遵循代码评审交互中解释的一些技术决策（例如，当缺少内联注释时）。尽管Critique并不是Google唯一使用的代码审查工具，但它是最受欢迎的工具。

我们在前面提到，Critique提供了支持代码审查目标的功能（我们在本章后面会详细介绍这种功能），但为什么它如此成功？Critique是基于Google的开发文化塑造的，其中包括代码审查作为工作流程的核心部分。这种文化影响转化为一套指导原则，Critique的设计就是为了强调这些原则：

在这些指导原则中，简单性可能对这个工具影响最大。我们考虑过增加许多有趣的功能，但我们决定不为支持一小部分用户而使模型更加复杂。

简单与工作流程的整合也有一个有趣的矛盾。我们考虑过，但最终决定不创建一个集代码编辑、审查和搜索于一体的 "代码中心 "工具。尽管Critique与其他工具有许多接触点，但我们还是有意识地决定将代码审查作为主要关注点。特征与评论相关，但在不同的子系统中实施。

代码审查可以在软件开发的许多阶段进行，如图19-1所示。评论评审通常在变更提交到代码库之前进行，也称为预提交评审。尽管第9章包含了对代码评审流程的简要描述，但在这里我们将其扩展，以描述Critique在每个阶段的关键作用。我们将在下面的章节中更详细地讨论每个阶段。

典型的审查步骤如下：

即使在审查过程开始后，整个系统也提供了很大的灵活性来偏离常规的审查流程。例如，评审员可以取消自己对修改的分配，或者明确地将其分配给其他人，而作者可以完全推迟评审。在紧急情况下，作者可以强行提交他们的修改，并在提交后对其进行审查。

当一个变更经过前面概述的阶段时，Critique 会发布可能被其他支持工具使用的事件通知。这种通知模式使Critique能够专注于成为一个主要的代码审查工具，而不是一个通用的工具，同时仍然能够集成到开发人员的工作流程中。通知实现了关注点的分离，这样Critique就可以直接发出事件，而其他系统则基于这些事件进行开发。

例如，用户可以安装使用这些事件通知的Chrome扩展。当一个变更需要用户注意时--例如，当更改需要用户注意时，由于轮到用户查看更改或某个预提交失败--该扩展会显示一个Chrome通知，其中有一个按钮可直接转到更改或使通知静音。我们发现，一些开发者非常喜欢即时的变更更新通知，但也有人选择不使用这个扩展，因为他们觉得这对他们的工作流程太过干扰。

代码审查工具应该在审查过程的各个阶段提供支持，不应该成为提交更改的瓶颈。在审查前的步骤中，让修改者在送出审查前更容易打磨修正，有助于减少审查者检查修改的时间。Critique在显示修改差异时，可以忽略空白处的修改，并突出显示纯移动的修改。Critique还可以显示构建、测试和静态分析器的结果，包括样式检查（如第9章中所讨论的）。

向作者展示修改的差异，让他们有机会拥有不同的思路：代码审查者的思路。Critique可以让修改作者像他们的审查者一样看到他们的修改的差异，也可以看到自动分析的结果。Critique还支持在审查工具中对变更进行轻量级的修改，并推荐合适的审查者。在发送请求时，作者也可以包括对修改的初步评论，提供机会直接向审查者询问任何公开的问题。让作者有机会像他们的审查者一样看到一个变化，可以防止误解。

为了给审阅者提供进一步的上下文，作者还可以将更改链接到特定的bug。评论使用自动完成服务来显示相关的bug，并对分配给作者的bug进行优先级排序。

代码审查过程的核心是理解代码变更本身。较大的变化通常比小的变化更难理解。因此，优化变更的差异是一个好的代码审查工具的核心要求。

在Critique中，这一原则转化为多个层面（见图19-2）。从优化的最长共同子序列算法开始，diffing组件得到了以下增强：

用户还可以以各种不同的模式查看diff，如叠加和并排。在开发Critique时，我们决定必须有并排的diff，使审查过程更容易。并排diff需要很大的空间：为了使它们成为现实，我们必须简化diff视图结构，因此没有边框，没有填充，只有diff和行号。我们还不得不使用各种字体和尺寸，直到我们有了一种差异视图，即使是在Critique启动时典型的屏幕宽度分辨率（1440像素）下，也能满足Java的100个字符行数限制。

为了使浏览diff的过程顺利进行，我们小心翼翼地避免浪费空间，并花费大量精力确保diff加载迅速，即使是图片和大文件和/或修改。我们还提供快捷键，以便在仅访问修改的部分时快速浏览文件。

当用户深入到文件层面时，Critique提供了一个UI小工具，紧凑地显示了文件的快照版本链；用户可以通过拖放来选择要比较的版本。这个小组件会自动折叠相似的快照，将注意力集中在重要的快照上。它帮助用户理解文件在变更中的演变；例如，哪些快照有测试覆盖率，已经被审查过，或者有评论。为了解决规模问题，Critique预取了所有内容，所以加载不同的快照非常快。

上传变更的快照会触发代码分析器（见第20章）。Critique将分析结果显示在变更页面上，按分析器状态筹码汇总，显示在变更描述下面，如图19-3所示，并在分析标签中详细说明，如图19-4所示。

分析器可以标记特定的结果，以红色突出显示，以提高可视性。仍在进行中的分析器由黄色卡片表示，否则显示灰色卡片。为了简单起见，Critique没有提供其他选项来标记或突出研究结果--可操作性是一个二元选项。如果一个分析器产生了一些结果（"研究结果"），点击卡片就可以打开研究结果。像评论一样，研究结果可以显示在diff里面，但风格不同，使它们容易区分。有时，研究结果也包括修正建议，作者可以预先查看这些建议，并从评论中选择应用。

例如，假设一个人发现行末有多余的空格，是违反风格的。更改页面将显示该linter的卡片。从卡片中，作者可以快速转到显示违规代码的diff，只需点击两次就能了解样式违规。大多数违规的linter也包括修复建议。通过点击，作者可以预览修正建议（例如，删除多余的空格），并通过另一次点击，在修改中应用修正。

谷歌拥有建立在Piper--其单体源代码库（见第16章）之上的工具，例如以下这些。

此外，还有一些服务可以提供变更元数据的上下文（例如，关于参与变更的用户或链接的错误）。Critique是一个很自然的熔炉，它可以快速地一键/悬停访问这些系统，甚至支持嵌入式UI，尽管我们需要小心不要牺牲简单性。例如，在Critique的修改页面上，作者只需要点击一次就可以在Cider中进一步编辑修改。我们支持使用Kythe在交叉引用之间进行导航，或在代码搜索中查看代码的主线状态（见第17章）。Critique链接到发布工具，这样用户就可以看到提交的变更是否在一个特定的版本中。对于这些工具，Critique更倾向于链接而不是嵌入，这样就不会分散对核心评审经验的注意力。这里的一个例外是测试覆盖率：测试是否覆盖代码行的信息由文件的diff视图中的行槽上的不同背景色显示（并非所有项目都使用此覆盖率工具）。

请注意，Critique和开发者的工作空间之间的紧密结合是可能的，因为工作空间存储在一个基于FUSE的文件系统中，可以在特定开发者的计算机之外访问。真相之源托管在云中，所有这些工具都可以访问。

在作者对更改的状态感到满意后，他们可以把它送去审查，如图19-5中所描述的。这需要作者挑选审查者。在一个小团队内，寻找审查者可能看起来很简单，但是即使在团队成员之间均匀地分配评论，也需要考虑像是谁休假的情况。为了解决这个问题，团队可以为收到的代码审查提供一个电子邮件别名。这个别名被一个叫做*GwsQ*的工具所使用（以最初使用这种技术的团队命名：

考虑到谷歌代码库的规模和修改代码的人数，很难找出谁最有资格审查你自己项目之外的变更。发现审查者在达到一定的规模时要考虑的问题。评论必须处理规模问题。Critique提供了建议一组足以批准更改的审阅者的功能。评审员的选择工具考虑到了以下因素。

为一个变更指定一个审查员会触发一个审查请求。该请求运行适用于该变更的 "预提交 "或预提交钩子；团队可以以多种方式配置与他们的项目相关的预提交。最常见的钩子包括以下内容：

审查过程开始后，作者和审查员协同工作，以达到提交高质量变更的目标。

发表评论是用户在Critique查看修改后的第二常见的行为（图19-6）。评论中的评论对所有人都是公开的。任何人--不仅仅是修改作者和指定的评审者--都可以对修改进行评论。

评论还提供了通过个人状态跟踪审查进度的能力。审阅者有复选框将最新快照中的单个文件标记为已审阅，以帮助审阅者跟踪他们已查看的内容。当作者修改文件时，所有审阅者都会清除该文件的“审阅”复选框，因为最新快照已更新。

当审查者看到一个相关的分析器发现时，他们可以点击 "请修复 "按钮，创建一个未解决的评论，要求作者解决这个问题。审查者还可以通过内联编辑文件的最新版本来建议修改。Critique将此建议转换为评论，并附上一个作者可以应用的修复程序。

如前所述，评论是随心所欲地起草的，但随后以原子方式 "发表"，如图19-7所示。这允许作者和审查者在发送评论之前确保他们对自己的评论感到满意。

加快审查过程的一个重要因素是了解什么时候轮到你干活了，特别是当有多个审查员被分配到一个变更时。如果作者想让软件工程师和负责该功能的用户体验人员审查他们的变更，或者为服务准备部署的SRE人员审查其更改，可能就是这种情况。通过管理每个变更的关注集，评论有助于确定下一个变更的关注者。

关注集由当前阻止更改的一组人组成。当评论者或作者在关注集中时，他们应该及时作出回应。Critique自动化地在用户发表评论时更新关注集，但用户也可以自己管理关注集。当变化中的评论者较多时，它的作用就更大了。在Critique中，关注集是通过将相关的用户名用黑体字显示出来的。

在我们实施这一功能后，我们的用户很难想象以前的状态。普遍的看法是：如果没有这个，我们是怎么过的？在我们实施这个功能之前，另一个选择是审查员和作者之间的聊天，以了解谁在处理一个变化。这个功能也强调了代码审查的轮流性质；总是至少轮到一个人采取行动。

仪表板页面是由一个名为*Changelist Search*的搜索系统提供的。Changelist Search索引了谷歌所有用户的所有可用变化的最新状态（包括提交前和提交后），并允许其用户通过基于正则表达式的查询来查找相关变化。每个仪表板部分都由对Changelist Search的查询来定义。我们花了很多时间来确保Changelist Search搜索足够快；所有的东西都被快速索引，这样作者和审稿人就不会被拖慢，尽管事实上谷歌同时出现了大量的并发更改。

为了优化用户体验（UX），Critique的默认仪表盘设置是在第一部分显示需要用户关注的变更，不过这也是可以定制的。还有一个搜索栏，可以对所有修改进行自定义查询，并浏览结果。作为一个审查员，你大多只需要关注集。作为一个作者，你大多数时候只需要看一下哪些东西还在等待审查，看看你是否需要修正。尽管我们在Critique用户界面的一些其他部分回避了可定制性，但我们发现用户喜欢以不同的方式设置他们的仪表板，而不影响基本的体验，就像每个人以不同的方式组织他们的电子邮件一样。

显示一个审查员是否认为一个变更是好的，归根结底是通过评论提供关注和建议。此外，还需要有一些机制来提供一个高水平的 "OK"。在谷歌，对一个变化的打分分为三个部分：

审查者的LGTM印章意味着 "我已经审阅了这个变更，相信它符合我们的标准，我认为在解决了未解决的评论之后，可以提交它。” 审查者的批准标识意味着 "作为一个把关人，我允许这个修改被提交到代码库中"。审查者可以将评论标记为未解决，这意味着作者需要对其采取行动。当变更至少有一个LGTM、足够的批准和没有未解决的评论时，作者可以提交变更。请注意，无论批准状态如何，每项变更都需要一个LGTM，以确保至少有两双眼睛查看该变更。这个简单的评分规则使Critique可以在修改准备好提交时通知作者（以绿色页眉的形式突出显示）。

在建立Critique的过程中，我们有意识地决定简化这一评分方案。最初，Critique有一个 "需要更多工作 "的评级，也有一个 "LGTM++"。我们所采用的模式是使 `LGTM/批准` 总是积极的。如果变更确实需要第二次审核，主要审查者可以添加内容，但无需LGTM/批准。在一个变化过渡到基本良好的状态后，审查员通常会相信作者会处理好小的编辑--无论变更大小如何，该工具都不需要重复LGTM。

这种评分方案也对代码审查文化产生了积极影响。审查者不能在没有任何有用反馈的情况下对一个改动竖起大拇指；所有来自审查者的负面反馈都必须与需要修复的具体内容相联系（例如，一个未解决的评论）。选择 "未解决的评论 "这一措辞也是为了听起来比较好。

批评包括一个打分板，在分析卡片旁边，有以下信息。

以这种方式呈现评分信息有助于作者快速了解他们仍然需要做些什么才能实现更改。

最后但并非最不重要的是，Critique有一个在审查后提交修改的按钮，以避免上下文切换到命令行界面。

除了Critique的核心用途是在源代码修改提交到版本库之前对其进行审查外，Critique还被用作变更考古的工具。对于大多数文件，开发者可以在代码搜索系统中查看过去修改某个文件的历史列表（见第17章），或者直接导航到某个修改。Google的任何人都可以浏览一般可查看文件的修改历史，包括对修改的评论和演变。这使未来的审计成为可能，并被用来了解更多的细节，如为什么会做出改变或如何引入bug。开发人员也可以使用这个功能来了解变化是如何被设计的，代码审查数据的汇总被用来制作培训。

尽管Critique是Google最常用的审查工具，但它并不是唯一的工具。由于Critique与我们的大型单体库和其他内部工具有紧密的相互依赖关系，所以Critique不能对外使用。正因为如此，在谷歌从事开源项目（包括Chrome和Android）或内部项目的团队，如果不能或不想托管在单片库中，就会使用另一种代码审查工具：Gerrit。

由于是开源的，Gerrit适应了更多的变体和更广泛的用例；Gerrit丰富的插件系统实现了与定制环境的紧密集成。为了支持这些用例，Gerrit还支持更复杂的评分系统。评审员可以通过给-2分否决变更，评分系统是高度可配置的。

你可以在[*https://www.gerritcodereview.com*](https://www.gerritcodereview.com/)了解更多关于Gerrit的信息，并看到它的运行情况。

在使用代码审查工具时，有一些隐含的权衡因素。Critique内置了许多功能，并与其他工具集成，使用户的审查过程更加完美。花在代码评审上的时间并不是比花在编码上的时间少多少，所以评审过程的任何优化都可以提高公司的生产效率。在大多数情况下，只有两个人（作者和审查者）在提交修改前达成一致，可以保持高速度。谷歌非常重视代码审查的培训方面，尽管它们更难以量化。

为了最大限度地减少评审更改所需的时间，代码评审过程应该无缝流动，简洁地告知用户需要关注的更改，并在人工评审员介入之前确定潜在问题（问题由分析人员和持续集成人员发现）。如果可能，在较长时间运行的分析完成之前，会显示快速分析结果。

信任和沟通是代码审查过程的核心。工具可以增强体验，但不能替代它们。与其他工具的紧密结合也是Critique成功的一个关键因素。

