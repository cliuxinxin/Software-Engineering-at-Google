在实践中，"频繁地集成工作 "对于现代的、分布式的应用程序意味着什么？今天的系统除了存储库中最新版本的代码外，还有许多可移动的部分。事实上，随着近来的微服务趋势，破坏应用程序的变化不太可能存在于项目的即时代码库中，而更可能存在于网络调用的另一端的松散耦合的微服务中。传统的持续构建是测试二进制文件的变更，而其延伸则是测试上游微服务的变化。依赖性只是从你的函数调用栈转移到HTTP请求或远程过程调用（RPC）。

甚至在代码依赖性之外，应用程序可能会定期接收数据或更新机器学习模型。它可能在不断发展的操作系统、运行时、云托管服务和设备上执行。它可能是位于不断增长的平台之上的功能，也可能是必须适应不断增长的功能基础的平台。所有这些都应该被视为依赖关系，我们也应该致力于“持续集成”它们的变化。更复杂的是，这些变化的组件通常由我们团队、组织或公司之外的开发人员拥有，并按照他们自己的时间表部署。

因此，在当今世界，特别是在大规模开发时，对CI更好的定义也许是以下几点：

从测试的角度对CI进行思考是很自然的，因为两者紧密结合，我们将在本章中这样做。在前面的章节中，我们讨论了一系列全面的测试，从单元到集成，再到更大范围的系统。

从测试的角度来看，CI是一种范式，可以告知以下内容：

例如，我们在预提交上运行哪些测试，在提交后保存哪些测试，哪些甚至要保存到我们的临时部署？因此，我们如何在这些点上表示SUT？正如你所想象的，预提交SUT的需求可能与测试中的部署环境的需求有很大的不同。例如，从预提交的待审代码构建的应用程序与真正的生产后端对话可能是危险的（考虑安全和配额漏洞），而这对于临时环境来说通常是可以接受的。

为什么我们要尝试用 CI 来优化在 "正确的时间 "测试 "正确的事情 "的这种往往很微妙的平衡？这些结果是由一个强有力的保证所驱动的：可验证的、及时的、可证明应用程序可以进入下一阶段的证明。我们不需要仅仅希望所有的贡献者都非常谨慎、负责和闭环；相反，我们可以保证我们的应用程序在从构建到发布的各个阶段的工作状态，从而提高对我们产品的信心和质量以及我们团队的生产力。

在本章的其余部分中，我们将介绍一些关键CI概念、最佳实践和挑战，然后介绍我们如何在Google管理CI，并介绍我们的持续构建工具TAP，以及对某个应用程序的CI转换的深入研究。

首先，让我们先看看CI的一些核心概念。

正如第11章所讨论的，一个bug被捕获的时间越晚，其成本几乎呈指数增长。图23-1显示了有问题的代码更改在其生命周期中可能出现的所有位置。

一般来说，随着问题向我们图中的 "右侧 "发展，它们的成本会变得更高，原因如下：

为了使bug的代价最小化，CI鼓励我们使用*快速反馈环*。每次我们将代码（或其他）变化集成到测试场景中并观察结果时，我们就会得到一个新的*反馈回路*。反馈可以有很多形式；下面是一些常见的形式（按从快到慢的顺序）:

同样重要的是，来自CI的反馈可以被广泛获取。除了我们围绕代码可见性的开放文化之外，我们对我们的测试报告也有类似的方式。我们有一个统一的测试报告系统，任何人都可以很容易地查看构建或测试运行，包括所有的日志（不包括用户的个人身份信息[PII]），无论是个人工程师的本地运行还是自动化开发或分段构建。

除了日志，我们的测试报告系统还提供了构建或测试目标开始失败的详细历史记录，包括每次运行时在何处剪切构建、在何处运行以及由谁执行的审计日志。我们还有一个薄片分类系统，该系统使用统计数据在谷歌范围内对薄片进行分类，因此工程师不需要自己来确定他们的更改是否破坏了另一个项目的测试（如果测试是薄片：可能不是）。

