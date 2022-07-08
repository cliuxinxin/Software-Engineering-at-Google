大多数软件工程机构都有管理其代码库的规则——关于源文件的存储位置、代码格式化规则、命名规则和模式以及异常和线程。大多数软件工程师就在这组控制他们如何运作的策略的范围内工作。在谷歌，要管理我们的代码库，我们维护了一套风格指南来定义我们的规则。

规则就像法律。 它们不仅仅是建议或提议，而是严格的、强制性的法律。 因此，规则具有普遍可执行性——不得无视规则除非在需要使用的基础上获得豁免。 与规则相反，指导提供帮助建议和最佳实践。 指导值得遵循，甚至是高度建议能够遵守，但与规则不同的是，指导通常允许出现一些变化的空间。

我们把我们定义的规则，即写代码时必须遵守的 "做 "和 "不做"，收集在我们的编程风格指南中，这些指南被视为典范。 “风格”可能这里有点名不副实，暗示着范围仅限于格式化实践。我们的风格指南不止于此； 它们是一套完整的约定约束我们的代码。 这并不是说我们的风格指南是严格规定的； 是指导还是规则可能需要判断，例如有一条命名规则是“[在合理范围内使用与描述性相同的名称。]” 并且，我们的风格指南是最终的来源我们的工程师必须遵守的规则。

我们为每一门在谷歌使用的编程语言都单独维护一套代码风格指南。在高层次上，所有指南都有相似的目标，旨在引导代码开发并着眼于可持续性。同时，也有很多变化其中包括范围、长度和内容。编程语言有不同的优势，不同的特点，不同的重点，以及在谷歌不断发展的代码库中采用的不同历史路径。因此，独立定制每种语言的指南要实际得多。我们的一部分风格指南注重简洁，专注于一些总体原则，如命名和格式，如在我们的 Dart、R 和 Shell 指南中进行演示的样子。另一部分风格指南注重更多细节方面，深入研究特定的语言特征并扩展内容，特别是我们的 C++、Python 和 Java 指南。还有一部分风格指南重视该语言在谷歌之外的惯例——我们的 Go 风格指南非常简短，只是在一个总结指令中添加了一些规则，以遵循外部公认的惯例。也有部分指南的规则和外部规范根本不同；我们的 C++ 风格指南中不允许使用异常，而使用异常是一种在 Google 代码之外广泛使用语言特性。

即使是我们自己的风格指南也存在很大的差异，这使得我们很难精确地描述一个风格指南应该涵盖什么内容。指导谷歌风格指南开发的决定源于保持我们代码库可持续性的需要。其他组织的代码库天生对可持续性有不同的要求，这就需要一套不同的定制规则。本章讨论了指导我们规则和指南开发的原则和过程，主要从谷歌的c++、Python和Java风格指南中抽取示例。

那么我们为什么需要规则呢?制定规则的目的是鼓励“好的”行为，阻止“坏的”行为。对“好”和“坏”的解释因组织而异，这取决于组织关心的是什么。这样的设计不是普遍的偏好;好与坏是主观的，是根据需要而定的。对于一些组织，“好”可能会促进支持小内存占用或优先考虑潜在运行时优化的使用模式。在其他组织中，“好”可能促进使用新语言特性的选择。有时，组织非常关心一致性，因此与现有模式不一致的任何东西都是“不好的”。我们必须首先认识到一个给定的组织的价值;我们使用规则和指导来鼓励和阻止相应的行为。

随着组织的发展，已建立的规则和指导方针形成了通用的编码词汇表。通用词汇表可以让工程师专注于他们的代码需要表达什么，而不是如何表达。通过塑造这种词汇，工程师会倾向于默认地、甚至是潜意识地去做“好”事情。因此，规则为我们提供了广泛的杠杆作用，以便将共同的开发模式推向所需的方向。

当定义一组规则时，关键问题不是“我们应该有什么规则?”我们要问的问题是:“我们想要实现的目标是什么?”当我们关注规则将服务的目标时，识别哪些规则支持这个目标，可以更容易地提取有用的规则集。在谷歌，风格指南作为编码实践的法规，我们不会问，“风格指南中包含什么?”而是“为什么要把一些东西放进风格指南?”我们的组织通过制定一套规范代码编写的规则获得了什么?

让我们把事情放在背景中:谷歌的工程组织由3万多名工程师组成。工程师群体在技能和背景方面表现出巨大的差异。每天大约有6万份文件提交给超过20亿行代码的代码库，这些代码库可能会存在几十年。我们正在优化一套不同于大多数其他组织所需要的价值，但在某种程度上，这些关注是无处不在的——我们需要维持一个对规模和时间都有扩展的工程环境。

在这种情况下，我们规则的目标是管理开发环境的复杂性，保持代码库的可管理性，同时仍然允许工程师高效地工作。我们在这里做了权衡:帮助我们实现这一目标的大量规则确实意味着我们在限制选择。我们失去了一些灵活性，甚至可能会冒犯某些人，但权威标准所带来的一致性和减少冲突的收益是最重要的。

