我们看到，编程和软件工程之间有三个关键的区别：时间、规模和权衡取舍。在一个软件工程项目中，工程师需要更多关注时间成本和需求变更。在软件工程中，我们需要更加关注规模和效率，无论是对我们生产的软件，还是对生产软件的组织。最后，作为软件工程师，我们被要求做出更复杂的决策，其结果风险更大，而且往往是基于对时间和规模增长的不确定性的预估。

在谷歌内部，我们有时会说，"软件工程是随着时间推移的编程。"编程当然是软件工程的一个重要部分：毕竟，编程首先是生成新软件的方式。如果你接受这一区别，那么很明显，我们可能需要在编程任务（开发）和软件工程任务（开发、修改、维护）之间进行划分。时间的增加为编程增加了一个重要的新维度。这是一个立方体三维模型不是正方形的二维模型，距离不是速度。软件工程不是编程。

了解时间对程序的影响的一种方法是思考“代码的预期生命周期是多少？”这个问题的合理答案大约相差100,000倍。想到生命周期几分钟的代码和想象将持续执行几十年的代码是一样合理。通常，周期短的代码不受时间的影响。对于一个只需要存活一个小时的程序，你不太可能考虑其适应基础库、操作系统（OS）、硬件或语言版本的新版本。这些短期系统实际上“只是”一个编程问题，就像在一个维度中压缩得足够扁的立方体是正方形一样。随着我们扩大时间维度，允许更长的生命周期，改变显得更加重要。在十年或更长的时间里，大多数程序依赖关系，无论是隐式的还是显式的，都可能发生变化。这一认识是我们区分软件工程和编程的根本原因。

我们不是指“开发生命周期”，而是指“维护生命周期”——代码将持续构建、执行和维护多长时间？这个软件能提供多长时间的价值？

这种区别是我们所说的软件可持续性的核心。如果在软件的预期生命周期内，你能够对任何有价值的变化做出反应，无论是技术还是商业原因，那么你的项目是可持续的。重要的是，我们只关注能力——你可能因为缺乏价值或其他优先事项而选择不进行特定的升级。当你基本上无法对基础技术或产品方向的变化做出反应时，你就把高风险赌注押在希望这种变化永远不会变得至关重要。对于短期项目，这可能是一个安全的赌注。几十年后，情况可能并非如此。

另一种看待软件工程的方法是考虑规模。有多少人参与？随着时间的推移，他们在开发和维护中扮演什么角色？编程任务通常是个人的创造行为，但软件工程任务是团队的工作。早期定义软件工程的尝试为这一观点提供了一个很好的定义：“多人开发的多版本程序”。这表明软件工程和程序设计之间的区别是时间和人的区别。团队协作带来了新的问题，但也提供了比任何单个程序员更多的潜力来产生有价值的系统。

团队组织、项目组成以及软件项目的策略和实践都支配着软件工程复杂性。这些问题是规模所固有的：随着组织的增长和项目的扩展，它在生产软件方面是否变得更加高效？我们的开发工作流程随着我们的发展，效率会提高，还是版本控制策略和测试策略的成本会相应增加？从软件工程的早期开始，人们就一直在讨论沟通和人员的规模问题，一直追溯到《人月神话》。这种规模问题通常是策略的问题，也是软件可持续性问题的基础：重复做我们需要做的事情要花多少钱？

我们还可以说，软件工程与编程的不同之处在于需要做出的决策的复杂性及其风险。在软件工程中，我们经常被迫在几个路径之间做评估和权衡，有时风险很高，而且价值指标不完善。软件工程师或软件工程负责人的工作目标是实现组织、产品和开发工作流程的可持续性和管理扩展成本为目标。考虑到这些投入，评估你的权衡并做出理性的决定。有时，我们可能会推迟维护更改，甚至接受扩展性不好的策略，因为我们知道需要重新审视这些决策。这些决策应该是明确的和清晰的递延成本。

在软件工程中很少有一刀切的解决方案，这本书也是如此。考虑到“这个软件能使用多久”的合理答案是100,000倍，而“你的组织中有多少工程师”的范围可能是10,000，谁知道“你的项目有多少计算资源可用”的范围是多少，谷歌的经验可能与你的经验不一致。在本书中，我们的目标是介绍我们在构建和维护软件方面的发现，这些软件预计将持续数十年，拥有数万计的工程师和遍布世界的计算资源。我们发现在这种规模下所需要的大多数做法也能很好地适用于复杂度较小的系统：考虑一下这是一个我们认为在你们扩大的时候可以做的很好的工程生态系统的报告。在一些地方，超大规模有其自身的成本，我们更倾向于不付出额外的管理成本。我们发出警告。希望如果你的组织发展到足以担心这些成本，你可以找到更好的答案。