对测试历史的可视性使工程师能够就反馈进行共享和协作，这是不同团队诊断和学习系统间集成故障的基本要求。类似地，谷歌的bug（如罚单或问题）是开放的，有完整的评论历史供所有人查看和学习（客户PII除外）。

最后，来自CI测试的任何反馈不仅应该是可访问的，而且应该是可操作的--易于用来发现和修复问题。我们将在本章后面的案例研究中看一个改进用户不友好反馈的例子。通过改善测试输出的可读性，你可以自动理解反馈。

众所周知，从长远来看，开发相关任务的自动化可以节省工程资源。直观地说，因为我们通过将流程定义为代码来实现自动化，所以在修改时的同行评审将减少错误的概率。当然，自动化流程，像其他软件一样，会有错误；但如果有效地实施，它们仍然比工程师手动尝试更快，更容易，更可靠。

特别是CI，它使*构建*和*发布*过程自动化，有持续构建和持续交付。持续测试贯穿始终，我们将在下一节中介绍。

持续构建（CB）集成了最新的代码修改，并运行自动构建和测试。因为CB在运行测试的同时也在构建代码，"破坏构建 "或 "构建失败 "包括破坏测试和破坏编译。

提交变更后，CB应运行所有相关测试。如果更改通过了所有测试，CB会将其标记为通过或绿色”，因为它通常显示在用户界面（UI）中。该流程有效地在报告中引入了两种不同版本的head：真实head或已提交的最新变更，以及绿色head或CB已验证的最新变更。工程师可以在本地开发中同步到任一版本。在编写变更代码时，通常会与绿色head同步，以便在稳定的环境中工作，并经CB验证，但有一个流程要求在提交变更之前将变更同步到真实head。

持续交付（CD；在第24章中详细讨论）的第一步是发布自动化，它不断地将最新的代码和配置从head组装成候选发布版本。在谷歌，大多数团队都是在绿色（而不是真正的）head进行切割。

请注意，我们在候选版本中包含了配置--这一点极为重要，尽管在候选版本的推广过程中，不同环境下的配置会略有不同。我们不一定提倡你把配置编译到你的二进制文件中--事实上，我们建议在许多情况下使用动态配置，如实验或特征标志。

相反，我们的意思是，您所拥有的任何静态配置都应该作为候选版本的一部分进行升级，以便它可以与其对应的代码一起接受测试。记住，很大比例的生产错误是由 "愚蠢的 "配置问题引起的，所以测试你的配置和测试你的代码一样重要（而且要和将要使用它的相同代码一起测试）。在这个发布--候选--推广的过程中，经常会出现版本倾斜。当然，这是假设你的静态配置是在版本控制中的--在谷歌，静态配置是和代码一起在版本控制中的，因此要经过同样的代码审查过程。

那么我们对CD的定义如下:

升级和部署过程通常取决于团队。我们将展示我们的案例研究如何引导这一过程。

对于谷歌的团队来说，他们希望从生产中的新变化（例如，持续部署）中获得持续的反馈，通常不可能持续地将整个二进制文件（通常相当大）推到绿色上。因此，通过实验或特性标志进行选择性连续部署是一种常见的策略。