鉴于这一观点，我们认识到一些指导我们制定规则的首要原则，这些原则必须:

并不是所有的东西都应该放在风格指南中。要求组织中的所有工程师学习和适应任何新规则的成本是有一定代价的。有太多的规则，不仅会让工程师在写代码时更难记住所有相关的规则，而且也会让新工程师更难学会他们的方法。更多的规则也会使维护规则集更具挑战性和更昂贵。

为此，我们有意排除了大家公认的不言而喻的规则。谷歌的风格指南不打算以法律的方式解释;没有明确规定的东西并不意味着它是合法的。例如，c++风格指南没有规定禁止使用goto。c++程序员已经倾向于避免使用它，所以包含禁止使用它的显式规则将引入不必要的开销。如果只有一两个工程师犯了错误，那么通过创建新规则来增加每个人的负担是不利于以后扩展的。

我们规则的另一个原则是为代码的读者而不是作者优化。随着时间的推移，我们的代码被阅读的频率将远远高于编写的频率。我们宁愿代码是繁琐的输入，而不是难以阅读。在我们的Python风格指南中，当讨论条件表达式时，我们认识到它们比if语句短，因此对代码作者来说更方便。然而，由于它们往往比更冗长的if语句更难让读者理解，所以我们限制了它们的使用。我们认为“读起来简单”比“写起来简单”更重要。我们在这里做了一个权衡:当工程师必须重复地为变量和类型输入可能更长的描述性名称时，前期的成本会更高。我们选择支付这笔费用，是因为它为所有未来的读者提供了可读性。

作为优先级的一部分，我们还要求工程师在他们的代码中留下预期行为的明确证明。我们希望读者在阅读代码时能够清楚地理解代码在做什么。例如，我们的Java、JavaScript和C++风格指导在方法重写超类方法时手动使用override注释或关键字。如果没有明确的设计证明，读者很可能会发现这一意图，但是这需要每个阅读代码的读者对代码进行更多的挖掘。

这可能令人惊讶，有意行为的证明变得更加重要。在C++中，仅仅通过阅读一段代码，有时很难追踪指针的所有权。如果一个指针被传递给一个函数，在不熟悉该函数的行为的情况下，我们不能确定将会发生什么。调用者仍然拥有指针吗?这个函数拥有所有权了吗?我可以在函数返回后继续使用指针吗?或者它可能已经被删除了?为了避免这个问题，我们的C++风格指南更倾向于使用std::unique_ptr来实现所有权转移。Unique_ptr是一个管理指针所有权的构造，确保指针只有一个副本存在。当函数接受一个unique_ptr作为参数，并打算获得指针的所有权时，调用者必须显式地调用move语义:

将此与以下内容进行比较:

鉴于风格指南规则，我们保证所有调用方将包括明确的所有权转移证明，无论何时适用。有了这个信号，代码的读者就不需要理解每个函数调用的行为了。我们在API中提供了足够的信息来推断它的交互。这种清晰的调用方行为文档确保了代码片段的可读性和可理解性。我们的目标是局部推理，目标是清楚地了解在调用方发生了什么，而不需要查找和引用其他代码，包括函数的具体实现。

大多数涉及注释的风格指南规则也被设计成支持为读者提供原地证明的目标。文档注释(预先挂在给定文件、类或函数上的块注释)描述了后面代码的设计或意图。实现注释(注释穿插在代码本身中)说明或突出不明显的选择，解释棘手的部分，并强调代码的重要部分。这两种类型注释的指导风格规则在指南中都有涵盖，要求工程师提供其他工程师在阅读代码时可能正在寻找的解释。

我们对代码库一致性的看法类似于我们应用于谷歌办公室的理念。由于工程人员众多，分布广泛，团队经常被分到不同的办公室，而且谷歌员工经常发现自己在其他地方出差。尽管每个办公室都保留了自己独特的个性，融入了当地的风味和风格，但为了完成工作，有一些东西被刻意保持一致。来访的谷歌员工的徽章可以工作在所有当地的徽章阅读器上;任何谷歌设备都可以使用WiFi;任何一间会议室的视频会议设置都将具有相同的界面。谷歌员工不需要花时间去学习如何设置这些;他们知道，无论他们在哪里，这个基本条件都是一样的。在不同的办公室之间转换工作很容易，而且还能完成工作。

这就是我们在源代码中所追求的。一致性是使任何工程师即使进入代码库中不熟悉的部分，也能够相当迅速地开始工作的原因。一个本地项目可以有它独特的个性，但是它的工具是一样的，它的技术是一样的，它的库是一样的，而且都是正常工作的。

尽管不允许办公室定制徽章阅读器或视频会议界面可能会让人觉得受到限制，但一致性带来的好处远远大于我们失去的创作自由。代码也是如此:一致性有时可能会让人感到约束，但这意味着更多的工程师用更少的努力完成更多的工作:  