在我们讨论团队合作、文化、策略和工具的细节之前，让我们首先阐述一下时间、规模和权衡这些主要主题。

当新手学习编程时，编码的生命周期通常以小时或天为单位。编程作业和练习往往是一次编写的，几乎没有重构，当然也没有长期维护。这些程序通常在初始生产后不再重建或再次执行。这在教学环境中并不奇怪。也许在中学或中学后教育，我们可以找到团队项目课程或实践论文。如果是这样的，项目很可能是学生们的代码生命周期超过一个月左右的时间。这些开发人员可能需要重构一些代码，也许是为了应对不断变化的需求，但他们不太可能被要求处理环境的更大变化。

我们还在常见的行业环境中找到短期代码的开发人员。移动应用程序的生命周期通常很短，而且无论好坏，完全重写都是相对常见的。初创初期的工程师可能会正确地选择关注眼前目标而不是长期投资：公司可能活得不够长，无法从回报缓慢的基础设施投资中获益。一个连续工作多年的开发人员可能有10年的开发经验，并且鲜少或根本没有维护任何预期存在超过一年或两年的软件的经验。

另一方面，一些成功的项目实际上有无限的生命周期：我们无法准确地预测Google搜索、Linux内核或Apache HTTP服务器项目的终点。对于大多数谷歌项目，我们必须假设它们将无限期地存在，我们无法预测何时不需要升级依赖项、语言版本等。随着他们生命周期的延长，这些长期项目最终会有一种不同于编程任务或初创企业发展不同的感受。

考虑图1-1，它演示了两个软件项目的“预期生命周期”的范围。对于从事预期生命周期为小时的任务的程序来说，什么类型的维护是合理的？也就是说，如果在编写一个Python脚本时出现了一个新版本的操作系统，该脚本将执行一次，你应该放弃你正在做的事情并升级吗？当然不是：升级并不重要。但与此相反，谷歌搜索停留在20世纪90年代的操作系统版本上显然是一个问题。

预期生命周期范围的低点和高点表明某处有一个过渡。介于一次性计划和持续十年的项目，发生了转变：一个项目必须开始对不断变化的外部因素做出反应。对于任何一个从一开始就没有升级计划的项目，这种转变可能会非常痛苦，原因有三个，每一个都会使其他原因变得复杂：

因此，在经历过一次升级（或中途放弃）之后，高估后续升级的成本并决定“永不再升级”是非常合理的。得出这个结论的公司最终承诺放弃并重写代码，或决定不再升级。有时，更负责任的答案不是采取常规的方法避免痛苦的任务，而是投入资源用于减轻痛苦。这一切都取决于升级的成本、提供的价值以及相关项目的预期生命周期。

不仅完成了第一次大升级，而且达到可靠地保持当前状态的程度，这是项目长期可持续性的本质。可持续性要求规划和管理所需变化的影响。对于谷歌的许多项目，我们相信我们已经实现了这种持续能力，主要是通过试验和错误。

那么，具体来说，短期编程与生成预期生命周期更长的代码有何不同？随着时间的推移，我们需要更多地意识到“正常工作”和“可维护”之间的区别。识别这些问题没有完美的解决方案。这是不幸的，因为保持软件的长期可维护性是一场持久战。

如果你正在维护一个由其他工程师使用的项目，那么关于“有效”与“可维护”最重要的一课就是我们所说的海勒姆定律：   

根据我们的经验，这个定律在任何关于软件随时间变化的讨论中都是一个主导因素。它在概念上类似于熵：对随时间变化和维护的讨论必须了解海勒姆定律，正如对效率或热力学的讨论必须注意熵一样。仅仅因为熵从不减少并不意味着我们不应该努力提高效率。在维护软件时，"海勒姆定律 "会适用，但这并不意味着我们不能对它进行规划或试图更好地了解它。我们可以减轻它，但我们知道，它永远不可能被根除。

海勒姆定律代表了一种实践知识，即使有最好的规划、最好的工程师和可靠的代码评审实践，我们也不能假设完全遵守已发布的契约或最佳实践。作为API所有者，通过明确地接口约定，你将获得一定的灵活性和自由度，但在实践中，给定更改的复杂性和难度还取决于用户对你的API的一些可观察行为的有用程度。如果用户不能依赖这些东西，那么你的API将很容易更改。如果有足够的时间和足够的用户，即使是最无害的变更也会破坏某些东西；你对变更价值的分析必须包含调查、识别和解决这些缺陷的难度。

考虑哈希迭代排序的例子。如果我们在一个基于散列的集合中插入五个元素，我们将以什么顺序将它们取出？