当一个RC在各种环境中发展，它的构建（如二进制文件、容器）最好不要被重新编译或重建。使用像Docker这样的容器有助于在不同的环境中强制执行RC的一致性，从本地开发开始。同样，使用像Kubernetes这样的协调工具（或者在我们的例子中，通常是[Borg](https://oreil.ly/89yPv)），有助于强制执行部署之间的一致性。通过强制执行在不同环境间的发布和部署的一致性，我们实现了更高的保真度、更早的测试和更少的生产意外。

让我们来看看，当我们将持续测试（CT）应用于代码变更的整个生命周期时，CB和CD是如何配合的，如图23-2所示。

向右箭头显示单个代码更改从本地开发到生产的过程。同样，我我们在CI中的一个关键目标是确定在这个过程中*什么时候*测试什么。在本章后面，我们将介绍不同的测试阶段，并就提交前与提交后以及RC和其他阶段测试内容的注意事项。我们将展示，当我们向右移动时，代码更改将受到范围越来越大的自动化测试的影响。

为了尽快发现有问题的更改，并且能够在预提交上运行自动测试，你可能会想：为什么不在预提交时运行所有测试？

主要原因是它成本太高了。工程师的工作效率是非常宝贵的，在提交代码期间等待很长时间运行每个测试可能会严重降低生产力。此外，通过取消对预提交的限制，如果测试通过的频率远远高于失败的频率，就可以获得大量的效率提升。例如，运行的测试可以限制在特定范围内，或者根据预测其检测故障可能性的模型进行选择。

同样，如果工程师在提交前被与他们的代码修改无关的不稳定或软弱性引起的故障所阻挡，代价也很高。

另一个原因是，在我们运行预提交测试以确认更改是安全的过程中，底层存储库可能以与正在测试的更改不兼容的方式进行了更改。也就是说，两个涉及完全不同文件的更改可能会导致测试失败。我们称这种情况为空中碰撞，虽然一般来说很少发生，但大多数情况下都会发生在我们的掌控范围内。用于较小存储库或项目的CI系统可以通过序列化提交来避免此问题，以便在即将输入的内容和刚刚输入的内容之间没有区别。

那么，哪些测试*应该*在预提交时运行？我们的一般经验法则是：只有快速、可靠的测试。你可以接受在预提交时有一些覆盖面的损失，但这意味着你需要在提交后抓住任何漏掉的问题，并接受一定的回滚的次数。在提交后，你可以接受更长的时间和一些不稳定性，只要你有适当的机制来处理它。

我们不想因为等待太长时间的缓慢测试或太多测试而浪费宝贵的工程师生产力--我们通常将预提交的测试限制在发生变化的项目上。我们还同时运行测试，所以也要考虑资源决定。最后，我们不希望在预提交时运行不可靠的测试，因为让许多工程师受其影响，调试与他们的代码变更无关的同一个问题的成本太高。

谷歌的大多数团队都在预提交上运行他们的小型测试（如单元测试）--这些是明显要运行的，因为它们往往是最快和最可靠的。是否以及如何在提交前运行更大范围的测试是个更有趣的问题，这因团队而异。对于想要运行这些测试的团队来说，封闭测试是一种行之有效的方法来减少其固有的不稳定性。另一个选择是允许大范围的测试在预提交时不可靠，但当它们开始失败时，要主动禁用它们。

在代码修改通过CB（如果有失败的话，这可能需要多个周期）后，它很快再进行CD，并被纳入待发布的候选版本。

在CD构建RC的过程中，它将针对整个候选版本运行更大范围测试。我们通过一系列的测试环境来测试候选发布版，并在每次部署时对其进行测试。这可能包括沙盒、临时环境和共享测试环境的组合，如开发或临时。通常也包括在共享环境中对RC的一些手动QA测试。

有几个原因可以说明为什么对RC运行一个全面的、自动化的测试套件很重要，即使它是CB在提交后对代码运行的同一个套件（假设CD是绿色的）:

我们的持续、自动化测试过程一直持续到最后的部署环境：生产环境。我们应该对生产环境运行相同的测试套件（有时称为*probers*），就像我们早期对候选发布版所做的那样，以验证：1）根据我们的测试，生产环境的工作状态；2）根据生产环境，我们测试的相关性。

在应用程序进展的每一步进行持续测试，每一步都有其自身的权衡，这提醒了 "深度防御 "方法在捕捉错误方面的价值--我们依靠的不仅仅是一种技术或策略来保证质量和稳定性，还有多种测试方法的结合。

与负责任地运行生产系统一样，可持续地维护软件系统也需要持续的自动监控。正如我们使用监控和告警系统来了解生产系统对变化的反应一样，CI揭示了我们的软件是如何对其环境的变化做出反应的。生产监控依赖于运行系统的被动告警和主动探测，而CI则使用单元和集成测试来检测软件在部署前的变化。在这两个领域之间进行比较，可以让我们把一个领域的知识应用到另一个领域。

一个管理良好的告警系统有助于确保你的服务水平目标（SLO）得到满足。一个好的CI系统有助于确保你的构建处于良好状态--代码编译，测试通过，如果需要的话，你可以部署一个新版本。这两个领域的最佳实践策略都非常注重仿真度和可操作的告警：测试应该只在重要的基础不变因素被违反时才失败，而不是因为测试很脆弱或不稳定。一个脆弱的测试，每运行几次CI就会失败，就像一个虚假的警报每隔几分钟就会响起，并为值班人员生成一个页面一样，是一个问题。如果它不具有可操作性，就不应该发出警报。如果它实际上没有违反SUT的不变性，就不应该是测试失败。

同样，我们在这两个领域的故障模式中看到了一种潜在的联系。脆弱的基于原因的告警基于超过任意阈值（例如，过去一小时内的重试）而启动，而该阈值与终端用户看到的系统健康状况之间不一定有根本联系。当一个任意的测试要求或不变量被违反时，脆性测试就会失败，而不一定在该不变量和被测软件的正确性之间有根本的联系。在大多数情况下，这些测试很容易写，并有可能有助于调试更大的问题。在这两种情况下，它们都是整体健康/正确性的粗略代理，无法捕获整体行为。如果你没有一个简单的端到端探针，但你确实可以轻松地收集一些聚合统计信息，那么团队将基于任意统计信息编写阈值警报。如果你没有一个高层次的方法说："如果解码后的图像与这个解码后的图像不大致相同，则测试失败"，团队就会建立测试，断言字节流是相同的。

基于原因的告警和脆性测试仍然有价值；它们只是在告警场景中不是识别潜在问题的理想方式。在实际发生故障的情况下，有更多的调试细节可以使用。当SRE正在调试一个故障时，有这样的信息是很有用的："一小时前，用户开始遇到更多的失败请求。大约在同一时间，重试的数量开始上升。让我们开始调查。" 同样地，脆弱的测试仍然可以提供额外的调试信息。"图像渲染管道开始吐出垃圾。其中一个单元测试表明，我们从JPEG压缩器那里得到了不同的字节。让我们开始调查吧。"

尽管监控和告警被认为是SRE/生产管理领域的一部分，其中 "错误成本 "的洞察力被很好地理解，CI来自一个仍然倾向于关注绝对性的视角。将CI定义为告警的 "左移"，开始建议如何推理这些策略并提出更好的最佳实践：

这种 "CI就是警报 "的见解是新的，我们仍在摸索如何充分地得出相似之处。鉴于所涉及的风险较高，SRE对围绕监控和警报的最佳实践进行了大量的思考，而CI则被视为一种奢侈的功能，这一点并不奇怪。在未来几年，软件工程的任务将是看看现有的SRE实践可以在CI背景下重新概念化，以帮助重新制定测试和CI景观，也许测试的最佳实践可以帮助澄清监控和警报的目标和策略。

我们已经讨论了CI的一些已确认的最佳实践，并介绍了其中的一些挑战，例如不稳定的、缓慢的、冲突的或仅仅是在预提交时太多的测试对工程师生产力的潜在干扰。实施CI时，一些常见的额外挑战包括以下内容：

还有一个挑战是*失败管理*--当测试失败时该怎么做。尽管较小的问题通常可以很快得到解决，但我们的许多团队发现，当涉及到大型的端到端测试时，要有一个持续的绿色测试套件是非常困难的。它们本来就会出现故障或不稳定，而且难以调试；需要有一种机制来暂时禁用并跟踪它们，以便发布工作能够继续进行。在谷歌，一种常见的技术是使用由值班或发布工程师提交的bug "热名单"，并将其分发给相应的团队。如果这些bug能够自动生成并归档，那就更好了--我们的一些大型产品，如谷歌网络服务器（GWS）和谷歌助手，就能做到这一点。应对这些热名单进行整理，以确保立即修复所有阻止发布的bug。非发布障碍也应该被修复；它们不那么紧急，但也应该被优先处理，这样测试套件才会保持有用，而不仅仅是一堆越来越多的失效的旧测试。通常，由端到端测试失败引起的问题实际上是测试问题，而不是代码问题。

不稳定测试给这个过程带来了另一个问题。它们会侵蚀信心，就像一次失败的测试一样，但找到一个可以回滚的变化往往更困难，因为失败不会一直发生。一些团队依靠一种工具，在调查和修复不稳定的测试时，暂时从预提交中删除这种不稳定测试。这样可以保持较高的信心，同时允许有更多的时间来修复这个问题。

另一种有助于解决测试不稳定性（和其他CI挑战）的方法是封闭测试，我们将在下一节中讨论。

因为与实时后端交互是不可靠的，我们经常使用[封闭后端](https://oreil.ly/-PbRM)进行较大范围的测试。当我们想在提交前运行这些测试时，这是特别有用的，因为此时稳定性是最重要的。在第11章中，我们介绍了封闭测试的概念。

封闭测试有两个重要的特性：更高的确定性（即稳定性）和隔离性。封闭式服务器仍然容易受到一些非确定性来源的影响，如系统时间、随机数生成和竞态条件。但是，进入测试的内容不会因为外部的依赖关系而改变，所以当你用相同的应用程序和测试代码运行两次测试时，你应该得到相同的结果。如果一个封闭测试失败了，你就知道是由于你的应用程序代码或测试的变化造成的（有一个小的注意事项：它们也可能由于你的封闭测试环境的重组而失败，但这不应该经常更变）。出于这个原因，当CI系统在几小时或几天后重新运行测试以提供额外的信号时，封闭性使测试失败更容易缩小范围。

另一个重要特性，隔离，意味着生产环境中的问题不应该影响这些测试。我们通常也在同一台机器上运行这些测试，因此我们不必担心网络连接问题。反之亦然：运行封闭测试引起的问题不应影响生产环境。

封闭测试的成功不应取决于运行测试的用户。这允许人们复制CI系统运行的测试，并允许人们（例如，库的开发者）运行其他团队拥有的测试。

一种封闭式的后端是模拟的。正如在第13章中所讨论的，这些可能比运行一个真正的后端更廉价，但它们需要花费精力去维护，而且仿真度有限。

实现具有预提交价值的集成测试的最干净的选择是使用一个完全精细的设置--即启动整个堆栈沙盒--谷歌为流行组件（如数据库）提供开箱即用的沙盒配置，以使其更简单。这对于组件较少的小型应用程序更为可行，但谷歌也有例外，即使是一个（由DisplayAds提供）在每次提交前以及提交后从零开始启动大约400台服务器的应用程序。但是，自创建该系统以来，录制/重播已成为大型系统的一种更受欢迎的范例，并且往往比启动大型沙盒堆栈更便宜。

记录/重放（见第14章）系统记录实时的后端响应，缓存它们，并在一个封闭的测试环境中重放它们。记录/重放是一个强大的工具，可以减少测试的不稳定性，但一个缺点是它会导致测试变脆弱：很难在以下方面取得平衡：

理想情况下，记录/重放系统应该只检测有问题的更改，并且只有在请求以有意义的方式更改时才检测缓存未命中。如果该更改导致问题，代码修改者会用更新的响应重新运行测试，查看测试是否仍然失败，并因此收到问题警报。在实践中，在一个大型且不断变化的系统中，知道请求何时以有意义的方式发生了更改可能非常困难。

谷歌助手为工程师提供了一个运行端到端测试的框架，包括一个具有设置查询功能的测试套件，指定是否在手机或智能家居设备上进行模拟，并在与谷歌助手的整个交互中验证响应。

其最大的成功故事之一是使其测试套件在提交前完全密封。当该团队以前在提交前运行非封闭测试时，测试经常会失败。在某些日子里，团队会看到超过50个代码更改绕过并忽略测试结果。在将预提交转为封闭的过程中，该团队将运行时间缩短了14倍，而且几乎没有任何闪失。它仍然会出现故障，但这些故障往往是相当容易发现和回滚的。

现在，非封闭测试已经被推到提交后，结果反而导致失败在那里累积。调试失败的端到端测试仍然很困难，一些团队甚至没有时间尝试，所以他们只是禁用它们。这比让它停止所有人的开发要好，但它可能导致生产失败。

该团队目前的挑战之一是继续微调其缓存机制，以便预提交可以捕捉到更多过去只在提交后发现的问题类型，同时不引入过多的脆弱性。

另一个问题是，鉴于组件正在转移到自己的微服务中，如何为分散的助手做预提交测试。因为助手有一个庞大而复杂的堆栈，在预提交上运行一个封闭的堆栈，在工程工作、协调和资源方面的成本会非常高。

最后，该团队正在利用这种分散的优势，采取一种巧妙的新的提交后故障隔离策略。对于助手中的N个微服务中的每一个，团队将运行一个提交后的环境，其中包含在头部构建的微服务，以及其他N-1个服务的生产（或接近生产）版本，以将问题隔离到新构建的服务器。这种设置通常是O(N2)的成本，但该团队利用了一个很酷的功能，称为热交换，将这一成本削减到O(N)。从本质上讲，"热交换 "允许一个请求指示服务器 "交换 "一个后端地址来调用，而不是通常的一个。因此，只需要运行N个服务器，每个微服务都有一个，而且它们可以重复使用同一组被交换到这N个 "环境 "中的生产环境后端。

正如我们在本节中所看到的，封闭测试既可以减少大范围测试中的不稳定性，也可以帮助隔离故障，解决我们在上一节中确定的两个重大CI挑战。然而，封闭式后端也可能更昂贵，因为它们使用更多的资源，并且设置速度较慢。许多团队在他们的测试环境中使用密封和活动后端的组合。

现在让我们更详细地看看CI在谷歌是如何实施的。首先，我们将了解谷歌绝大多数团队使用的全球持续构建TAP，以及它是如何实现一些实践和解决我们在上一节中看到的一些挑战的。我们还将介绍一个应用程序Google Takeout，以及CI转换如何帮助其作为平台和服务进行扩展。

我们在整个代码库中运行一个大规模的持续构建，称为测试自动化平台（TAP）。它负责运行我们大部分的自动化测试。由于我们使用的是monorepo，TAP是谷歌几乎所有变化的门户。每天，它负责处理超过50,000个独特的变化，运行超过40亿个单独的测试用例。

为了快速和持续地发现问题，必须确保对每一个变化都进行测试。如果没有CB，运行测试通常是由个别工程师决定的，这往往会导致一些有积极性的工程师试图运行所有的测试并跟进故障。

如前所述，等待很长时间来运行预提交的每个测试可能会造成严重破坏，在某些情况下需要数小时。为了最大限度地减少等待时间，谷歌的CB方法允许潜在的破坏性更改提交到存储库中（请记住，这些更改会立即被公司其他人看到！）。我们只要求每个团队创建一个快速的测试子集，通常是一个项目的单元测试，可以在提交更改之前（通常是在发送更改进行代码审查之前）运行这些测试。根据经验，通过预提交的变更通过其余测试的可能性非常高（95%+），我们乐观地允许将其集成，以便其他工程师可以开始使用它。

提交更改后，我们使用TAP异步运行所有可能受影响的测试，包括较大和较慢的测试。

当变更导致TAP测试失败时，必须迅速修复变更，以防止阻塞其他工程师。我们已经建立了一种文化规范，强烈反对在已知失败测试的基础上进行任何新的工作，尽管不稳定测试会让这变得困难。因此，当提交的变更打破了团队的内置TAP时，该变更可能会阻止团队向前推进或构建新版本。因此，快速处理故障势在必行。

为了处理这种破坏，每个团队都有一个 "Build Cop"。Build Cop的责任是保持他们特定项目的所有测试通过，无论谁破坏了它们。当Build Cop被告知他们的项目中有一个失败的测试时，他们会放下手中的工作，修复构建。这通常是通过识别违规的变化，并确定它是否需要回滚（首选解决方案）或可以继续修复（风险较大）。

在实践中，允许在验证所有测试之前提交更改的折衷方案已经真正得到了回报；提交更改的平均等待时间约为11分钟，通常在后台运行。再加上Build Cop的原则，我们能够以最小的中断量有效地检测和解决运行时间较长的测试检测到的故障。

谷歌大型测试套件面临的一个问题是找到破坏测试的具体变化。从概念上讲，这应该很容易：抓取一个变更，运行测试，如果任何测试失败，将变更标记为坏的。不幸的是，由于片断的流行以及测试基础设施本身偶尔出现的问题，要确信失败是真实的并不容易。更加复杂的是，TAP必须每天评估如此多的变化（一秒钟超过一个），以至于它不能再对每个变化运行每个测试。取而代之的是，它退回到批处理相关的更改，这减少了要运行的独特测试的总数。尽管这种方法可以加快运行测试的速度，但它可以掩盖批处理中导致测试中断的更改。

为了加快故障识别，我们使用了两种不同的方法。首先，TAP自动将失败的批次拆分为单独的更改，并针对每个更改单独重新运行测试。这个过程有时需要一段时间才能收敛到失败，因此，我们还创建了罪魁祸首查找工具，每个开发人员可以使用这些工具通过一批更改进行二进制搜索，并确定哪一个是可能的罪魁祸首。

在隔离破坏性变更后，尽快修复该变更非常重要。失败测试的存在可能会很快开始侵蚀测试套件的信心。如前所述，修复损坏的构建是Build Cop的责任。Build Cop最有效的工具是*回滚*。

回滚更改通常是修复生成的最快和最安全的方法，因为它可以快速将系统恢复到已知的良好状态。事实上，TAP最近已升级为自动回滚更改，当它高度确信更改是罪魁祸首时。

快速回滚与测试套件携手并进，以确保持续的生产力。测试给了我们改变的信心，回滚给了我们撤销的信心。没有测试，回滚就不能安全进行。没有回滚，破损的测试就不能被快速修复，从而降低了对系统的信心。

虽然工程师可以在本地运行测试，但大多数测试的执行是在一个叫做*Forge*的分布式构建和测试系统中进行。Forge允许工程师在我们的数据中心运行他们的构建和测试，这最大限度地提高了并行性。在我们的规模下，运行所有由工程师按需执行的测试以及作为CB流程一部分运行的所有测试所需的资源是巨大的。即使考虑到我们拥有的计算资源量，像Forge和TAP这样的系统也受到资源限制。为了解决这些限制，在TAP上工作的工程师想出了一些聪明的方法来确定哪些测试应该在什么时候运行，以确保花费最少的资源来验证一个特定的变化。

确定需要运行哪些测试的主要机制是分析每个更改的下游依赖关系图。谷歌的分布式构建工具Forge和Blaze维护了一个近乎实时的全球依赖关系图版本，并可供用户使用。因此，TAP可以快速确定任何更改的下游测试，并运行最小集以确保更改是安全的。

影响TAP使用的另一个因素是测试运行的速度。TAP通常能够以更少的测试比更多测试更快地运行更改。这种情况鼓励工程师编写小而集中的更改。在繁忙的一天中，触发100个测试的更改和触发1000个测试的更改之间的等待时间差异可能是几十分钟。希望花更少时间等待的工程师最终会做出更小的、有针对性的修改，这对所有人来说都是一种胜利。 

团队决定将每个新的API部署为一个定制的实例，使用相同的原始Takeout二进制文件，但将它们配置成有点不同的工作方式。例如，用于Drive批量下载的环境拥有最大的集群，为从Drive API获取文件保留了最多的配额，以及一些自定义的认证逻辑，允许未登录的用户下载公共文件夹。

不久，Takeout就面临“标志问题”。为其中一个实例添加的标志将破坏其他实例，当服务器由于配置不兼容而无法启动时，它们的部署将中断。除了功能配置之外，还有安全性和ACL配置。例如，消费者驱动器下载服务不应访问加密企业Gmail导出的密钥。配置很快变得复杂，几乎每晚都会发生故障。

我们做了一些努力来分解和模块化配置，但这暴露出的更大的问题是，当Takeout工程师想要修改代码时，手动测试每台服务器是否在每种配置下启动是不切实际的。他们在第二天的部署中才发现配置失败的情况。有一些单元测试是在提交前和提交后运行的（通过TAP），但这些测试不足以捕获此类问题。

尽管这些新的沙盒式预提交测试大大减少了部署失败，但它们并没有完全消除它们。特别是，Takeout的端到端测试仍然经常中断部署，而且这些测试很难在预提交中运行（因为它们使用的是测试账户，在某些方面仍然与真实账户一样，并受到同样的安全和隐私保护）。重新设计它们以使其对预提交友好，将是一项巨大的工程。

如果团队不能在预提交中运行端到端测试，那么它什么时候可以运行？它想比第二天的开发部署更快得到端到端的测试结果，并决定每两小时一次是一个好的起点。但团队并不想这么频繁地进行全面的开发部署--这将产生开销，并扰乱工程师在开发中测试的长期运行的流程。为这些测试建立一个新的共享测试环境，似乎也需要太多的开销来提供资源，再加上查找问题（即找到导致失败的部署）可能涉及一些不可预知的手动工作。

因此，该团队重新使用了预提交的沙盒环境，轻松地将它们扩展到新的后提交环境。与预提交不同，后提交符合安全保障措施，可以使用测试账户（其一，因为代码已经被批准），所以端到端的测试可以在那里运行。提交后的CI每两小时运行一次，从绿头抓取最新的代码和配置，创建一个RC，并针对它运行已经在开发中运行的相同的端到端测试套件。

一个常见的失败原因是：当产品插件推出一个功能时，测试会中断。例如，YouTube插件的播放列表获取功能可能在开发阶段启用了几个月的测试，然后才在生产阶段启用。Takeout测试只知道要检查一个结果，所以这往往导致测试需要在特定的环境中被禁用，并在功能推出时被手动修复。

对于推广问题，团队增加了插件工程师指定功能标志名称或代码更改ID的功能，该功能使特定功能和输出能够同时使用和不使用该功能。测试配备了查询测试环境的功能，以确定给定的功能是否在那里启用，并相应地验证预期输出。

当被禁用的测试的bug标签开始积累并且不被更新时，该团队将其清理自动化。测试现在会通过查询我们的错误系统的API来检查一个错误是否被关闭。如果一个被标记为失败的测试实际通过了，并且通过的时间超过了配置的时间限制，测试就会提示清理标签（如果还没有被修复的话，就标记为bug修复）。这个策略有一个例外：不稳定的测试。对于这些，团队将允许测试被标记为不稳定，如果测试通过了，系统不会提示清理标记的 "不稳定 "故障。

你可能会想，这一切都很好，但你既没有时间也没有钱来建立这些。我们当然承认，谷歌可能比一般的创业公司拥有更多的资源来实施CI。然而，我们的许多产品成长得如此之快，以至于他们也没有时间去开发一个CI系统（至少不是一个合适的系统）。

在你自己的产品和组织中，试着想想你已经为在生产中发现和处理的问题支付了多少成本。这些问题当然会对最终用户或客户产生负面影响，但它们也会影响到团队。频繁的生产救火是一种压力和士气的体现。尽管建立CI系统是昂贵的，但它不一定是一个新的成本，而是将成本转移到一个更早的、更可取的阶段，减少问题的发生率，从而减少成本，因为问题发生在右边。CI带来了更稳定的产品和更快乐的开发运营文化，在这种文化中，工程师更相信“系统”会发现问题，他们可以更多地关注功能，而不是修复问题。

尽管我们已经描述了我们的 CI 流程和一些自动化的方法，但这并不是说我们已经开发了完美的 CI 系统。毕竟，CI系统本身只是一个软件，永远不会完整，应该进行调整以满足应用程序和工程师不断变化的需求。我们试图用Takeout的CI的演变和我们指出的未来改进领域来说明这一点。