几年前，我们的C++风格指南承诺，几乎不会改变会使旧代码不一致的风格指南规则:“在某些情况下，改变某些风格规则可能有很好的理由，但我们仍然保持事物的原样，以保持一致性。”

当代码库比较小的时候，老旧的代码比较少的时候，这是有意义的。

当代码库变得更大、更老旧时，这就不再是需要优先考虑的事情了。这是(至少对于我们C++风格指南背后的裁定者来说)一个有意识的改变:当改变这一点时，我们明确地声明C++代码库将不再是完全一致的，我们甚至也不打算这样做。

不仅要将规则更新到当前的最佳实践，而且还要将这些规则应用到已经编写的所有内容，这样的负担太大了。我们的大规模变更工具和过程允许我们更新几乎所有的代码，以遵循几乎每一个新的模式或语法，所以大多数旧的代码都呈现出最新的被认可的风格(见第22章)。然而，这种机制并不完美;当代码库变得足够大时，我们不能保证每一段旧代码都能符合新的最佳实践。对完美一致性的要求需要付出的价代价太大了。

然而，对于一个组织来说，仅仅创建并遵守一套内部惯例是不够的。有时，应考虑到外部环境的惯例。

谷歌的Python风格指南最初要求我们所有的Python代码都采用双空格缩进。外部Python社区使用的标准Python风格指南使用四空格缩进。我们早期的大部分Python开发都是直接支持我们的C++项目，而不是实际的Python应用程序。因此，我们选择使用双空格缩进，以与我们的C++代码保持一致，C++代码已经以这种方式格式化了。随着时间的推移，我们发现这种理论并不成立。编写Python代码的工程师读和写其他Python代码的频率要比读和写c++代码的频率高得多。每次我们的工程师需要查找或引用外部代码片段时，我们都要花费额外的精力。每次我们试图将代码片段输出到开源时，我们都经历了很多痛苦，花了很多时间来调和内部代码和我们想要加入的外部世界之间的差异。

当Starlark(一种基于python的语言，设计于谷歌，作为构建描述语言)有了自己的风格指南时，我们选择使用四间距缩进来与外界保持一致。

如果惯例已经存在，那么一个组织与外界保持一致通常是一个好主意。对于小的，独立的，生命周期短的项目，它可能不会有什么不同;内部一致性比发生在项目有限范围之外的任何事情都重要。一旦时间的推移和潜在的扩展性成为要素，代码与外部项目交互甚至最终与外部世界交互的可能性就会增加。从长远来看，坚持被广泛接受的标准可能会有回报。

我们的风格指南限制了我们使用的语言中一些更令人惊讶、不寻常或棘手的结构的使用。复杂的特征往往有一些乍一看并不明显的细微缺陷。在没有彻底了解其复杂性的情况下使用这些特性，很容易误用它们并引入错误。即使现在的项目的工程师可以很好地理解这个结构，也不能保证未来的项目成员和维护者有同样的理解。

这就是我们Python风格指南中避免使用反射等功能特性的原因。Python反射函数hasattr()和getattr()允许用户使用字符串访问对象的属性:

现在，在这个例子中，一切看起来都很好。但是考虑一下这个:

当搜索代码时，怎么知道这里访问的是字段 foo、bar 和 baz ?没有给读者留下明确的证明。你不容易看到，因此也不容易验证哪些字符串用于访问对象的属性。如果不是从A_CONSTANT读取这些值，而是从远程过程调用(Remote Procedure Call, RPC)请求消息或数据存储读取这些值，那会怎么样呢?这种模糊化的代码可能会导致一个严重的安全漏洞，一个很难被发现的漏洞，只需错误地验证消息就可能发生。测试和验证这样的代码也很困难。

尽管这些高级语言特性可能完美地解决了知道如何利用它们的专家的问题，但强大的特性通常更难理解，而且没有得到广泛的应用。我们需要我们所有的工程师都能够在代码库中操作，而不仅仅是专家。它不仅支持新手软件工程师，而且对SRE来说也要创建一个更好的环境——如果SRE在调试生产中断，他们会跳入任何可疑的代码，甚至包括一些用他们不熟练的语言编写的代码。我们更加重视易于理解和维护的简化、直接的代码。

用拉尔夫·沃尔多·爱默生的话说:“愚蠢的一致性是心胸狭隘的妖怪（为渺小的政治家、哲学家和神学家所崇拜。一个伟大的灵魂与一致性毫无关系）”。在我们追求一致的、简化的代码库时，我们不想盲目地忽略所有其他内容。我们知道风格指南中的一些规则会遇到需要例外的情况，这都是可以的。必要时，我们允许对可能与我们的规则相冲突的优化和和实际问题做出让步。

