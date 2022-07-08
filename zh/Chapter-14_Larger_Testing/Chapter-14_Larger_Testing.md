在前几章中，我们已经讲述了测试文化是如何在Google建立的，以及小单元测试是如何成为开发人员工作流程的基本组成部分。但是其他类型的测试呢？事实证明，Google确实使用了许多大型测试，这些测试构成了健康的软件工程所需的风险缓解策略的重要组成部分。但是，这些测试导致了额外的挑战，以确保它们是有价值的资产而不是资源黑洞。在这一章中，我们将讨论什么是 "大型测试"，什么时候执行这些测试，以及保持其有效性的最佳做法。

如前所述，谷歌对测试规模有特定的概念。小型测试仅限于一个线程、一个进程、一台机器。较大的测试没有相同的限制。但谷歌也有测试范围的概念。单元测试的范围必然比集成测试的范围小。而最大范围的测试（有时被称为端到端或系统测试）通常涉及多个实际依赖项和较少的测试替身。（`Test Double`是在Martin Fowler的文章[Test Double](https://martinfowler.com/bliki/TestDouble.html)中，Gerard Meszaros提出了这个概念。虽然是06年的文章了，但里面的概念并不过时。这篇文章提到`Test Double`只是一个通用的词，代表为了达到测试目的并且减少被测试对象的依赖，使用“替身”代替一个真实的依赖对象，从而保证了测试的速度和稳定性。统一翻译为测试替代）

较大的测试有许多是小型测试所不具备的内容。它们不受相同的约束；因此，它们可以表现出以下特征：

那么，为什么要进行大型测试？回想一下你的编码过程。你是如何确认你写的程序真的能工作的？你可能边写边运行单元测试，但你是否发现自己在运行实际的二进制文件并亲自体验？而当你与他人分享这些代码时，他们是如何测试的呢？是通过运行你的单元测试，还是通过自己体验？

另外，你怎么知道你的代码在升级时还能继续工作？假设你有一个使用谷歌地图API的网站，有一个新的API版本。你的单元测试很可能无法帮助你知道是否有任何兼容性问题。你可能会运行它，试一试，看看是否有什么故障。

单元测试可以让你对单个功能、对象和模块有信心，但大型测试可以让你对整个系统按预期工作更有信心。而以手动测试无法实现的方式进行实际的自动化测试。

大型测试存在的主要原因是为了解决保真度问题。仿真度是测试反映被测系统（SUT）真实行为的属性。

一种设想保真度的方法是在环境方面。如图14-1所示，单元测试将测试和一小部分代码捆绑在一起作为一个可运行的单元，这确保了代码得到测试，但与生产代码的运行方式有很大不同。产品本身才是测试中仿真度最高的环境。也有一系列的临时选项。大型测试的一个关键是要找到适当的契合点，因为提高仿真度也伴随着成本的增加和（在线上的情况下）故障风险的增加。

测试也可以用测试内容对现实的仿真度程度来衡量。如果测试数据本身看起来不真实，许多手工配置的大型测试就会被工程师驳回。从生产中复制的测试数据仿真度更高（以这种方式捕获），但一个很大的挑战是如何在*启动新代码之前创建真实的测试流量。这在人工智能（AI）中尤其是一个问题，因为 "种子 "数据经常受到内在偏见的影响。而且，由于大多数单元测试的数据是手工配置的，它涵盖的案例范围很窄，并倾向于符合作者的偏见。数据所遗漏的场景代表了测试中的仿真度差距。

如果较小的测试失败，也可能需要进行较大的测试。下面的小节介绍了单元测试无法提供良好风险缓解覆盖率的一些特殊领域

一个单元测试通常覆盖一个类或模块。测试替代（如第13章所讨论的）经常被用来消除重量级或难以测试的依赖项。但是当这些依赖关系被替换时，就有可能出现替换后的东西和被替换的东西不一致的情况。

在谷歌，几乎所有的单元测试都是由编写被测单元的工程师编写的。当这些单元测试需要替代时，当使用的替代是模拟时，是编写单元测试的工程师在定义模拟和它的预期行为。但该工程师通常没有*写被模拟的东西，因此可能对其实际行为有误解。被测单元与给定对等方之间的关系是一种行为契约，如果工程师对实际行为有误解，则对契约的理解无效。

此外，模拟会变得过时。如果实际实现的作者看不到这个基于模拟的单元测试，并且实际实现发生了变化，那么就没有信号表明应该更新测试（以及正在测试的代码）以跟上变化。

请注意，正如在第13章中提到的，如果团队为他们自己的服务提供模拟，这种担忧大多会得到缓解。

单元测试涵盖了给定二进制中的代码。但该二进制文件在如何执行方面通常不是完全自足的。通常情况下，二进制文件有某种部署配置或启动脚本。此外，真正为终端用户服务的生产实例有他们自己的配置文件或配置数据库。

如果这些文件存在问题，或者这些存储定义的状态与有问题的二进制文件之间存在兼容性问题，则可能会导致重大的用户故障。单元测试不能验证这种兼容性。顺便说一下，这是一个很好的理由，确保你的配置和你的代码一样在版本控制中，因为这样，配置的变更可以被识别为bug的来源，而不是引入随机的外部碎片，并且可以在大型测试中构建。

在谷歌，配置变更是我们重大故障的头号原因。这是一个我们表现不佳的领域，并导致了我们一些最尴尬的错误。例如，2013年，由于一次从未测试过的糟糕网络配置推送，谷歌出现了一次全球停机。它们通常也比二进制文件具有更快的生产部署周期，而且它们可能更难测试。所有这些都会导致更高的失败可能性。但至少在这种情况下（和其他情况下），配置是由版本控制的，我们可以快速识别故障并缓解问题。

在谷歌，单元测试的目的是小而快，因为它们需要适配标准测试执行基础设施，也可以作为顺畅的开发人员工作流程的一部分多次运行。但性能、负载和压力测试往往需要向一个特定的二进制文件发送大量的流量。这些流量在典型的单元测试模型中变得难以制造。而我们的大流量是很大的，往往是每秒数千或数百万次的查询（在广告的情况下，实时竞价）!

单元测试受到编写它们的工程师想象力的限制。也就是说，他们只能测试预期的行为和输入。然而，用户在产品中发现的问题大多是未预料到的（否则，他们不太可能将其作为问题提交给最终用户）。这一事实表明，需要不同的测试技术来测试非预期的行为。

海勒姆定律在这里是一个重要的考虑因素：即使我们可以100%测试是否符合严格的规定合同，有效的用户合同也适用于所有可见的行为，而不仅仅是规定的合同。单元测试不太可能单独测试公共API中未指定的所有可视行为。

单元测试仅限于它们所覆盖的范围（特别是随着测试替代的广泛使用），因此如果在此范围之外的区域发生行为变化，则无法检测到。由于单元测试被设计为快速可靠，它们故意去除了真实依赖、网络和数据的混乱。单元测试就像理论物理中的一个问题：运行在真空中，巧妙地隐藏在现实世界的混乱中，这有助于提高速度和可靠性，但忽略了某些缺陷类别。

在前面的章节中，我们讨论了对开发者友好的测试的许多特性。特别是，它需要做到以下几点：

好的单元测试展现出这些特性。大型测试经常违反这些限制。例如，大型测试往往是脆弱的，因为它们比小单元测试使用更多的基础设施。它们的设置和运行速度也往往慢得多。而且，由于资源和时间的要求，它们在扩展上有困难，但往往也因为它们不是孤立的--这些测试可能会相互冲突。

此外，大型测试还带来了另外两个挑战。首先，所有权是一个挑战。单元测试显然由拥有单元的工程师（和团队）拥有。较大的测试跨越多个单元，因此可以跨越多个所有者。这带来了一个长期的所有权挑战：谁负责维护测试，谁负责在测试中断时诊断问题？没有明确的所有权，测试就会腐化。

大型测试的第二个挑战是标准化问题（或缺乏标准化）。与单元测试不同，大型测试在编写、运行和调试的基础设施和流程方面缺乏标准化。大型测试的方法是系统架构决策的产物，因此在所需的测试类型中引入了差异性。例如，我们在谷歌广告中建立和运行A-B差异回归测试的方式与在搜索后端建立和运行此类测试的方式完全不同，而搜索后端又与驱动不同。他们使用不同的平台，不同的语言，不同的基础设施，不同的库，以及相互竞争的测试框架。

这种缺乏标准化的情况有很大的影响。因为大型测试有许多运行方式，在大规模的变更中，它们经常被忽略。(见第22章) 基础设施没有一个标准的方式来运行这些测试，要求执行LSC的人员了解每个团队测试的本地细节是不可行的。因为更大的测试在各个团队的实施中是不同的，因此实际测试这些团队之间集成的测试需要统一不兼容的基础架构。而且由于缺乏标准化，我们无法向Nooglers（新的Googlers）甚至更有经验的工程师传授单一的方法，这既使情况长期存在，也导致人们对这种测试的动机缺乏了解。

当我们在前面讨论Google的测试历史时（见第11章），我们讨论了Google Web Server（GWS）如何在2003年强制执行自动化测试，以及这是一个分水岭时刻。然而，在这之前，我们实际上已经有了自动化测试的使用，但一个普遍的做法是使用自动化的大型测试。例如，AdWords早在2001年就创建了一个端到端的测试来验证产品方案。同样，在2002年，搜索公司为其索引代码写了一个类似的 "回归测试"，而AdSense（当时甚至还没有公开推出）在AdWords的测试上创造了它的变种。

其他 "较大 "的测试模式也开始于2002年左右。谷歌搜索前端在很大程度上依赖于手动QA--端到端的测试场景的手动版本。Gmail得到了它的 "本地演示 "环境的版本--一个脚本，在本地建立一个端到端的Gmail环境，其中有一些生成的测试用户和邮件数据，用于本地手动测试。

当C/J Build（我们的第一个持续构建框架）推出时，它并没有区分单元测试和其他测试，但有两个关键的发展导致了分裂。首先，Google专注于单元测试，因为我们想鼓励测试金字塔，并确保绝大部分的测试是单元测试。第二，当TAP取代C/J Build成为我们正式的持续构建系统时，它只能为符合TAP资格要求的测试服务：可在一次修改中构建的密封测试，可在最大时间限制内运行在我们的构建/测试集群上。尽管大多数单元测试满足了这一要求，但大型测试大多不满足。然而，这并没有阻止对其他类型的测试的需求，而且它们一直在填补覆盖率的空白。C/J Build甚至坚持了多年，专门处理这些类型的测试，直到更新的系统取代它。

在本书中，我们一直在关注时间对软件工程的影响，因为谷歌已经开发了运行20多年的软件。大型测试是如何受到时间维度的影响的？我们知道，代码的生命周期越长，某些活行为就越有意义，各种形式的测试是一种在各个层面都有意义的活动，但适合的测试类型会随着代码的生命周期而改变。

正如我们之前所指出的，单元测试对于预生命周期在几小时以上的软件开始有意义。在分钟级别（小型脚本），手动测试是最常见的，SUT通常在本地运行，但本地demo很可能就是*产品*，特别是对于一次性的脚本、演示或实验。在更长的生命期，手动测试继续存在，但SUT通常是分歧的，因为生产实例通常是云托管而不是本地托管。

其余大型测试都为生命周期较长的软件提供了价值，但随着时间的增加，主要的问题变成了这种测试的可维护性。

顺便说一句，这一时间冲击可能是开发“冰淇淋筒”测试反模式的原因之一，如第11章所述，图14-2再次显示

当开发从手动测试开始时（当工程师认为代码只能持续几分钟时），那些手动测试就会积累起来并主导最初的整体测试组合。例如，入侵脚本或应用程序并通过运行它来测试它，然后继续向其添加功能，但继续通过手动运行来测试它，这是非常典型的。该原型最终会变得功能化，并与其他人共享，但实际上不存在针对它的自动测试。

更糟糕的是，如果代码很难进行单元测试（因为它最初的实现方式），那么唯一可以编写的自动化测试就是端到端的测试，并且我们在几天内无意中创建了“遗留代码”。

在开发的头几天，通过建立单元测试，向测试金字塔迈进，然后在这之后通过引入自动化集成测试，摆脱手动端到端的测试，这对长期的稳定是*稳健*。我们成功地使单元测试成为提交的要求，但弥补单元测试和手工测试之间的差距对长期稳健是必要的。

在软件规模较大的情况下，大型测试似乎更有必要，也更合适，但即使如此，编写、运行、维护和调试这些测试的复杂性也会随着规模的增长而增加，甚至比单元测试更复杂。

在由微服务或独立服务器组成的系统中，互连模式看起来像一个图：让该图中的节点数为我们的N。每次向该图添加新节点时，都会对通过该图的不同执行路径的数量产生乘法效应。

图14-3描绘了一个想象中的SUT：这个系统由一个有用户的社交网络、一个社交图、一个feed流和一些混合广告组成。广告由广告商创建，并在社会流的背景下提供服务。这个SUT单独由两组用户、两个UI、三个数据库、一个索引管道和六个服务器组成。图中列举了14条边。测试所有端到端的可能性已经很困难了。想象一下，如果我们在这个组合中添加更多的服务、管道和数据库：照片和图像、机器学习照片分析等等？

以端到端的方式测试的不同场景的速率可以指数增长或组合增长，这取决于被测系统的结构，并且这种增长不会扩展。因此，随着系统的发展，我们必须找到其他更大的测试策略，以保持事情的可管理性。

然而，由于实现这一规模所需的决策，此类测试的价值也增加了。这是仿真度的一个影响：随着我们向更大的N层软件发展，如果服务的仿真度加倍（1ε），那么当把所有的服务放在一起时，出现错误的几率是N的指数。再看看这个例子SUT，如果我们用双倍替代用户服务器和广告服务器，并且这些加倍的仿真度较低（例如，10%的准确度），出现错误的可能性为99%（1–（0.1∗ 0.1)). 这只是两个低仿真度的替代。