大多数程序员都知道哈希表是无序的。很少有人知道他们使用的特定哈希表是否打算永远提供特定的排序。这似乎不起眼，但在过去的一二十年中，计算行业使用这类类型的经验不断发展：

因此，如果你问任何一位专家“我能为我的散列容器设定一个的输出序列吗？”这位专家大概会说“不”。总的来说，这是正确的，但过于简单。一个更微妙的回答是，“如果你的代码是短期的，没有对硬件、语言运行时或数据结构的选择进行任何更改，那么这样的假设是正确的。如果你不知道代码的生命周期，或者你不能保证你所依赖的任何东西都不会改变，那么这样的假设是不正确的。”，即使你自己的实现不依赖于散列容器顺序，也可能被隐式创建这种依赖关系的其他代码使用。例如，如果库将值序列化为远程过程调用（RPC）响应，则RPC调用程序可能会根据这些值的顺序结束。

这是“可用”和“正确”之间区别的一个非常基本的例子。对于一个短期的程序，依赖容器的迭代顺序不会导致任何技术问题。另一方面，对于一个软件工程项目来说，如果有足够的时间，这种对已定义顺序的依赖是一种风险使更改迭代顺序变得有价值。这种价值可以通过多种方式体现出来，无论是效率、安全性，还是仅仅是数据结构的未来验证，以允许将来的更改。当这一价值变得清晰时，你需要权衡这一价值与破坏开发人员或客户的痛苦之间的平衡。

一些语言专门在库版本之间，甚至在执行相同程序的随机散列排序，以防止依赖关系。但即使这样，也会出现一些令人惊讶的海勒姆定律：有些代码使用散列迭代排序作为一个低效的随机数生成器。现在消除这种随机性将破坏这些用户原使用方式。正如熵在每个热力学系统中增加一样，海勒姆定律适用于所有可观察到的行为。

思考一下用“当前可用”和“一直可用”心态编写的代码之间的差异，我们可以提取出一些明确的关系。将代码视为具有（高度）可变生命周期需求的构件，我们可以开始对编程风格进行分类：依赖其依赖性的脆弱和未发布特性的代码可能被描述为“黑客”或“聪明”而遵循最佳实践并为未来规划的代码更可能被描述为“干净”和“可维护”。两者都有其目的，但你选择哪一个关键取决于所讨论代码的预期生命周期。我们常说，“如果‘聪明’是一种恭维，那就是程序，如果‘聪明’是一种指责，那就是软件工程。”  

在所有关于时间和对变化作出反应的讨论中，隐含着一个假设，即变化可能是必要的？

与本书中的其他内容一样，这取决于实际情况。我们很乐意承诺“对于大多数项目，在足够长的时间内，它们下面的一切都可能需要更改。”如果你有一个用纯C编写的项目，没有外部依赖项（或者只有保证长期稳定性的外部依赖项，如POSIX），你完全可以避免任何形式的重构或困难的升级。C在提供多方面稳定性方面做了大量工作，这是其首要任务。

大多数项目更多地接触到不断变化的基础技术。大多数编程语言和运行时的变化要比C大得多。甚至用纯C实现的库也可能改变以支持新特性，这可能会影响下游用户。从处理器到网络库，再到应用程序代码，各种技术都会暴露安全问题。你的项目所依赖的每一项技术都有一些（希望很小）包含关键bug和安全漏洞的风险，这些漏洞只有在你开始依赖它之后才会暴露出来。如果你无法部署心脏出血或缓解推测性执行漏洞（如熔毁和幽灵）的修补程序，因为你假设（或保证）什么都不会改变，这是一场巨大的赌博。

效率的提高使情况更加复杂。我们希望为数据中心配备经济高效的计算设备，特别是提高CPU效率。然而，早期谷歌的算法和数据结构在现代设备上效率较低：链表或二叉搜索树仍能正常工作，但CPU周期与内存延迟之间的差距不断扩大，影响了看起来还像“高效”代码。随着时间的推移，升级到较新硬件的价值会降低，而无需对软件进行相应的设计更改。向后兼容性确保了旧系统仍能正常工作，但这并不能保证旧的优化仍然有用。不愿意或无法利用这些机会可能会带来巨大的成本。像这样的效率问题尤其微妙：最初的设计可能完全符合逻辑，并遵循合理的最佳实践。只有在向后兼容的变化演变之后，新的、更有效的选择才变得重要。虽然没有犯错误，但随着时间的推移，变化仍然是有价值的。