性能很重要。有时，即使这意味着牺牲一致性或可读性，适应性能优化也是有意义的。例如，尽管我们的C++风格指南禁止使用异常，但它包含了一条允许使用noexcept的规则，noexcept是一个与异常相关的语言说明符，可以触发编译器优化。

互操作性也很重要。为特定的非google部分而设计的代码，如果为其目标量身定做，可能会做得更好。例如，我们的C++风格指南有一个通用CamelCase命名准则的例外，它允许对模仿标准库功能的实体使用标准库的 snake_case 风格。C++风格指南还允许对Windows编程的豁免，在Windows编程中，与平台特性的兼容性需要使用多重继承，这对其他情况的C++代码来说都是明确禁止的。我们的Java和JavaScript风格指南都明确指出，生成的代码中，经常与项目之外的组件交互或依赖于这些组件的代码不在本指南的范围内。一致性是非常重要的；适应更是关键所在。

那么，语言风格指南应该包含哪些内容呢?所有的风格指南规则大致分为三类:

首先，我们的风格指南包括关于语言特性的规则，这些规则出于技术原因必须或不必须做。我们有关于如何使用静态成员和变量的规则；关于使用lambda表达式的规则;异常处理规则；关于构建线程、访问控制和类继承的规则。我们涵盖了要使用的语言特性和要避免的结构。我们列出了可以使用的标准词汇类型以及用途。我们特别包括了关于难以使用和难以正确使用的规则——一些语言特性具有微妙的使用模式，可能不直观或不容易正确应用，会导致一些Bug。对于指南中的每个规则，我们的目标是在描述所达成的决定时，解释权衡过的利弊。这些决定（规则）大多是基于对时适应性的需要，或者支持和鼓励可维护的语言使用。

我们的风格指南还包括一些编写源代码的最佳实践的规则。这些规则有助于保持代码库的健康和可维护性。例如，我们指定代码作者必须在哪里以及如何包含注释。我们的注释规则涵盖了注释的一般惯例，并扩展到包括必须包含代码内文档的特定情况——在这些情况下，意图并不总是明显的，例如switch语句中的失败，空的异常捕获块，以及模板元编程。我们还有详细说明源文件结构的规则，概述了预期内容的组织。我们有关于命名的规则:包、类、函数、变量的命名。所有这些规则都是为了指导工程师采用更健康、更可持续的代码的实践。

我们的风格指南中实施的一些最佳实践旨在使源代码更具可读性。许多格式规则都属于这一类。我们的样式指南指定了何时以及如何使用换行和水平空格，以提高可读性。它们还包括行长度限制和大括号对齐。对于某些语言，我们通过使用自动格式化工具来满足格式化要求——Go 的 gofmt 和 Dart 的 dartfmt。逐项列出格式化需求的详细列表，或者命名必须应用的工具，目标是相同的:我们有一套一致的格式化规则，旨在提高我们所有代码的可读性。

我们的风格指南还包括对新的和尚未被很好理解的语言特性的限制。目的是在学习过程中，在一个功能的潜在缺陷周围预先安装安全围栏。同时，在每个人都应用起来之前，限制使用让我们有机会观察，从我们观察的例子中开发和提取最佳实践的使用模式。对于这些新特性，在开始的时候，我们有时并不确定该如何给予适当的指导。随着采用范围的扩大，希望以不同方式使用新特性的工程师会与风格指南的所有者讨论他们的例子，要求允许超出最初限制范围的额外用例。通过观察收到的豁免请求，我们了解了该特性是如何被使用的，并最终收集了足够多的示例来总结好的实践。在我们得到这些信息之后，我们可以回到限制性规则，并修改它以允许更广泛的使用。

当C++11引入std::unique_ptr是一种智能指针类型，它表达了对动态分配对象的独占所有权，并在unique_ptr超出范围时删除该对象，我们的风格指南最初不允许使用。unique_ptr 的使用方式对于大多数工程师来说是不熟悉的，并且该语言引入的相关的move语义是非常新的，对大多数工程师来说，非常令人困惑。防止在代码库中引入std::unique_ptr似乎是更安全的选择。我们更新了工具来捕获对不允许类型的引用，并保留了现有的指南规则，建议使用其他类型的智能指针。

时间飞逝。工程师有机会适应move语义的语义，我们也越来越相信使用std::unique_ptr直接符合我们的风格指南的目标。在函数调用处上，std::unique_ptr所提供的关于对象所有权的信息使读者更容易理解该代码。引入这种新类型所增加的复杂性，以及随之而来的新的move语义，仍然是一个值得关注的问题，但是代码库长期整体状态的显著改善使得采用std::unique_ptr是一个值得的权衡。

我们的风格指南还包含了一些规则，涵盖了许多较小的内容。对于这些规则，我们主要是为了做出和记录一个决定。这类规则中的许多规则都没有重大的技术影响。诸如命名约定、缩进间距、导入顺序等:通常一种形式相对于另一种形式没有明确的、可衡量的技术优势，这可能是技术社区倾向于对它们争论不休的原因。选择一个，我们就退出了无休止的辩论循环，可以继续前进了。我们的工程师不再花时间去讨论两个空格与四个空格。这类规则的重要部分不是我们为给定的规则选择的内容，而是我们选择的这个动作本身。

