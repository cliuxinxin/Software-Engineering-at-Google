上一章介绍了谷歌对测试进行分类的两个主要轴线：*大小*和*范围*。简而言之，大小是指测试所消耗的资源和允许做的事情，范围是指测试要验证多少代码。虽然谷歌对测试规模有明确的定义，但范围往往是比较模糊的。我们使用术语*单元测试*指的是范围相对较窄的测试，如单个类或方法的测试。单元测试通常是小规模的，但这并不总是这样的。

在实现防止bug之后，测试最重要的目的是提高工程师的生产效率。与范围更广的测试相比，单元测试有许多特性，使其成为优化生产效率的绝佳方式:

由于单元测试有很多优点，在谷歌写的大多数测试都是单元测试，作为经验法则，我们鼓励工程师把80%的单元测试和20%的范围更广的测试混合起来。这个建议，再加上编写单元测试的简易性和运行速度，意味着工程师要运行*多个*单元测试--一个工程师在平均工作日中执行数千个单元测试（直接或间接）是很正常的。

因为测试在工程师的生活中占了很大一部分，所以谷歌非常重视*测试*的可维护性。可维护的测试是那些 "正常工作 "的测试：在写完测试后，工程师不需要再考虑它们，直到它们失败，而这些失败表明有明确原因的真正错误。本章的主要内容是探讨可维护性的概念和实现它的技术。

想象一下这个场景：Mary希望向产品添加一个简单的新功能，并且能够快速实现它，可能只需要几十行代码。但是，当她去检查她的改动，她从自动测试系统那里得到了满屏的错误。她花了一天的时间来逐一检查这些错误。在每种情况下，更改都没有引入实际的bug，但打破了测试对代码内部结构的一些设定，需要更新这些测试。通常情况下，她很难弄清楚这些测试一开始要做什么，而她为修复它们而添加的黑操作使得这些测试在以后更难理解。最终，本来应该是一份快速的工作，结果却要花上几个小时甚至几天的时间忙碌，扼杀了Mary的工作效率，消磨了她的士气。

在这里，测试产生了与预期相反的效果，它消耗了生产力，而不是提高生产效率，同时没有显著提高被测试代码的质量。这种情况太普遍了，谷歌工程师每天都在与之斗争。没有什么灵丹妙药，但谷歌的许多工程师一直在努力开发一套模式和实践来缓解这些问题，我们鼓励公司的其他人效仿。

正如刚才所定义的，脆性测试是指在面对不相关的程序代码变化时失败的测试，这些变化不会引入任何真正的错误。在只有几个工程师的小型代码库中，每次修改都要调整一些测试，这可能不是一个大问题。但是，如果一个团队经常写脆弱测试，测试维护将不可避免地消耗团队越来越多的时间，因为他们不得不在不断增长的测试套件中梳理越来越多的失败。如果一套测试需要工程师为每一个变化进行手动调整，称其为 "自动化测试套件"就有点牵强了！

脆弱测试在任何规模的代码库中都会造成痛苦，但在谷歌的规模中，它们变得尤为严重。一个单独的工程师在工作过程中，可能在一天内就会轻易地运行数千个测试，而一个大规模的变化（见第22章）可能会引发数十万个测试。在这种规模下，即使是影响一小部分测试的误报故障也会浪费大量的工程时间。谷歌的团队在测试套件的脆弱性方面存在很大差异，但我们已经确定了一些实践和模式，这些实践和模式倾向于使测试变得更健壮，更易于更改。

在讨论避免脆性测试的模式之前，我们需要回答一个问题：编写测试后，我们应该多久更改一次测试？任何花在更新旧测试上的时间都不能花在更有价值的工作上。因此，*理想的测试是不变的：*在编写之后，它永远不需要更改，除非被测系统的需求发生变化。

这在实践中是什么样子的呢？我们需要考虑工程师对生产代码所做的各种修改，以及我们应该如何期望测试对这些修改做出反应。从根本上说，有四种更改：