像刚才提到的那些担忧，没有对可持续性的长期项目进行投入是存在巨大风险。我们必须能够应对这些问题，并利用好机会，无论它们是否直接影响我们，或者仅仅表现为我们所建立的技术的过渡性封闭中。**变化本质上不是好事**。我们不应该仅仅为了改变而改变。但我们确实需要有能力改变。如果我们考虑到最终的必要性，我们也应该考虑是否加大投入使这种能力变得简单易用（成本更低）。正如每个系统管理员都知道的那样，从理论上知道你可以从磁带恢复是一回事，在实践中确切地知道如何进行恢复以及在必要时需要花费多少钱是另一回事。实践和专业知识是效率和可靠性的重要驱动力。

正如（SRE）这本书所指出的，谷歌的生产系统作为一个整体是人类创造的最复杂的系统之一。构建这样复杂系统并保持其平稳运行所涉及的复杂性需要我们组织和全球各地的专家进行无数小时的思考、讨论和重构。因此，我们已经写了一本书，讲述了保持机器以这种规模运行的复杂性。

本书的大部分内容都集中在产生这种系统的组织规模的复杂性，以及我们用来保持系统长期运行的过程。再考虑代码库可持续性的概念：“当你能够安全地改变你应该改变的所有事情，你的组织的代码库是可持续的，并且可以为你的代码库的生命做这样的事情。”隐藏在能力的讨论中也是成本的一个方面：如果改变某事的代价太大，它可能会被推迟。如果成本随着时间的推移呈超线性增长，运营显然是不可扩展的。最终，时间会占据主导地位，出现一些意想不到的情况，你必须改变。当你的项目范围扩大了一倍，并且你需要再次执行该任务时，它会是劳动密集型的两倍吗？下次你是否有足够的人力资源来解决这个问题？

人力成本不是唯一需要扩大规模的有限资源。就像软件本身需要与传统资源（如计算、内存、存储和带宽）进行良好的可扩展一样，软件的开发也需要进行扩展，包括人力时间的参与和支持开发工作流程的计算资源。如果测试集群的计算成本呈超线性增长，每个季度人均消耗更多的计算资源，那么你的项目就走上了一条不可持续的道路，需要尽快做出改变。

最后，软件系统最宝贵的资产*代码库*本身也需要扩展。如果你的构建系统或版本控制系统随着时间的推移呈超线性扩展，也许是由于内容增长和不断增加的变更日志历史，那么可能会出现无法持续的情况。许多问题，如“完成完整构建需要多长时间？”、“拉一个新的版本库需要多长时间？”或“升级到新语言版本需要多少成本？”都没有受到有效的监管，并且效率变得缓慢。这些问题很容易地变得像温水煮青蛙；问题很容易慢慢恶化，而不会表现为单一的危机时刻。只有在整个组织范围内提高意识并致力于扩大规模，才可能保持对这些问题的关注。

你的组织生产和维护代码所依赖的一切都应该在总体成本和资源消耗方面具有可扩展性。特别是，你的组织必须重复做的每件事都应该在人力方面具有可扩展性。从这个意义上讲，许多通用策略似乎不具有可扩展性。

只要稍加练习，就可以更容易地发现具有不可扩展的策略。最常见的情况是，可以通过考虑施加在单个设计并想象组织规模扩大10倍或100倍。当我们的规模增大10倍时，我们会增加10倍的工作量，而我们的工程师能跟得上吗？我们的工程师的工作量是否随着组织的规模而增长？工作是否随着代码库的大小而变多？如果这两种情况都是真实的，我们是否有机制来自动化或优化这项工作？如果没有，我们就有扩展问题。

考虑传统的弃用方式。我们在第15章中详细讨论了弃用，但常用的弃用方法是扩展问题的一个很好的例子。开发了一个新的小组件。决定是每个人都应该使用新的，停止使用旧的。为了激发这一点，项目负责人说：“我们将在8月15日删除旧的小组件；确保你已转换为新的小组件。”

这种方法可能适用于小型软件项目，但随着依赖关系图的深度和广度的增加，很快就会失败。团队依赖越来越多的小部件，单个构建中断可能会影响公司不断增长的百分比。以一种可扩展的方式解决这些问题，意味着需要改变我们废弃的方式： 不是将迁移工作推给客户，团队可以将其内部消化，并提供所需资源投入。

传统的开发分支的使用是另一个有内在扩展问题的例子。一个组织可能会发现，将大的功能分支合并到主干中会破坏产品的稳定性，并得出结论：“我们需要对分支的合并时间进行控制，还要降低合并的频率”。这很快会导致每个团队或每个功能都有单独的开发分支。每当任何分支被确定为“完整”时，都会对其进行测试并合并到主干中，从而引发其他仍在开发分支上工作的工程师以重新同步和测试，造成巨大的工作量。这样的分支机构管理模式可以应用在小型组织里，管理5到10个这样的分支机构。随着一个组织的规模（以及分支机构的数量）的增加，我们很快就会发现，为了完成同样的任务，我们付出越来越多的管理成本。随着规模的扩大，我们需要一种不同的方法，我们将在第16章中对此进行讨论。