除此之外，还有很多内容不在我们的风格指南中。我们试着专注于那些对我们代码库的健康状况有重大影响的事情。这些文档中绝对有一些没有详细说明的最佳实践，包括许多很好的工程建议:不要自做聪明，不要分支代码库，不要重新发明轮子，等等。像我们的风格指南这样的文档不能让一个完全的新手拥有软件工程大师的理解——有些事情是我们假设的，这是故意的。

我们的风格指南不是一成不变的。与大多数事情一样，随着时间的推移，做出风格指导决策的环境和指导给定裁决的因素可能会发生变化。有时，情况的变化足以使人们需要重新评估。如果发布了新的语言版本，我们可能需要更新规则以允许或排除新的特性和习惯用法。如果某个规则是工程师需要付出努力来规避它的，我们可能需要重新审视该规则本来应该提供的好处。如果我们用来执行规则的工具变得过于复杂和难于维护，那么规则本身可能已经腐化，需要重新修订。注意到一条规则何时准备好，以便再次审视，这是过程中的一个重要部分，它使我们的规则集保持相关性和时效性。

在我们的风格指南中，规则背后的决定是有证据支持的。在添加规则时，我们将花时间讨论和分析相关的利弊以及潜在的后果，并试图验证给定的更改是否适合谷歌运营规模。谷歌风格指南中的大多数条目都包含了这些考虑因素，列出了在过程中权衡的利弊，并给出了最终裁决的理由。理想情况下，我们优先考虑这种详细的推理，并将其包含在每条规则中。

记录给定决策背后的原因，使我们能够识别何时需要更改。考虑到时间的流逝和环境的变化，以前做出的好决定可能不是现在的最佳决定。明确地指出影响因素后，我们就能够确定何时与一个或多个因素相关的更改需要重新评估规则。

在谷歌，当我们为Python代码定义初始风格指导时，我们选择使用驼峰命名风格，而不是使用snake_case命名风格来命名方法名。尽管公共Python风格指南(PEP 8)和大多数Python社区使用了snake_case命名，但当时谷歌的大多数Python用法是为c++开发人员使用Python作为c++代码库之上的脚本层。许多定义的Python类型是相应c++类型的包装器，因为Goo‐gle的C++命名惯例遵循驼峰命名风格，在这里跨语言的一致性被视为关键。

后来，我们到了构建和支持独立 Python 应用程序的地步。最经常使用Python的工程师是那些开发Python项目的Python工程师，而不是编写快速脚本的C++工程师。我们给我们的Python工程师造成了一定程度的尴尬和可读性问题，要求他们为我们的内部代码维护一个标准，但每次他们引用外部代码时都要不断地调整另一个标准。我们还让有Python经验的新员工更难以适应我们的代码库规范。

随着Python 项目的增长，我们的代码与外部Python项目的交互越来越频繁。我们在一些项目中合并了第三方Python库，导致我们的代码库中混合了我们自己的驼峰命名格式和外部偏爱的snake_case样式。当我们开始开源我们的一些Python项目时，在一个我们的没有约定规范限制的外部世界中维护它们，既增加了我们的复杂性，也增加了社区对我们风格的警惕，他们觉得我们的风格令人惊讶，有些怪异。

提出这些论点后，讨论了成本(失去与其他谷歌代码的一致性，对习惯于我们的 Python 风格的 Google 员工进行再教育)和好处(获得与大多数其他Python代码的一致性，允许已经泄露到第三方库的内容)，Python风格指南的风格仲裁者决定改变规则。有了它被应用为文件范围的选择的限制，现有代码的例外，以及项目决定什么最适合他们的自由度，谷歌Python风格指南已更新为允许 snake_case 命名。

考虑到我们所追求的长生命周期和扩展能力，我们认识到事情需要改变，因此我们创建了一个更新规则的过程。改变我们的风格指南的过程是基于解决方案的。风格指南更新的建议是以此观点为框架的，识别现有的问题，并将建议的更改作为修复问题的一种方法。在这个过程中，“问题”并不是可能出错的假设例子;问题是通过在现有谷歌代码中发现的模式来证明的。给定一个被证明的问题，因为我们有现有风格指南决策背后的详细理由，我们可以重新评估，检查和之前不同的结论现在是否更有意义。

编写风格指南所规范的代码的工程师群体，往往最能注意到何时需要改变规则。事实上，在谷歌，我们的风格指南的大多数改变都是从社区讨论开始的。任何工程师都可以提出问题或提出更改建议，通常从专门用于讨论风格指南的特定语言邮件列表开始。