因此，以在这种规模下工作良好但保持合理高仿真度的方式实现更大的测试变得至关重要。

即便是集成测试，也是越小越好--少数大型测试比一个超大测试要好。而且，因为测试的范围经常与SUT的范围相联系，找到使SUT变小的方法有助于使测试变小。

当出现一个需要许多内部系统服务的用户请求时，实现这种测试比率的一种方法是 "连锁 "测试，如图14-4所示，不是具体执行，而是创建多个较小的成对集成测试，代表整个场景。

尽管大型测试不受小型测试约束的约束，并且可以由任何内容组成，但大多数大型测试都显示出共同的模式。大型测试通常由具有以下阶段的流程组成：

大型测试的一个关键组成部分是前述的SUT（见[图14-5](#_bookmark1234)）。一个典型的单元测试将关注点集中在一个类或模块上。此外，测试代码运行在与被测试代码相同的进程（或Java虚拟机[JVM]，在Java的情况下）。对于大型测试，SUT通常是非常不同的；一个或多个独立的进程，测试代码通常（但不总是）在自己的进程中。

在谷歌，我们使用许多不同形式的SUT，而SUT的范围是大型测试本身范围的主要驱动因素之一（SUT越大，测试越大）。每种SUT形式都可以根据两个主要因素来判断。

通常有这两个因素是直接冲突的。以下是一些SUT的例子。

大型测试中的SUT可能是不可靠性和长周转时间的主要原因。例如，生产中的测试使用实际的生产系统部署。如前所述，这很受流行，因为没有额外的环境开销成本，但在代码到达生产环境之前，生产测试无法运行，这意味着这些测试本身无法阻止将代码发布到生产环境--SUT本质上太晚了。

最常见的第一种选择是创建一个巨大的共享临时环境并在那里运行测试。这通常是作为某些发布升级过程的一部分来完成的，但它再次将测试执行限制为仅当代码可用时。作为一个替代方案，一些团队允许工程师在临时环境中 "保留 "时间，并使用该时间窗口来部署待定的代码和运行测试，但这并不能随着工程师数量的增加或服务数量的增加而扩展，因为环境、用户数量和用户冲突的可能性都会迅速增加。

下一步是支持云隔离的或机器密闭的SUT。这样的环境通过避免代码发布的冲突和保留要求来改善情况。

我们提到，在生产中进行测试是有风险的。我们需要一种方法来验证YouTube生产中的视频渲染是否正常，因此创建了自动脚本来生成测试视频，上传它们，并验证上传质量，这是在谷歌拥有的名为Webdriver Torso的YouTube中进行的。

后来，这个渠道在《连线》杂志的一篇文章中被公布，这导致它在媒体上传播，随后人们努力解开这个谜团。最后，我们通过与它进行一些互动，包括一个Rickroll和一个复活节彩蛋，所以一切都很顺利。但我们确实需要考虑最终用户发现我们在生产中包含的任何测试数据的可能性并做好准备。

有一些特别痛苦的测试界限，值得避免。同时涉及前台和后台的测试变得很痛苦，因为用户界面（UI）测试是出了名的不可靠和高成本：

尽管对服务的UI进行端到端测试非常有用，但这些测试会增加UI和后端的维护成本。相反，如果后端提供公共API，则通常更容易在UI/API边界将测试拆分为连接的测试，并使用公共API驱动端到端测试。无论UI是浏览器、命令行界面（CLI）、桌面应用程序还是移动应用程序，都是如此。

另一个特殊的边界是第三方依赖关系。第三方系统可能没有用于测试的公共共享环境，在某些情况下，向第三方发送流量会产生成本。因此，不建议让自动匹配的测试使用真正的第三方API，并且依赖性是分割测试的一个重要接点。

为了解决规模问题，我们通过用内存数据库替换它的数据库，并移除SUT范围之外的一个我们真正关心的服务器，使这个SUT变得更小，如图14-6所示。这个SUT更可能适合在一台机器上使用。

关键是要确定仿真度和成本/可靠性之间的权衡，并确定合理的边界。如果我们能够运行少量的二进制文件和一个测试，并将其全部打包到进行常规编译、链接和单元测试执行的同一台机器上，我们就能为我们的工程师提供最简单、最稳定的 "集成 "测试。

在前一章中，我们讨论了测试加倍和可用于将被测类与其难以测试的依赖项解耦的方法。我们还可以通过使用具有等效API的模拟、存根或伪服务器或进程来复制整个服务器和进程。然而，无法保证所使用的双重测试实际上符合其所替换的真实对象的契约。

处理SUT的依赖关系和附属服务的一种方法是使用测试替代，但如何知道替代反映了依赖的实际行为？在谷歌之外，一种正在发展的方法是使用一个框架进行消费者驱动的合同测试。这些测试为客户和服务的提供者定义了一个合同，这个合同可以驱动自动测试。也就是说，一个客户定义了一个服务的模拟，说对于这些输入参数，我得到一个特定的输出。然后，真正的服务在真正的测试中使用这个输入/输出对，以确保它在这些输入的情况下产生那个输出。消费者驱动的合同测试的两个公共工具是[Pact Contract Testing](https://docs.pact.io/)和[Spring Cloud Con-](https://oreil.ly/szQ4j) [tracts](https://oreil.ly/szQ4j)。谷歌对协议缓冲区的严重依赖意味着我们内部不使用这些工具。

在谷歌，我们做的有些不同。我们最流行的方法（有公共API）是使用较大的测试生成较小的测试，方法是在运行较大的测试时记录到这些外部服务的流量，并在运行较小的测试时重播流量。大型或“记录模式”测试在提交后持续运行，但其主要目的是生成这些流量日志（但必须通过才能生成日志）。在开发和提交前测试过程中，使用较小的或“重播模式”测试。

记录/重放工作原理的一个有趣方面是，由于非终结性，必须通过匹配器匹配请求，以确定重放的响应。这使得它们与存根和模拟非常相似，因为参数匹配用于确定结果行为。

新测试或客户端行为发生显著变化的测试会发生什么情况？在这些情况下，请求可能不再与记录的流量文件中的内容匹配，因此测试无法在重放模式下通过。在这种情况下，工程师必须以记录模式运行测试以生成新的通信量，因此使运行记录测试变得简单、快速和稳定非常重要。

测试需要数据，大型测试需要两种不同的数据：

由于独立的和更大的SUT的概念，SUT状态的种子工作往往比单元测试中的设置工作要复杂得多。比如说：

数据可以通过不同的方式产生，比如说以下几种：

在SUT运行并向其发送流量后，我们仍然必须验证其行为。有几种不同的方法可以做到这一点。

需要注意的是，人工回归测试的规模不是线性的：系统越大，通过它的操作越多，需要人力测试的时间就越多。

我们现在可以将这些不同的方法组合到SUT、数据和断言中，以创建不同类型的大型测试。然后，每项测试都有不同的特性，可以降低哪些风险；编写、维护和调试它需要多少劳动；以及它在运行资源方面的成本。

下面是我们在谷歌使用的各种大型测试的列表，它们是如何组成的，它们的用途是什么，它们的局限性是什么：

考虑到如此广泛的组合和如此广泛的测试，我们如何管理做什么以及何时做？软件设计的一部分是起草测试计划，而测试计划的一个关键部分是需要什么类型的测试以及每种测试需要多少的战略大纲。该测试策略确定了主要风险向量和缓解这些风险向量的必要测试方法。

在谷歌，我们有一个专门的工程角色“测试工程师”，我们在一个好的测试工程师身上寻找的东西之一就是能够为我们的产品勾勒出一个测试策略。

此类试验具有以下特点：

到目前为止，我们已经看到，单元测试无法以真正的反正测试复杂系统，仅仅是因为它们的打包方式与实际代码的打包方式不同。许多功能测试场景与给定二进制文件的交互方式不同于与该二进制文件中的类的交互方式，这些功能测试需要单独的SUT，因此是规范的、更大的测试。

毫不奇怪，测试多个二进制文件的相互作用甚至比测试单个二进制文件更复杂。一个常见的案例是在微服务环境中，当服务被部署为许多独立的二进制文件。在这种情况下，功能测试可以通过提出由所有相关二进制文件组成的SUT，并通过发布的API与之交互，来覆盖二进制文件之间的真实交互。

测试web UI和移动应用程序是对一个或多个交互二进制文件进行功能测试的特例。可以对底层代码进行单元测试，但对于最终用户来说，公共API是应用程序本身。将测试作为第三方通过其前端与应用程序交互提供了额外的覆盖层。

此类试验具有以下特点：

尽管可以在性能、负载和压力方面测试小型单元，但此类测试通常需要同时向外部API发送通信量。该定义意味着此类测试是多线程测试，通常在被测二进制文件的范围内进行测试。但是，这些测试对于确保版本之间的性能不会下降以及系统能够处理预期的流量峰值至关重要。

随着负载测试规模的增长，输入数据的范围也在增长，甚至很难在负载下生成触发bug所需的负载规模。负载和压力处理是系统的 "高度涌现 "属性；也就是说，这些复杂的行为属于整个系统，而不是个别组成。因此，重要的是使这些测试看起来尽可能地接近生产。每个SUT所需的资源与生产所需的资源类似，因此很难缓解生产拓扑中的噪音。

消除性能测试中的噪音的一个研究领域是修改部署拓扑结构--各种二进制文件在机器网络中的分布。运行二进制文件的机器会影响性能特性；因此，如果在性能差异测试中，基本版本在快速机器（或具有高速网络的机器）上运行，而新版本在慢速机器上运行，则可能会出现性能回归。此特性意味着最佳部署是在同一台机器上运行两个版本。如果一台机器无法同时安装两种版本的二进制文件，另一种方法是通过执行多次运行并消除峰值和谷值来进行校准。

此类试验具有以下特点：

很多时候，缺陷的根源不是代码，而是配置：数据文件、数据库、选项定义等等。较大的测试可以测试SUT与其配置文件的集成，因为这些配置文件是在给定二进制文件启动期间读取的。

这种测试实际上是SUT的烟雾测试，不需要太多额外的数据或验证。如果SUT成功启动，则测试通过。否则，测试失败。

此类试验具有以下特点：

探索性测试是一种手动测试，它的重点不是通过重复已知的测试流来寻找行为回归，而是通过尝试新的用户场景来寻找有问题的行为。训练有素的用户/测试人员通过产品的公共API与产品交互，在系统中寻找新的路径，寻找行为偏离预期或直观行为的路径，或者是否存在安全漏洞。

探索性测试对于新系统和已发布系统都很有用，可以发现意外行为和副作用。通过让测试人员在系统中遵循不同的可到达路径，我们可以增加系统覆盖率，并且当这些测试人员发现bug时，可以捕获新的自动化功能测试。在某种意义上，这有点像功能集成测试的手动“模糊测试”版本。

手动测试不会进行次线性扩展；也就是说，执行手动测试需要人工时间。通过探索性测试发现的任何缺陷都应该通过能够更频繁地运行的自动化测试进行复制。

我们用于手动探索性测试的一种常见方法是bug bash。一组工程师和相关人员（经理、产品经理、测试工程师、熟悉产品的任何人）安排了一次“会议”，但在此情况下，所有相关人员都会手动测试产品。对于bug bash的特定关注领域和/或使用系统的起点，可能会有一些已发布的指南，但目标是提供足够的交互多样性，以记录有问题的产品行为和底层的bug。

此类试验具有以下特点：

单元测试覆盖了一小部分代码的预期行为路径。但是，对于给定的面向公众的产品，预测许多可能的故障模式是不可能的。。此外，正如Hyrum's Law所指出的，实际的公共API不是声明的API，而是一个产品的所有用户可见的方面。鉴于这两个特性，A/B对比测试可能是谷歌最常见的大型测试形式，这并不奇怪。这种方法在概念上可以追溯到1998年。在谷歌，我们从2001年开始为我们的大多数产品进行基于这种模式的测试，从广告、搜索和地图开始。

还有其他的变体。我们使用A-A测试（将系统与自身进行比较）来识别非决定性行为、噪音和松散型，并帮助从A-B差异中去除这些东西。我们有时也会使用A-B-C测试，比较最后的生产版本、基线构建和一个待定的变化，以便一眼就能看出即时更改的影响，以及下一个发布版本的累积影响。

对比测试确实带来了一些需要解决的挑战：

此类试验具有以下特点：

单元测试的一个关键方面是，它们是由编写被测代码的开发人员编写的。但是，这使得对产品的*预期行为的误解很可能不仅反映在代码中，而且也反映在单元测试中。这样的单元测试验证了代码是 "按实现工作 "而不是 "按预期工作"。

对于有特定终端客户或客户代理（客户委员会甚至产品经理）的情况，UAT是通过公共API执行产品的自动化测试，以确保特定用户工作的总体行为符合预期。存在多个公共框架（例如，Cucumber和RSpec），使这种测试可以用用户友好的语言写/读，通常是在 "可运行规范 "的背景下。

谷歌实际上并没有做很多自动化的UAT，也不怎么使用规范语言。谷歌的许多产品在历史上都是由软件工程师自己创建的。几乎不需要可运行的规范语言，因为那些定义预期产品行为的规范语言通常能够流利地使用实际的编码语言。

此类试验具有以下特点：

探针和金丝雀分析是确保生产环境本身健康的方法。在这些方面，它们是生产监控的一种形式，但在结构上与其他大型测试非常相似。

金丝雀分析也是类似的，只不过它关注的是一个版本何时被推送到生产环境。如果发布是分阶段进行的，我们可以同时运行针对升级（金丝雀）服务的探针断言，以及比较生产中金丝雀和基线部分的健康指标，并确保它们没有失衡。

探针应该在任何实时系统中使用。如果生产推广过程包括一个阶段，其中二进制文件被部署到生产机器的有限子集的阶段（一个金丝雀阶段），金丝雀分析应该在该过程中使用。

此时（生产中）发现的任何问题都已经影响到最终用户。

如果探针执行可变（写入）操作，它将修改生产状态。这可能导致以下三种结果之一：不确定性和评估失败、未来写入能力失败或用户可见的副作用。

此类试验具有以下特点：

这些测试将测试系统对意外更改或故障的反应。

多年来，谷歌每年都会举办一场名为“灾难恢复测试”[DiRT](https://oreil.ly/17ffL)(Disaster Recovery Testing)的演练，在这场演练中，故障几乎以全球规模注入我们的基础设施。我们模拟了从数据中心火灾到恶意攻击的一切。在一个令人难忘的案例中，我们模拟了一场地震，将我们位于加州山景城的总部与公司其他部门完全隔离。这样做不仅暴露了技术上的缺陷，也揭示了在所有关键决策者都无法联系到的情况下，管理公司的挑战。

这些类型的故障和负面测试对于具有足够理论容错能力的实时生产系统是有意义的，并且测试本身的成本和风险是可以承受的。

此时（生产中）发现的任何问题都已经影响到最终用户。

如果探针执行了一个可变（写）的动作，它将修改生产的状态。这可能导致非确定性和断言的失败，未来写入能力的失败，或用户可见的副作用。

此类试验具有以下特点：

基于产品的测试可以收集大量关于用户行为的数据。我们有几种不同的方法来收集有关即将推出的功能的受欢迎程度和问题的指标，这为我们提供了UAT的替代方案：

我们已经讨论了什么是大型测试，为什么要做测试，什么时候做，做多少测试，但我们还没有说太多是谁的问题。谁来写测试？谁来运行测试并调查故障？谁拥有这些测试？我们如何让这一切变得可以忍受？

尽管标准的单元测试基础设施可能不适用，但将大型测试集成到开发人员的工作流程中仍然是至关重要的。做到这一点的一个方法是确保存在预提交和后提交执行的自动化机制，即使这些机制与单元测试的机制不同。在谷歌，许多大型测试不属于TAP。它们不密封、太薄和/或资源密集。但是我们仍然需要防止它们被破坏，否则它们就不能提供任何信号，并且变得太难处理了。那么，我们所做的就是为这些测试建立一个单独的提交后持续构建。我们也鼓励在提交前运行这些测试，因为这样可以直接向作者提供反馈。

需要手动批准的A/B对比测试也可以被纳入这样一个工作流程。对于预提交，在批准更改之前批准UI中的任何差异可能是代码审查要求。我们有一个这样的测试，如果提交的代码有未解决的差异，就会自动归档阻断发布的错误。

在某些情况下，测试是如此之大或痛苦，以至于提交前的执行增加了太多的开发者负担。这些测试仍然在提交后运行，并且作为发布过程的一部分运行。不在提交前运行这些测试的缺点是，bug会进入monorepo，我们需要确定罪魁祸首的变化来回滚它。但我们需要在开发人员的痛苦和所产生的变更延迟与持续构建的可靠性之间做出权衡。

虽然大型测试的结构是相当标准的，但创建这样的测试仍然存在挑战，特别是当团队中有人第一次操作时。

要使写这种测试成为可能，最好的办法是有明确的库、文档和例子。单元测试很容易写，因为有本地语言的支持（JUnit曾经是深奥的，但现在是主流）。我们重新使用这些断言库进行功能集成测试，但随着时间的推移，我们也创建了与SUT交互的库，用于运行A/B差异，用于播种测试数据，以及用于协调测试工作流。

大型测试在资源和人力时间方面的维护成本较高，但不是所有的大型测试都是一样的。A/B对比测试受欢迎的一个原因是，它们在维护验证步骤方面的人力成本较低。同样，生产型SUT的维护成本比隔离的封闭型SUT要低。而且，由于所有这些自创的基础设施和代码都必须被维护，成本的节省可以是落地的。

然而，必须从整体上看待这一成本。如果手动协调差异或支持和保护生产测试的成本超过了节省的成本，那么它将变得无效。

我们在上面提到，我们的大型测试不适合在TAP中进行，所以我们为它们准备了备用的持续构建和预提交。对我们的工程师来说，最初的挑战之一是如何运行非标准的测试，以及如何对它们进行迭代。

我们尽可能地使我们的大型测试以工程师熟悉的方式运作。我们的预提交基础设施在运行这些测试和运行TAP测试之前都提供了一个通用的API，我们的代码审查基础设施显示了这两组结果。但许多大型测试是定制的，因此需要具体的文档来说明如何按需运行它们。对于不熟悉的工程师来说，这可能是一个令人沮丧的原因。

工程师不会等待缓慢的测试。测试越慢，工程师运行测试的频率就越低，失败后等待测试再次通过的时间就越长。

加速测试的最佳方法通常是缩小其范围，或者将大型测试拆分为两个可以并行运行的小型测试。但是，您还可以使用其他一些技巧来加速更大的测试。

一些简单的测试会使用基于时间延迟注入来等待非确定性的动作发生，这在大型测试中是很常见的。但是，这些测试没有线程限制，并且实际生产用户希望等待的时间尽可能少，因此最好让测试以实际生产用户的方式做出反应。方法包括：  

请注意，当运行这些测试的负载变得超载时，依赖延时和超时的测试都会开始失败，这是因为这些测试需要更频繁地重新运行，进一步增加了负载。

对于单元测试来说，松散性已经很糟糕了，但对于大型测试来说，它可能会使它们无法使用。一个团队应该把消除这种测试的松散性视为一个高度优先事项。但是，如何才能从这些测试中消除松散性呢？

最大限度地减少松散，首先要减少测试的范围--封闭的SUT不会有生产或共享暂存环境的各种多用户和真实世界松散的风险，单机封闭的SUT不会有分布式SUT的网络和部署闪失问题。但是你可以通过测试设计和实施以及其他技术来减轻其他的松散性问题。在某些情况下，你需要平衡这些与测试速度。

正如使测试反应式或事件驱动可以加快它们的速度一样，它也可以消除松散性。定时休眠需要超时维护，这些超时可以嵌入测试代码中。增加系统的内部超时可以减少松散性，而减少内部超时可以导致松散性，如果系统的行为是不确定的。这里的关键是确定一个权衡（平衡），既要为终端用户定义一个可容忍的系统行为（例如，我们允许的最大超时是*n*秒），但很好地处理了不稳定的测试执行行为。

内部系统超时的一个更大问题是，超过这些超时会导致难以分类的错误。生产系统通常会试图通过优雅地方式处理可能的内部系统问题来限制终端用户对灾难性故障的暴露。例如，如果谷歌不能在给定的时间限制内提供广告，我们不会返回500，我们只是不提供广告。但在测试运行人员看来，如果只是出现异常超时问题，广告服务可能会被中断。在这种情况下，重要的是使故障模式变得明显，并使调整测试场景的此类内部超时变得容易

当这些测试产生的结果对运行测试的工程师来说是无法理解的时候，就很难将测试整合到开发者的工作流程中。即使是单元测试也会产生一些混乱--如果我的修改破坏了你的测试，如果我一般不熟悉你的代码，就很难理解为什么，但对于大型测试，这种混乱可能是无法克服的。坚定的测试必须提供一个明确的通过/失败信号，并且必须提供有意义的错误输出，以帮助分类失败的原因。需要人工调查的测试，如A/B对比测试，需要特殊处理才能有意义，否则在预提交期间有被跳过的风险。

这在实践中是如何运作的？一个成功的大型测试应该从失败中获取到信息，要做到以下几点：

大型测试必须有记录的所有者--他们可以充分审查测试的变更，并且在测试失败的情况下，可以依靠他们提供支持。没有适当的所有权，测试可能成为以下情况的受害者：

而且测试也会腐烂。

特定项目中组件的集成测试应由项目负责人负责。以功能为中心的测试（覆盖一组服务中特定业务功能的测试）应由“功能所有者”负责；在某些情况下，该所有者可能是负责端到端功能实现的软件工程师；在其他情况下，可能是负责业务场景描述的产品经理或“测试工程师”。无论谁拥有该测试，都必须有权确保其整体健康，并且必须具备支持其维护的能力和这样做的激励。

如果以结构化的方式记录此信息，则可以围绕测试所有者构建自动化。我们使用的一些方法包括：

一个全面的测试套件需要大型测试，既要确保测试与被测系统的仿真度相匹配，又要解决单元测试不能充分覆盖的问题。因为这样的测试必然更复杂，运行速度更慢，所以必须注意确保这样的大型测试是正确的，良好的维护，并在必要时运行（例如在部署到生产之前）。总的来说，这种大型测试仍然必须尽可能的小（同时仍然保留仿真度），以避免开发人员的阻力。一个全面的测试策略，确定系统的风险，以及解决这些风险的大型测试，对大多数软件项目来说是必要的。