随着公司的发展，什么样的策略会带来更低的成本？或者，最好是，随着组织化的发展，我们可以制定什么样的策略来提供超高的价值？

我们最喜欢的内部策略之一是为基础架构团队提供强大的支持，维护他们安全地进行基础措施更改的能力。“如果一个产品由于基础架构更改而出现停机或其他问题，但我们的持续集成（CI）系统中的测试没有发现问题，这不是基础架构变更的错。”更通俗地说，这是“如果你喜欢它，你应该对它进行CI测试”，我们称之为“碧昂斯规则。”从可伸缩性的角度来看，碧昂斯规则意味着复杂的、一次性的定制测试（不是由我们的通用CI系统触发的）不算数。如果没有这一点，基础架构团队的工程师需要跟踪每个有任何受影响代码的团队，问他们如何进行测试。当有一百个工程师的时候，我们可以这样做。我们绝对不能这样做。

我们发现，随着组织规模的扩大，专业知识和共享交流论坛提供了巨大的价值。随着工程师在共享论坛中讨论和回答问题，知识往往会传播。新的专家人数不断增加。如果你有100名工程师编写Java，那么一位愿意回答问题的友好且乐于助人的Java专家很快就会产生一个数百名工程师编写更好的Java代码。知识是病毒，专家是载体，扫除工程师常见的绊脚石是非常有价值的。我们将在第3章更详细地介绍这一点。

考虑升级编译器的艰巨任务。从理论上讲，编译器的升级应该是简单的，因为语言需要多少工作才能向后兼容，但是在实际操作中又有多简单呢？如果你以前从未做过这样的升级，你将如何评价你的代码库是否与兼容升级？

根据我们的经验，语言和编译器升级是微妙而困难的任务，即使人们普遍认为它们是向后兼容的。编译器升级几乎总是会导致编译的微小变化：修复错误编译、调整优化，或者潜在地改变任何以前未定义的结果。你将如何针对所有这些潜在的结果来评估你整个代码库的正确性？

谷歌历史上最具传奇色彩的编译器升级发生在2006年。当时，我们已经运行了几年，拥有数千名工程师。我们大约有五年没有升级过编译器。我们的大多数工程师都没有升级编译器的经验。我们的大部分代码只针对在单一编译器版本。对于一个由（大部分）志愿者组成的团队来说，这是一项艰难而痛苦的任务，最终变成了寻找捷径和简化的问题，以便绕过我们不知道如何采用的上游编译器和语言变化。最后，2006年的编译器升级过程非常痛苦。许多海勒姆定律问题，无论大小，都潜入了代码库，加深了我们对特定编译器版本的依赖。打破这些隐式依赖性是痛苦的。相关工程师正在冒风险：我们还没有碧昂斯规则，也没有通用的CI系统，因此很难提前知道更改的影响，或者确保他们不会因回退而受到指责。

这个故事一点也不稀奇。许多公司的工程师都可以讲述一个关于痛苦升级的故事。不同的是，我们在经历了痛苦的任务之后认识到了这一点，并开始关注技术和组织变革，以克服规模问题，并将规模转变为我们的优势：自动化（这样一个人就可以做到更多）、整合/一致性（这样低级别的更改影响有限的问题范围）和专业知识（以便少数人就可以做得更多）。

你更改基础设施的频率越高，更改就越容易。我们发现，在大多数情况下，当代码作为编译器升级的一部分进行更新时，它会变得没那么脆弱，将来更容易升级。大多数代码都经历了几次升级的一个系统中，它的停止取决于底层实现的细微差别。相反，它取决于语言或操作系统所保证的抽象。无论你升级的是什么，代码库的第一次升级都比以后的升级要复杂得多，甚至可以控制其他因素。

通过这些和其他经验，我们发现了许多影响代码库灵活性的因素：

潜在的教训不是关于编译器升级的频率或难度，而是一旦我们意识到编译器升级任务是必要的，我们就找到了方法，确保在代码库增长的情况下，由固定数量的工程师执行这些任务。如果我们认为任务成本太高，应该学会避免，我们可以仍然使用十年前的编译器版本。由于错过了优化机会，我们需要额外支付25%的计算资源。考虑到2006年的编译器对缓解推测性执行漏洞没有效果，我们的中央基础设施可能会面临重大的安全风险，这不是一个明智的选择。