关于风格指南更改的建议可能是完整的，包括建议的具体的、更新的建议，或者可能以关于给定规则的适用性的模糊问题开始。社区会讨论新想法，并接收其他语言用户的反馈。一些提案被社区共识拒绝，被认为是不必要的、过于模糊的或无益的。另一些则收到了积极的反馈，被认为是有价值的，或者有一些建议的改进。这些提案，经过社区审查后，需要得到最终决策的批准。

在谷歌，对于每种语言的风格指南，最终的决定和批准都是由风格指南的所有者——我们的风格仲裁者——做出的。对于每一种编程语言，都有一群资深的语言专家是风格指南的所有者和指定的决策者。特定语言的风格仲裁人通常是该语言库团队的高级成员，以及其他具有相关语言经验的长期谷歌员工。

对于任何风格指南的更改，实际的决策都是对提议修改的工程权衡的反复讨论。仲裁者在风格指南优化的一致目标上下文中做出决定。更改并非根据个人喜好。它们是权衡判断。事实上，C++风格仲裁组目前由四个成员组成。这可能看起来很奇怪:如果委员会成员人数为奇数，就可以防止出现出现意见分歧的情况，出现票数平手的情况。然而，由于决策制定方法的本质，没有什么是“因为我认为它应该是这样的”，一切都是一种权衡，决策是通过共识而不是投票做出的。这个由四名成员组成的小组就这样愉快地运作着。

没错，我们的规则就是法律，但也有例外。我们的规则通常是为更大的一般情况而设计的。有时，特定的情况会受益于对特定规则的豁免。当出现这种情况时，会咨询风格仲裁者，以确定是否存在授予某个特定规则豁免的有效案例。

豁免不是轻易就能获得的。在C++代码中，如果引入了宏API，风格指南要求使用特定于项目的前缀来命名它。由于C++处理宏的方式，将它们视为全局命名空间的成员，所有从头文件导出的宏必须具有全局唯一的名称，以防止冲突。关于宏命名的风格指南规则确实允许对一些真正全局的实用宏进行仲裁授予的豁免。但是，当请求排除项目特定前缀的豁免请求背后的原因归结为由于宏名称长度或项目一致性而导致的偏好时，豁免被拒绝。这里代码库的完整性胜过项目的一致性。

在被认为允许违反规则比避免违反规则更有利的情况下，允许例外。C++ 风格指南不允许隐式类型转换，包括单参数构造函数。然而，对于那些被设计成透明地包装其他类型的类型，其中的底层数据仍然被精确地表示出来，允许隐式转换是完全合理的。在这种情况下，授予对无隐式转换规则的豁免。拥有如此明确的有效豁免案例可能表明需要澄清或修改相关规则。然而,对于此特定规则，收到了足够多的豁免请求，这些请求似乎适合豁免的有效案例，但实际上不符合。因为所讨论的特定类型实际上不是透明包装器类型，或者因为该类型是包装器但实际上并不需要——保持原样的规则仍然是值得的。

除了规则之外，我们还以各种形式提供编程指导，从对复杂主题的长而深入的讨论到对我们认可的最佳实践的简短而有针对性的建议。

指导代表了我们收集的工程经验的智慧，记录了我们从一路走来的经验教训中提取的最佳实践。指导往往侧重于我们观察到人们经常出错的事情或不熟悉并因此容易混淆的新事物。如果规则是“必须”，那么我们的指导就是“应该”。

我们培养的指导库的一个例子是我们使用的一些主要语言的编写入门指南。虽然我们的风格指南是规范性的，规定了哪些语言特性是允许的，哪些是不允许的，而入门指南是描述性的，解释了指南认可的特性。他们的内容相当广泛，几乎涵盖了谷歌新使用该语言的工程师需要参考的所有主题。它们不会深入研究特定主题的每一个细节，但它们提供解释和推荐使用。当工程师需要弄清楚如何应用他们想要使用的功能时，入门指南的目的是作为指导参考。

几年前，我们开始发布一系列C++提示，其中包括通用语言建议和谷歌特有的提示。我们涵盖了困难的事情——对象生命期、复制和移动语义、依赖于参数的查找;新事物- C++11特性，它们在代码库中被采用，预采用的 C++17 类型，如string_view, optional, 和 variant;还有一些东西需要温和的调整——提醒不要使用using指令，警告要记住寻找隐式布尔转换。这些技巧来源于所遇到的实际问题，解决了样式指南中没有涉及的实际编程问题。与风格指南中的规则不同，它们的建议并不是真正的经典;它们仍然属于建议而非规则的范畴。然而，考虑到它们是从观察到的模式而不是抽象的理想中发展出来的，它们广泛而直接的适用性使它们有别于大多数其他建议，成为一种“共同的经典”。这些小贴士的内容都比较狭隘，篇幅也相对较短，每一条都不超过几分钟的阅读时间。这个“每周技巧”系列在内部已经非常成功，在代码评审和技术讨论中经常被引用。