启示是，在编写测试之后，在重构系统、修复bug或添加新功能时，不需要再次接触该测试。这种理解使大规模使用系统成为可能：扩展系统只需要写少量的与你所做的改变有关的新测试，而不是可能要触动所有针对该系统写过的测试。只有对系统行为的破坏性更改才需要返回以更改其测试，在这种情况下，更新这些测试的成本相对于更新所有系统用户的成本往往很小。

现在我们了解了我们的目标，让我们看看一些做法，以确保测试不需要改变，除非被测试系统的需求改变。到目前为止，确保这一点的最重要的方法是编写测试，以与用户相同的方式调用正在测试的系统；也就是说，针对其公共API[而不是其实现细节](https://oreil.ly/ijat0)进行调用。如果测试的工作方式与系统的用户相同，根据定义，破坏测试的变化也可能破坏用户。作为一个额外的好处，这样的测试可以作为用户的有用的例子和文档。

考虑例12-1，它验证了一个事务并将其保存到数据库中。

测试这段代码的一个诱人的方法是去掉 "私有 "可见修饰符，直接测试实现逻辑，如例12-2所示。

此测试与事务处理器的交互方式与其实际用户的交互方式大不相同：它窥视系统的内部状态并调用系统API中未公开的方法。因此，测试是脆弱的，几乎任何对被测系统的重构（例如重命名其方法、将其分解为辅助类或更改序列化格式）都会导致测试中断，即使此类更改对类的实际用户是不可见的。

相反，同样的测试覆盖率可以通过只测试类的公共 API 来实现，如例 12-3.2 所示。

根据定义，仅使用公共API的测试是以与用户相同的方式访问被测系统。这样的测试更现实，也不那么脆弱，因为它们形成了明确的契约：如果这样的测试失败，它意味着系统的现有用户也将失败。只测试这些契约意味着你可以自由地对系统进行任何内部重构，而不必担心对测试进行繁琐的更改。

什么是 "公共API "并不总是很清楚，这个问题实际上涉及到单元测试中的 "单元 "的核心。单元可以小到一个单独的函数，也可以大到由几个相关的包/模块组成的集合。当我们在这里说 "公共API "时，我们实际上是在谈论该单元暴露给拥有该代码的团队之外的第三方的API。这并不总是与某些编程语言提供的可见性概念一致；例如，Java中的类可能将自己定义为 "公共"，以便被同一单元中的其他包所访问，但并不打算供该单元之外的其他方使用。有些语言，如Python，没有内置的可见性概念（通常依靠惯例，如在私有方法名称前加上下划线），而像Bazel这样的构建系统可以进一步限制谁可以依赖编程语言所声明的公共API。

为一个单元定义一个合适的范围，因此应该将其视为公共API，这与其说是科学，不如说是艺术，但这里有一些经验法则：

在谷歌，我们发现工程师有时需要被说服，通过公共API进行测试比针对实现细节进行测试要好。这种不情愿的态度是可以理解的，因为写测试的重点往往是你刚刚写的那段代码，而不是弄清楚这段代码是如何影响整个系统的。然而，我们发现鼓励这种做法是很有价值的，因为额外的前期努力在减少维护负担方面得到了许多倍的回报。针对公共API的测试并不能完全防止脆性，但这是你能做的最重要的事情，以确保你的测试只在系统发生有意义的变化时才失败。

测试通常依赖于实现细节的另一种方法不是测试调用系统的哪些方法，而是如何验证这些调用的结果。通常，有两种方法可以验证被测系统是否按预期运行。通过*状态测试*，你观察系统本身，看它在调用后是什么样子。通过*交互测试*，你要检查系统是否对其合作者采取了预期的行动序列[以响应调用它](https://oreil.ly/3S8AL)。许多测试将执行状态和交互验证的组合。

交互测试往往比状态测试更脆弱，原因与测试一个私有方法比测试一个公共方法更脆的原因相同：交互测试检查系统是*如何*得到结果的，而通常你只应该关心结果是*什么*。例12-4展示了一个测试，它使用一个测试替换（在第13章中进一步解释）来验证一个系统如何与数据库交互。

该测试验证了对数据库API的特定调用，但有两种不同的方法可能会出错：

如例12-5所示，直接对系统的状态进行测试就不那么脆弱了。

这种测试更准确地表达了我们所关心的：被测系统与之交互后的状态。

交互测试出现问题的最常见原因是过度依赖嘲弄框架。这些框架可以很容易地创建测试替换，记录并验证针对它们的每个调用，并在测试中使用这些替换来代替真实对象。这种策略直接导致了脆弱的交互测试，因此我们倾向于使用真实对象而不是模拟对象，只要真实对象是快速和确定的。

总有一天，即使我们已经完全避免了脆弱性，我们的测试也会失败。失败是一件好事--测试失败为工程师提供了有用的信号，也是单元测试提供价值的主要方式之一。

测试失败有两个原因之一：

当测试失败时，工程师的首要工作是确定失败属于哪种情况，然后诊断出实际问题。工程师定位问题的速度取决于测试的清晰程度。清晰的测试是指工程师在诊断故障时，立即明确其存在目的和故障原因的测试。如果测试失败的原因不明显，或者很难弄清楚最初写这些测试的原因，那么测试就无法达到清晰的效果。清晰的测试还能带来其他的好处，比如记录被测系统，更容易作为新测试的基础。

随着时间的推移，测试的清晰度变得非常重要。测试往往比编写测试的工程师的时间更长，而且随着时间的推移，对系统的要求和理解会发生微妙的变化。一个失败的测试完全有可能是多年前由一个已经不在团队中的工程师写的，没有办法弄清楚其目的或如何修复它。这与不明确的生产代码形成了鲜明的对比，你通常可以通过查看调用代码的内容和删除代码后的故障来确定其目的。对于一个不明确的测试，你可能永远不会明白它的目的，因为删除该测试除了（潜在地）在测试覆盖率中引入一个细微的漏洞之外没有任何影响。

在最坏的情况下，这些晦涩难懂的测试最终会被删除，因为工程师不知道如何修复它们。删除这些测试不仅会在测试覆盖率上带来漏洞，而且还表明该测试在其存在的整个期间（可能是多年）一直提供零价值。

为了使测试套件能够随时间扩展并变得有用，套件中的每个测试都尽可能清晰是很重要的。本节探讨了为实现清晰性而考虑测试的技术和方法。

帮助测试实现清晰的两个高级属性是完整性和简洁性。一个测试是完整的，当它的主体包含读者需要的所有信息，以了解它是如何得出结果的。当一个测试不包含其他分散注意力的或不相关的信息时，它就是简洁的。例12-6显示了一个既不完整也不简洁的测试：

测试将大量不相关的信息传递给构造函数，测试的实际重要部分隐藏在辅助方法中。通过澄清辅助方法的输入，可以使测试更加完整，通过使用另一个辅助隐藏构建计算器的无关细节，可以使测试更加简洁，如示例12-7所示。

我们稍后讨论的观点，特别是围绕代码共享，将与完整性和简洁性挂钩。需要注意的是，如果能使测试更清晰，违反DRY（不要重复自己）原则通常是值得的。记住：一个测试的主体应该包含理解它所需要的所有信息，而不包含任何无关或分散的信息。

许多工程师的第一直觉是试图将他们的测试结构与他们的代码结构相匹配，这样每个产品方法都有一个相应的测试方法。这种模式一开始很方便，但随着时间的推移，它会导致问题：随着被测试的方法越来越复杂，它的测试也越来越复杂，变得越来越难以理解。例如，考虑例12-8中的代码片段，它显示了一个事务的结果。

如例12-9所示，一个测试涵盖了该方法可能显示的两个信息，这并不罕见。

对于这样的测试，很可能一开始测试只包括第一个方法。后来，当第二条信息被添加进来时，工程师扩展了测试（违反了我们前面讨论的稳定的测试理念）。这种修改开创了一个不好的先例：随着被测方法变得越来越复杂，实现的功能越来越多，其单元测试也会变得越来越复杂，越来越难以使用。

问题是，围绕方法测试框架自然会鼓励不清晰测试，因为单个方法经常在背后下做一些不同的事情，可能有几个棘手的边缘和角落的情况。有一个更好的方法：与其为每个方法写一个测试，不如为每个行为写一个测试。 行为是一个系统对它在特定状态下如何响应一系列输入的任何保证。"鉴于一个银行账户是空的，当试图从该账户中取钱时，该交易被拒绝。" 方法和行为之间的映射是多对多的：大多数不重要的方法实现了多个行为，一些行为依赖于多个方法的交互。前面的例子可以用行为驱动的测试来重写，如例12-10所介绍。

拆分单个测试所需的额外模板文件非常值得，并且最终的测试比原来测试更清晰。行为驱动测试往往比面向方法的测试更清晰，原因有几个。首先，它们阅读起来更像自然语言，让人们自然地理解它们，而不需要语言繁琐的心理分析。其次，它们更清楚地表达了因果关系，因为每个测试的范围都更有限。最后，每个测试都很短且描述性强，这一事实使我们更容易看到已经测试了哪些功能，并鼓励工程师添加新的简洁测试方法，而不是堆积在现有方法上。

将测试视为与行为而非方法相耦合会显著影响测试的结构。请记住，每个行为都有三个部分：一个是定义系统如何设置的 "given "组件，一个是定义对系统采取的行动的 "when "组件，以及一个验证结果的 "then "组件。当此结构是显式的时，测试是最清晰的。一些框架（如Cucumber和Spock）直接加入了given/when/then的功能支持。其他语言可以使用空格和可选注释使结构突出，如示例12-11所示。

这种程度的描述在琐碎的测试中并不总是必要的，通常省略注释并依靠空白来使各部分清晰。然而，明确的注释可以使更复杂的测试更容易理解。这种模式使我们有可能在三个层次的粒度上阅读测试。

最常见的违反模式是在对被测系统的多个调用之间穿插断言（即，组合“when”和“then”块）。以这种方式合并 "then "和 "when "块会使测试不那么清晰，因为它使人们难以区分正在执行的操作和预期结果。

当一个测试确实想验证一个多步骤过程中的每个步骤时，定义when/then块的交替序列是可以接受的。长的区块也可以用 "and"字来分割，使其更具描述性。例12-12显示了一个相对复杂的、行为驱动的测试是什么样子的。

在编写这种测试时，要注意确保你不会无意中同时测试多个行为。每个测试应该只覆盖一个行为，绝大多数的单元测试只需要一个 "when "和一个 "then "块。

面向方法的测试通常以被测试的方法命名（例如，对 updateBalance 方法的测试通常称为 testUpdateBalance）。对于更加集中的行为驱动的测试，我们有更多的灵活性，并有机会在测试的名称中传达有用的信息。测试名称非常重要：它通常是失败报告中第一个或唯一一个可见的标记，所以当测试中断时，它是你沟通问题的最好机会。它也是表达测试意图的最直接的方式。

测试的名字应该概括它所测试的行为。一个好的名字既能描述在系统上采取的行动，又能描述预期的结果。测试名称有时会包括额外的信息，如系统或其环境的状态。一些语言和框架允许测试相互嵌套，并使用字符串命名，例如例12-13，其中使用了Jasmine，这样做比其他语言和框架更容易。

其他语言要求我们在方法名中编码所有这些信息，导致方法的命名模式如例12-14所示。

像这样的名字比我们通常为产品代码中的方法所写的要啰嗦得多，但使用情况不同：我们从来不需要写代码来调用这些方法，而且它们的名字经常需要由人类在报告中阅读。因此，额外的言辞是有必要的。

许多不同的命名策略是可以接受的，只要它们在一个测试类中使用一致。如果你陷入困境，一个好的技巧是尝试用 "应该 "这个词来开始测试名称。当与被测类的名称一起使用时，这种命名方案允许将测试名称作为一个句子来阅读。例如，一个名为shouldNotAllowWithdrawalsWhenBalanceIsEmpty的BankAccount类的测试可以被理解为 "BankAccount不应该允许在余额为空时提款"。通过阅读套件中所有测试方法的名称，你应该对被测系统实现的行为有一个很好的了解。这样的名字也有助于确保测试集中在单个行为上：如果你需要在测试名称中使用 "and"这个词，很有可能你实际上是在测试多个行为，应该写多个测试!

清晰的测试在检查时通常是正确的；也就是说，很明显，只要看一眼，测试就做了正确的事情。这在测试代码中是可能的，因为每个测试只需要处理一组特定的输入，而产品代码必须被泛化以处理任何输入。对于产品代码，我们能够编写测试，确保复杂的逻辑是正确的。但测试代码没有那么奢侈--如果你觉得你需要写一个测试来验证你的测试，那就说明出了问题！这是不可能的。

复杂性最常以逻辑的形式引入。逻辑是通过编程语言的指令部分来定义的，如运算符、循环和条件。当一段代码包含逻辑时，你需要做一些心理预期来确定其结果，而不是仅仅从屏幕上读出来。不需要太多的逻辑就可以使一个测试变得更难理解。例如，例12-15中的测试在你看来是否正确？

这里没有什么逻辑：实际上只是一个字符串连接。但是，如果我们通过删除这一点逻辑来简化测试，一个错误就会立即变得清晰，如例12-16所示。

当写出整个字符串时，我们可以立即看到，我们期望URL中有两个斜杠，而不是一个。如果产品代码犯了类似的错误，此测试将无法检测到错误。重复基本URL是为了使测试更具描述性和意义而付出的小代价（见本章后面关于DAMP与DRY测试的讨论）。

如果人类不善于发现来自字符串连接的错误，那么我们更不善于发现来自更复杂的编程结构的错误，如循环和条件。这个教训很清晰：在测试代码中，坚持使用直线代码而不是复杂的逻辑，并在测试更具描述性的时候考虑容忍一些重复。我们将在本章后面讨论关于重复和代码共享的想法。

清晰度的最后一个方面与测试的编写方式无关，而是与工程师在测试失败时看到的信息有关。在一个理想的世界里，工程师可以通过阅读日志或报告中的失败信息来诊断一个问题，而不需要看测试本身。一个好的故障信息包含与测试名称相同的信息：它应该清楚地表达预期结果、实际结果和任何相关的参数。

下面是一个糟糕失败消息的示例：

测试失败是因为帐户已关闭，还是因为帐户预期将关闭，而测试失败是因为帐户未关闭？一条更好的失败消息清楚地将预期状态与实际状态区分开来，并提供有关结果的更多上下文：

好的库可以帮助我们更容易写出有用的失败信息。考虑一下例12-17中Java测试中的断言，第一个断言使用了经典的JUnit断言，第二个断言使用了Truth，一个由Google开发的断言库：

因为第一个断言只接收一个布尔值，所以它只能给出一个通用的错误信息，如 "预期<true>，但得到的是<false>"，这在失败的测试输出中不是很有意义。因为第二个断言明确地接收断言的主题，它能够给出一个更有用的错误信息。AssertionError: <[red, green, blue]>应该包含<orange>"。

并非所有的语言都有这样的辅助工具，但总是可以手动指定失败信息中的重要信息。例如，Go中的测试断言通常看起来像例12-18。

编写清晰的测试和避免脆弱性的最后一个方面与代码共享有关。大多数软件都试图实现一个称为DRY的原则——“不要重复你自己。”DRY指出，如果每个概念都在一个地方被规范地表示，并且代码重复保持在最低限度，那么软件就更容易维护。这种方法在简化更改方面尤其有用，因为工程师只需要更新一段代码，而不需要跟踪多个引用。。这种合并的缺点是，它可能会使代码变得不清楚，需要读者跟随引用链来理解代码在做什么。

在正常的产品代码中，为了使代码更容易修改和使用，这种缺点通常是一个很小的代价。但是这种成本/效益分析在测试代码的背景下有一点不同。好的测试被设计成稳定的，事实上，当被测试的系统发生变化时，你通常希望它们会被破坏。因此，当涉及到测试代码时，DRY并没有那么多的好处。同时，对于测试来说，复杂性的成本更高：产品代码具有测试套件的优势，可以确保它在变得复杂时继续工作，而测试必须独立进行，如果它们不明显正确，则可能出现错误。如前所述，如果测试变得足够复杂，以至于感觉需要自己的测试来确保它们正常工作，那么就会出现问题。

与其说是完全的DRY，不如说测试代码应该经常努力做到DAMP--也就是提倡 "描述性和有意义的短语"。在测试中，一点点的重复是可以的，只要这种重复能使测试更简单、更清晰。为了说明这一点，例12-19介绍了一些过于DRY的测试。

基于前面对清晰度的讨论，这段代码中的问题应该是显而易见的。首先，尽管测试主体非常简洁，但它们并不完整：重要的细节被隐藏在辅助方法中，读者如果不滚动到文件的完全不同部分就看不到这些方法。那些辅助方法也充满了逻辑，使它们更难以一目了然地验证（你发现了这个错误吗？） 当它被改写成使用DAMP时，测试就变得清晰多了，如例12-20所示。

这些测试有更多的重复，测试体也有点长，但额外的言辞是值得的。每个单独的测试都更有意义，不离开测试主体就可以完全理解。这些测试的读者可以确信，这些测试做了他们声称要做的事情，并且没有隐藏任何bug。

许多测试的结构是通过定义一组测试使用的共享值，然后通过定义测试来涵盖这些值如何交互的各种情况。例12-21说明了此类测试的模样。

此策略可以使测试非常简洁，但随着测试套件的增长，它会导致问题。首先，很难理解为什么选择某个特定值进行测试。在示例12-21中，幸运的是，测试名称澄清了正在测试的场景，但你仍然需要向上滚动到定义，以确认ACCOUNT_1和ACCOUNT_2适用于这些场景。更具描述性的常量名称（例如，CLOSED_ACCOUNT 和 ACCOUNT_WITH_LOW_BALANCE）有一些帮助，但它们仍然使查看被测试值的确切细节变得更加困难，并且重用这些值的方便性可以鼓励工程师这样做，即使名称不能准确描述测试需要什么。

工程师通常倾向于使用共享常量，因为在每个测试中构造单独的值可能会很冗长。实现此目标的更好方法是使用辅助方法（参见示例12-22）构造数据，该方法要求测试作者仅指定他们关心的值，并为所有其他值设置合理的默认值。在支持命名参数的语言中，这种构造非常简单，但是没有命名参数的语言可以使用构建器模式等构造来模拟它们（通常需要AutoValue等工具的帮助）：

使用辅助方法来构造这些值允许每个测试创建它需要的确切值，而不必担心指定不相关的信息或与其他测试相冲突。

测试共享代码的相关方法是通过设置/初始化逻辑。许多测试框架允许工程师在运行套件中的每个测试之前定义要执行的方法。如果使用得当，这些方法可以避免重复繁琐和不相关的初始化逻辑，从而使测试更清晰、更简洁。如果使用不当，这些方法会在单独的初始化方法中隐藏重要细节，从而损害测试的完整性。

设置方法的最佳用例是构造被测试对象及其合作者们。当大多数测试不关心用于构造这些对象的特定参数，并且可以让它们保持默认状态时，这非常有用。同样的想法也适用于测试替换的打桩返回值，这是一个我们在第13章中详细探讨的概念。

使用设置方法的一个风险是，如果这些测试开始依赖于设置中使用的特定值，它们可能导致测试不明确。例如，例12-23中的测试似乎不完整，因为测试的读者需要去寻找字符串“Donald Knuth”的来源。

像这样明确关心特定值的测试应该直接说明这些值，如果需要的话，可以覆盖setup方法中定义的默认值。如例12-24所示，所产生的测试包含了稍多的重复，但其结果是更有描述性和意义的。

最后一种在测试中共享代码的常见方式是通过从测试方法主体中调用 "辅助方法"。我们已经讨论了辅助方法如何成为简明地构建测试值的有用方法--这种用法是有必要的，但其他类型的辅助方法可能是危险的。

一种常见的辅助工具是对被测系统执行一套共同的断言的方法。极端的例子是在每个测试方法的末尾调用一个验证方法，它对被测系统执行一组固定的检查。这样的验证策略可能是一个不好的习惯，因为使用这种方法的测试是较少的行为驱动。有了这样的测试，就更难确定任何特定测试的意图，也更难推断出作者在编写测试时到底想到了什么情况。当bug被引入时，这种策略也会使它们更难被定位，因为它们会经常导致大量的测试开始失败。

然而，更有针对性的验证方法仍然是有用的。最好的验证辅助方法只断言其输入的一个概念性事实，与涵盖一系列条件的通用验证方法相反。当他们验证的条件在概念上很简单，但需要循环或条件逻辑来实现，如果将其包含在测试方法的主体中，就会降低清晰度，这样的方法特别有用。例如，例12-25中的辅助方法在测试中可能很有用，它涵盖了围绕账户访问的几种不同情况。

到目前为止，我们讨论的技术包括在单个测试类或测试套件中跨方法共享代码。有时，跨多个测试套件共享代码也很有价值。我们将这种代码称为测试基础框架。尽管它通常在集成或端到端测试中更有价值，但精心设计的测试基础框架可以使单元测试在某些情况下更易于编写。

自定义测试基础框架必须比在单个测试套件中发生的代码共享更谨慎地对待。在许多方面，测试基础框架的代码比其他测试代码更类似于产品代码，因为它可能有许多依赖它的调用者，并且在不引入破坏的情况下很难改变。大多数工程师不应该在测试他们自己的功能时对通用测试基础框架进行修改。测试基础框架需要被当作自己独立的产品，相应地，测试基础框架必须始终有自己的测试。

当然，大多数工程师使用的测试基础框架都是以知名的第三方库的形式出现的，如JUnit。有大量这样的库可以使用，在一个组织内对它们进行标准化应该尽可能早地和普遍地发生。例如，Google多年前规定Mockito是新的Java测试中唯一应该使用的模拟框架，并禁止新的测试使用其他模拟框架。这一规定在当时引起了一些对其他框架感到满意的人的不满，但今天，人们普遍认为这是一个好的举措，使我们的测试更容易理解和使用。

单元测试是我们作为软件工程师所拥有的最强大的工具之一，它可以确保我们的系统在面对意料之外的变化时仍能正常工作。但是，强大的力量伴随着巨大的责任，不小心使用单元测试会导致系统需要更多的努力来维护，需要更多的努力来更改，不然不会真正提高我们对所述系统的信心。

谷歌的单元测试远非完美，但我们发现遵循本章所述做法的测试比那些不遵循的测试要有价值得多。我们希望它们能帮助你提高你自己的测试的质量。