我们看到的一个普遍真理是，在开发人员的工作流程中发现的问题，通常可以降低成本。考虑开发人员工作流程的时间表，从左到右，从概念和设计开始，通过实施、评审、测试、提交、金丝雀和最终的生产部署来进行。在此时间线之前，将问题发现转移到“左侧”会使修问题解决成本更低，如图1-2所示。

这个术语似乎源一种观点，即安全问题不能推迟到开发过程的最后阶段，必须要求“在安全上向左转移”。这种情况下的论点相对简单：如果安全问题是在产品投入生产后才发现的，修复的成本就非常高。如果在部署到生产之前就发现了安全问题，那也需要花费大量的工作来检测和修复问题，但成本更低些。如果你能够在最初的开发之前发现安全问题，将缺陷提交到版本控制就被发现，修复的成本更低：他们已经了解该功能；根据新的安全约束规范进行开发，要比提交代码后再让其他人分类标识并修复它更简单。

同样的基本模式在本书中多次出现。在提交之前通过静态分析和代码审查发现的bug要比投入生产的bug成本更低。在开发过程的早期提供高质量、可靠性和安全性的工具和实践是我们许多基础架构团队的共同目标。没有一个过程或工具是完美的，所以我们可以采取纵深防御的方法，希望尽早抓住图表左侧的缺陷。

如果我们了解如何编程，了解我们所维护的软件的生命周期，并且随着在我们随着更多的工程师一起开发和维护新功能，了解扩大规模时如何运维它，那么剩下的就是做出正确的决策。这是显而易见的：在软件工程中，如同生活一样，好的选择会带来好的结果。然而，这一观点很容易被忽视。在谷歌内部，人们对“因为我这么说了”有反对的意见。重要的是，任何议题都要有一个决策者，当决策是错误的时候，要有明确的改进路径，但目标是共识，而不是一致。看到一些 "我不同意你的衡量标准/评价，但我知道你是如何得出这个结论的 "的情况是没有问题的，也是可以预期的。所有这一切的内在想法是，每件事都需要一个理由；“仅仅因为”、“因为我这么说”或“因为其他人都这样做”是潜在错误的决策。 只要这样做是有效的，在决定两个工程方案的一般成本时，我们应该能够解释清楚。

我们所说的成本是什么呢？我们这里不仅仅是指金钱。“成本”大致可以转化为努力的方向，可以包括以下任何或所有因素：

从历史上看，忽视社会成本的问题尤其容易出现。然而，谷歌和其他大型科技公司现在可以可靠地部署拥有数十亿用户的产品。在许多情况下，这些产品是高净效益的，但当我们以这样的规模运营时，即使在可用性、可访问性和公平性方面或潜在的滥用方面的微小差异也会被放大，往往对边缘化的群体产生不利影响。软件渗透到社会和文化的各个方面；因此，明智的做法是，在做出产品和技术决策时，我们要意识到我们所能带来的好处和坏处。我们将在第4章对此进行更多讨论。

除了上述的成本（或我们对其的估计），还有一些偏差：维持现状偏差（个体在决策时，倾向于不作为、维持当前的或者以前的决策的一种现象。这一定义揭示个体在决策时偏好事件当前的状态，而且不愿意采取行动来改变这一状态，当面对一系列决策选项时，倾向于选择现状选项），损失厌恶偏差（人们面对同样的损失和收益时感到损失对情绪影响更大）等。当我们评估成本时，我们需要牢记之前列出的所有成本：一个组织的健康不仅仅是银行里是否有钱，还包括其成员是否感到有价值和有成就感。在软件等高度创新和利润丰厚的领域在工程设计中，财务成本通常不是限制因素，而人力资源是。保持工程师的快乐、专注和参与所带来的效率提升会成为主导因素，仅仅是因为专注力和生产力变化大，会有10-20%的差异很容易想象。

在许多组织中，白板记号笔被视为贵重物品。它们受到严格的控制，而且总是供不应求。在任何的白板上，都有一半的记号笔是干的，无法使用。你有多少次因为没有一个好用的记号笔而中断会议进程？多少次因为记号笔水用完而打断思考？多少次所有的记号笔都不见了，大概是因为其他团队的记号笔用完了，不得不拿走你的记号笔？所有这些都是因为一个价格不到一美元的产品。

谷歌往往在大多数工作区域都有未上锁的柜子，里面装满了办公用品，包括记号笔。只要稍加注意，就可以很容易地拿到各种颜色的几十支记号笔。在某种程度上，我们做了一个明确的权衡：优化无障碍的头脑风暴远比防止有人拿着一堆记号笔乱跑要重要得多。