软件工程师进入一个新的项目或代码库时，已经掌握了他们将要使用的编程语言的知识，但缺乏关于如何在 Google 中使用该编程语言的知识。为了弥补这一差距，我们为使用中的每一种主要编程语言设置了一系列的“<语言>@Google 101”课程。这些全日制课程侧重于在我们的代码库中使用该语言进行开发的不同之处。它们涵盖了最常用的库和习惯用法、内部首选项和自定义工具的使用。对于一个刚刚成为谷歌C++工程师的C++工程师，该课程填补了缺失的部分，使他们不仅是一名优秀的工程师，而且是一名优秀的 Google 代码库工程师。

除了教授旨在让完全不熟悉我们的人快速运行的课程外，我们还为深入代码库的工程师培养现成的参考资料，以便在学习途中找到可以帮助他们的信息。这些参考资料的形式各不相同，并且跨越了我们使用的语言。我们内部维护的一些有用的参考资料包括：  

就其本质而言，规则在可执行的情况下会带来更大的价值。规则可以通过教学和培训在社会上执行，也可以在技术上通过工具来执行。我们在谷歌提供了各种正式的培训课程，涵盖了我们的规则所要求的许多最佳实践。我们还投入资源使我们的文档保持更新，以确保参考材料保持准确和最新。当涉及到对规则的了解和理解时，我们整体培训方法的一个关键部分是代码评审所扮演的角色。我们在谷歌运行的可读性过程——在这里，针对特定语言的谷歌开发环境的新工程师会通过代码审查来指导——在很大程度上是为了培养我们的风格指南所要求的习惯和模式(详见第3章可读性过程) 。这个过程是我们如何确保这些实践是跨项目边界学习和应用的重要部分。

尽管一定程度的培训总是必要的——毕竟，工程师必须学习规则，这样他们才能编写遵循规则的代码——但在检查合规性时，我们强烈倾向于不完全依赖基于工程师的验证使用工具自动执行。

自动执行规则可确保随着时间的推移或组织规模的扩大，规则不会被丢弃或遗忘。新的人加入；他们可能还不知道所有的规则。规则会随着时间而改变;即使有良好的沟通，也不是每个人都能记住所有事情的当前状态。项目不断发展并添加新功能;以前不相关的规则突然适用了。检查规则合规性的工程师取决于记忆或文档，这两者都可能失败。只要我们的工具保持更新，与我们的规则更改同步，我们就知道我们的规则被我们所有的工程师应用于我们所有的项目。

自动执行的另一个优点是最大限度地减少了规则解释和应用的差异性。当我们编写脚本或使用工具检查合规性时，我们会根据一个单一的、不变的规则定义来验证所有输入。我们不会将解释留给每个单独的工程师。人类工程师以带有偏见的视角看待一切。无论是否是无意识的，潜在的微妙的，甚至可能是无害的，偏见仍然会改变人们看待事物的方式。如果让工程师来执行这些规则，可能会导致对规则的解释和应用不一致，对责任的期望也可能不一致。我们授权给工具的越多，留给人类偏见进入的入口就越少。

工具还使执行具有可扩展性。随着组织的成长，一个专家团队就可以编写公司其他部门都可以使用的工具。如果公司的规模扩大一倍，在整个组织内执行所有规则的成本不会增加一倍，它与以前差不多。

即使具有我们通过合并工具获得的优势，也可能无法自动执行所有规则。一些技术规则明确要求人的判断。例如，在C++风格指南中:“避免复杂的模板元编程。”“使用auto来避免冗长、明显或不重要的类型名，这些类型名不能帮助读者清晰地读懂。“组合通常比继承更合适。”在Java风格指南中:“对于如何[对类的成员和初始化程序进行排序]并没有一个正确的方法;不同的类可能以不同的方式排列内容。”“对所捕获的异常不做任何响应很少是正确的。"覆盖 Object.finalize 的情况极为罕见。"对于所有这些规则，都需要人工判断，工具目前还不能代替判断。

其他规则是社会性的而不是技术性的，用技术性的解决方案来解决社会性问题通常是不明智的。对于这个类别下的许多规则，细节往往不太明确，工具将变得复杂且昂贵。将这些规则的执行留给人类通常会更好。例如，当涉及到给定代码更改的大小（即受影响的文件数和修改的行数）时，我们建议工程师倾向于较小的更改。对于工程师来说，小的变更更容易审核，所以审核往往更快、更可靠。它们也不太可能引入bug，因为更容易推断出较小更改的潜在影响和效果。然而，“小”的定义有些模糊。一个在数百个文件中传播相同的单行更新的变化实际上可能很容易审查。相比之下，一个较小的20行修改可能会引入复杂的逻辑，并产生难以评估的副作用。我们认识到有许多不同的衡量尺度，其中一些可能是主观的——特别是当考虑到变化的复杂性时。这就是为什么我们没有任何工具来自动拒绝超过任意行限制的建议更改。如果审阅者认为更改过大，他们可以(而且确实会)推回。对于这种规则和类似的规则，执行由编写和审查代码的工程师自行决定。然而，当涉及到技术规则时，只要是可行的，我们倾向于技术执行。