我们的目标是对我们所做的每件事都有同样程度的关注和明确的成本/收益权衡，从办公用品和员工津贴到开发者的日常体验，再到如何提供和运行全球规模的服务。我们经常说，“谷歌是一家数据驱动的公司。”事实上，这很简单：即使没有数据，也会有证据、先例和论据。做出好的工程决策就是权衡所有可用的输入，并就权衡做出明智的决策。有时，这些决策是基于本能或公认的最佳实践，但仅是一种假设之后，我们用尽了各种方法来衡量或估计真正的潜在成本。

最后，工程团队的决策应该归结为几件事：

决策不应该是“我们这样做是因为我这么说。”[^17]

当我们权衡数据时，我们发现两种常见情况：

在第一类决策上没有什么理由存在缺陷。任何软件工程的组织都可以并且应该跟进当前的计算资源成本、工程师工作量以及你经常接触的其他成本。即使你不想向你的组织公布确切的金额，你仍然可以制定一份版本表：这么多CPU的成本与这么多RAM或这么多网络带宽。

有了一个协定的转换表，每个工程师都可以自己进行分析。“如果我花两周的时间将这个链表转换成一个更高性能的数据结构，我将多使用5Gb的RAM，但节省两千个CPU。我应该这样做吗？”这个问题不仅取决于RAM和CPU的相对成本，还取决于人员成本（对软件工程师的两周支持）和机会成本（该工程师在两周内还能生产什么？）。

对于第二类决策，没有简单的答案。我们依靠经验、领导和先例来协商这些问题。我们正在投入研究，以帮助我们量化难以量化的问题（见第7章）然而，我们所拥有的最好的广泛建议是，意识到并非所有的事情都是可衡量或可预测的，并尝试以同样的优先权和更大的谨慎对待此类决策。它们往往同样重要，但更难管理。

考虑到你的构建。根据不可靠的推特投票结果显示，大约有60到70%的开发者在本地构建，即使是今天的大型、复杂的构建。才有了这样的笑话，如“编译”漫画所示。你的组织中有多少时间被浪费在等待构建上？将其与为一个小团队运行类似distcc的成本进行比较。或者，为一个大团队运行一个小构建场需要多少成本？这些成本需要多少周/月才能成为一个净收益？

早在2000年代中期，谷歌就完全依赖于本地构建系统：你切出代码，然后在本地编译。在某些情况下，我们有大量的本地机器（你可以在桌面电脑上构建地图！），但随着代码库的增长，编译时间变得越来越长。不出所料，由于时间的浪费，我们的人员成本增加，以及更大、更强大的本地机器的资源成本增加等等。这些资源成本特别麻烦：当然，我们希望人们有一个尽可能快的构建，但大多数时候，一个高性能的桌面开发机器将被闲置。这感觉不像是投资这些资源的正确方式。

最终，谷歌开发了自己的分布式构建系统。开发这个系统当然要付出代价：工程师花费了时间，工程师花更多的时间来改变每个人的习惯和工作流程，学习新系统，当然还需要额外的计算资源。但总体节约显然值得我去做： 构建速度变快了，工程师的时间被节约了，硬件投资可以集中在管理的共享基础设施上（实际上是我们生产机群的一个子集），而不是日益强大的桌面机。第18章详细介绍了我们的分布式构建方法和相关权衡。

因此，我们构建了一个新系统，将其部署到生产环境中，并加快了每个人的构建速度。这就是故事的圆满结局吗？不完全是这样：提供分布式构建系统极大地提高了工程师的工作效率，但随着时间的推移，分布式构建本身变得臃肿起来。在以前的情况下，单个工程师受到的限制（因为他们尽最大可能保持本地构建的速度）在分布式构建系统中是不受限制的。构建图中的臃肿或不必要的依赖关系变得非常普遍。当每个人都直接感受到非最佳构建的痛苦，并被要求去保持警惕时，激励措施会更好地协调一致。通过取消这些激励措施，并将臃肿的依赖关系隐藏在并行的分布式构建中，我们创造了一种情况，在这种情况下，消耗可能猖獗，而且几乎没有人被要求去关注构建的臃肿。这让人想起杰文斯悖论（Jevons Paradox）：一种资源的消耗可能会随着使用效率的提高而增加。

总的来说，与添加分布式构建系统相关的节省成本远远超过了与其构建和维护相关的负成本。但是，正如我们看到的消耗增加，我们并没有以前预见到这些成本。勇往直前之后，我们发现自己处于这样一种境地：我们需要重新认识系统的目标和约束以及我们的使用方式，确定最佳实践（小型依赖项、依赖项的机器管理），并为新生态系统的工具和维护提供资金。即使是相对简单的 "我们花美元购买计算资源以收回工程师时间 "的权衡，也会产生不可预见的下游影响。

很多时候，我们关于时间和规模的主题相互重合，相互影响。符合碧昂斯规则策略具备可扩展性，并帮助我们长期维护事物。对操作系统接口的更改需要许多小的重构来适应，但这些更改中的大多数都可以很好地扩展，因为它们具有相似的形式： 操作系统的变化对每个调用者和每个项目都没有不同的表现。

有时时间和规模会发生冲突，而且没有什么地方能像在基本问题中那么明显：我们应该添加一个依赖性，还是分支/重新实现它，来更好地满足我们的需求？

这个问题可能出现在软件栈（解决方案栈）各个层面，通常情况下，为特定问题定制解决方案优于需要处理所有问题的通用解决方案。通过分支或重新实现程序代码，并为特定问题定制它，你可以更便捷地添加新功能，或更确定地进行优化，无论我们谈论的是微服务、内存缓存、压缩程序还是软件生态系统中的其他内容。更重要的是，你从这样一个分支中获得的控制将你与基础依赖项中的变更隔离开：这些变化并不是由另一个团队或第三方供应商所决定的。你随时决定如何对时间的推移和变化的作出必要地反应。

软件栈（Software Stack），是指为了实现某种完整功能解决方案（例如某款产品或服务）所需的一套软件子系统或组件。

另一方面，如果每个开发人员都将他们的软件项目中使用的组件是多样化，而不是复用现有的组件，那么可扩展性和可持续性都会受到影响。对底层库中的安全问题作出反应不再是更新单个依赖项及其用户的问题：现在要做的是识别该依赖关系的每一个易受攻击的分支以及使用这个分支的用户。

与大多数软件工程决策一样，对于这种情况并没有一个一刀切的答案。如果你的项目生命周期很短，那么fork的风险较小。 如果有问题的分支被证明是范围有限的，那是有帮助的，同时也要避免分支那些可能跨越时间段或项目时间界限的接口（数据结构、序列化格式、网络协议）。一致性有很大的价值，但通用性也有其自身的成本，你往往可以通过做自己的事情来赢得胜利——如果你仔细做的话。

致力于数据驱动文化的一个不明显的好处是承认错误的能力和必要性相结合。在某个时候，将根据现有的数据做出决定——希望是基于准确的数据和仅有的几个假设，但隐含的是基于目前可用的数据。随着新数据的出现，环境的变化，或假设的不成了，可能会发现某个决策是错误的，或在当时是有意义的，但现在已经没有意义了。这对于一个长期存在的组织来说尤其重要：时间不仅会触发技术依赖和软件系统的变化，还会触发用于驱动决策的数据的变化。

我们坚信数据能为决策提供信息，但我们也认识到数据会随着时间的推移而变化，新数据可能会出现。这意味着，本质上，在相关系统的生命周期内，需要不时地重新审视决策。对于长期项目而言，在做出初始决策后，有能力改变方向通常是至关重要的。更重要的是，这意味着决策者需要勇气承认错误。与人的本能相反，勇于承认错误的领导人受更多的尊重。

以证据为导向，但也要意识到无法衡量的东西可能仍然有价值。如果你是一个领导者，那就是你被要求做的：审时度势，主张事在人为。我们将在第5章和第6章中详细介绍领导力

在介绍软件工程和编程之间的区别时，你可能会问，是否存在内在的价值判断。编程是否比软件工程更糟糕？一个由数百人组成的团队预计将持续十年的项目是否比一个只有一个月的项目和两个人构建的项目更有价值？

当然不是。我们的观点并不是说软件工程是优越的，只是它们代表了两个不同的问题领域，具有不同的约束、价值和最佳实践。相反，指出这种差异的价值来自于认识到一些工具在一个领域是伟大的，但在另一个领域不是。对于只持续几天的项目，你不需要依赖集成测试（参见第14章）和持续部署（CD）实践（参见第24章）。同样地，我们对软件工程项目中的版本控制（SemVer）和依赖性管理（参见第21章）的所有长期关注，并不适用于短期编程项目：利用一切可以利用的手段来解决手头的任务。

我们认为，区分相关但不同的术语“编程”和“软件工程”是很重要的。这种差异很大程度上源于随着时间的推移对代码的管理、时间对规模的影响以及面对这些想法的决策。编程是产生代码的直接行为。软件工程是一组策略、实践和工具，这些策略、实践和工具是使代码在需要使用的时间内发挥作用，并允许整个团队的协作。

本书讨论了所有这些主题：一个组织和一个程序员的策略，如何评估和改进你的最佳实践，以及用于可维护软件的工具和技术。谷歌一直在努力打造可持续的代码库和文化。我们不认为我们的方法是做事情的唯一正确方法，但它确实通过例子证明了它是可以做到的。我们希望它将提供一个有用的框架来思考一般问题：你如何维护你的代码，让它正常运行。