许多涉及语言使用的规则可以通过静态分析工具强制执行。事实上，我们的一些C++类库管理员在2018年年中对C++风格指南进行的一项非正式调查估计，其中大约90%的规则可以自动验证。错误检查工具采用一组规则或模式，并验证给定的代码示例是否完全符合。自动验证消除了代码作者记住所有适用规则的负担。如果工程师只需要查找违规警告——其中许多都带有建议的修复——在代码审查期间由已紧密集成到开发工作流中的分析器发现的，我们将尽可能减少遵守规则所需要的工作量。当我们开始使用工具基于源标签来标记已弃用的函数时，警告和建议的就都会同时给出，新使用已弃用 API 的问题几乎在一夜之间消失了。降低合规成本使工程师更有可能愉快地贯彻执行。

我们使用像clang-tidy(用于C++)和Error Prone(用于Java)这样的工具来自动化执行规则的过程。有关我们方法的深入讨论，请参见第 20 章。

我们使用的工具都是为支持我们定义的规则而设计和定制的。大多数支持规则的工具都是绝对的;每个人都必须遵守规则，所以每个人都使用检查规则的工具。有时，当工具支持最佳实践时，在遵守约定方面有更多的灵活性，就会有选择退出机制，允许项目根据自己的需要进行调整。

在谷歌，我们通常使用自动样式检查器和格式化器来在我们的代码中执行一致的格式。行长度的问题已经不再有趣了。工程师只需运行样式检查器并继续前进。如果每次都以相同的方式进行格式化，那么在代码审查期间就不会出现问题，从而消除了用来查找、标记和修复要样式细节的审查周期。

在管理有史以来最大的代码库时，我们有机会观察人工格式化和自动化工具格式化的结果。平均而言，机器人比人类好很多。在某些地方，领域专业知识很重要——例如，格式化矩阵，人工通常可以比通用格式化程序做得更好。如果做不到这一点，用自动样式检查器格式化代码很少出错。

我们通过预提交检查强制使用这些格式化程序:在提交代码之前，服务会检查在代码上运行格式化器是否会产生任何差异。如果是，提交将被拒绝，并提供有关如何运行格式化程序以修复代码的说明。谷歌上的大多数代码都要接受这种预提交检查。在我们的代码中，C++使用了clang-format;Python使用yapf内部包装器;Go使用gofmt; Dart 使用 dartfmt;以及我们的 BUILD 文件使用buildifier。

萨米尔·阿吉马尼

谷歌于2009年11月10日以开源方式发布了Go编程语言。从那时起，Go已经发展成为一种开发服务、工具、云基础设施和开源软件的语言。

我们从一开始就知道我们需要一个Go代码的标准格式。我们也知道，在开源版本发布后，几乎不可能改造标准格式。因此，最初的Go版本包括gofmt，这是Go的标准格式工具。

代码审查是一种软件工程的最佳实践，但是太多的时间被花在评审中争论格式。虽然标准格式不是每个人都喜欢的，但它足以消除这些浪费的时间。

通过标准化格式，我们为可以自动更新 Go 代码而不会创建虚假差异的工具奠定了基础：机器编辑的代码与人工编辑的代码无法区分。

例如，在2012年Go 1.0的前几个月，Go 团队使用了一个名为 gofix 的工具来自动将 1.0 之前的 Go 代码更新到语言和库的稳定版本。多亏了gofmt, gofix 产生的差异只包括重要的部分：语言和 API 使用的更改。 这使程序员可以更轻松地查看更改并从工具所做的更改中学习。

成千上万的开源包读取和编写 Go 代码。因为所有的编辑器和IDE都同意Go格式，所以Go工具是可移植的，并且很容易通过命令行集成到新的开发环境和工作流中。

在2012年，我们决定使用一个新的标准格式器来自动格式化谷歌中的所有BUILD文件:buildifier。BUILD文件包含了使用Blaze(谷歌的构建系统)构建谷歌软件的规则。标准的BUILD格式将使我们能够创建自动编辑BUILD文件而不破坏其格式的工具，就像Go工具对Go文件所做的那样。

一位工程师花了六周时间重新格式化了谷歌的 200,000 个 BUILD 文件，这些文件被各个代码所有者接受，在此期间每周都会添加一千多个新的 BUILD 文件。谷歌为进行大规模变革而建立的基础设施大大加快了这一努力。（见第 22 章）。

对于任何组织，尤其是像 Google 的工程师团队这样大的组织，规则帮助我们管理复杂性并建立一个可维护的代码库。一组共享的规则框定了工程流程，以便它们可以扩大规模并保持增长，从而保持代码库和组织的长期可持续性。
