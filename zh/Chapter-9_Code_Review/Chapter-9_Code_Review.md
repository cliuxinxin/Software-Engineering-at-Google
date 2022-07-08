代码审查是一个由作者以外的人对代码进行审查的过程，通常在将该代码引入代码库之前。尽管这是一个简单的定义，但在整个软件行业中，代码审查过程的实施有很大不同。一些组织在代码库中拥有一组挑选出来的 "守门人 "来审查修改。其他人将代码审查过程委托给这个小团队，允许不同的团队要求不同级别的代码审查。在谷歌，基本上每一个改动在提交之前都会被审查，每个工程师都负责启动审查和审查变更。

代码审查通常需要一个流程和一个支持该流程的工具的组合。在Google，我们使用一个定制的代码审查工具Critique来支持我们的流程。 Critique在Google是一个非常重要的工具，足以让它在本书中占有一章。本章重点介绍Google实施的代码审查流程，而不是具体的工具，这是因为这些基础比工具更古老，而且这些见解大多可以适应你可能用于代码审查的任何工具。

代码审查的一些好处，例如在代码进入代码库之前检测到代码中的错误，已经得到了很好的证实，而且有点明显（如果测量不精确的话）。然而，其他的好处则更为微妙。由于谷歌的代码审查过程是如此的普遍和广泛，我们已经注意到了许多这些更微妙的影响，包括心理上的影响，随着时间的推移和规模的扩大，会给一个组织带来许多好处。

代码评审可以发生在软件开发的多个阶段。在谷歌，代码评审是在更改提交到代码库之前进行的；这个阶段也被称为*预提交审查*。代码评审的主要最终目标是让另一位工程师同意变更，我们通过将变更标记为“我觉得不错”（LGTM）来表示。我们将此LGTM用作必要的权限“标识”（与下面提到的其他标识结合使用），以允许提交更改。

谷歌的典型代码审查过程如下：

我们将在本章后面更详细地介绍这个过程。

重要的是要记住（并接受），编码本身就是一种责任。它可能是一种必要的责任，但就其本身而言，编码只是某个人的一项维护任务。就像飞机携带的燃料一样，它有重量，尽管它当然是[飞机飞行的必要条件](https://oreil.ly/TmoWX)。

当然，新特性通常是必需的，但在开发代码之前，首先要注意确保任何新特性都是有必要的。重复的代码不仅是一种浪费，而且实际上比根本没有代码要花费更多的时间；当代码库中存在重复时，可以在一个代码模式下轻松执行的更改通常需要更多的工作。编写全新的代码是如此令人不快，以至于我们中的一些人都有这样一句话：“如果你是从头开始写的，那你就是做错了！”

这对于类库或实用程序代码来说尤其如此。有可能，如果你正在写一个实用程序，那么在像Google那样大的代码库中，其他人可能已经做了类似的事情。因此，像那些在第17章中讨论的工具对于找到这些实用程序代码和防止引入重复的代码都是至关重要的。理想情况下，这种研究是事先完成的，在编写任何新的代码之前，任何新的设计都已经传达给合适的小组。

当然，新项目的发生，新技术的引入，新组件的需要，等等。综上所述，代码审查并不是一个重提或辩论以前的设计决策的时机。设计决策通常需要时间，需要分发设计方案，在API评审或类似的会议上对设计进行辩论，也许还需要开发原型。就像对全新的代码进行代码审查不应该突然出现一样，代码审查过程本身也不应该被看作是重新审视以前决策的时机。

我们已经粗略地指出了典型的代码审查过程是如何工作的，但问题在于细节。本节详细概述了谷歌的代码审查工作原理，以及这些实践如何使其能够随时间适当扩展。

对于Google的任何特定变化，有三个方面的审查需要 "批准"：  

虽然这种程度的控制听起来很繁琐——而且，不可否认，有时的确如此——但大多数审查都是由一个人承担这三个角色，这就大大加快了审查过程。重要的是，作者也可以承担后两个角色，只需要另一个工程师的LGTM就可以将代码检查到他们自己的代码库中，前提是他们已经具备了该语言的可读性（所有者经常这样做）。

这些要求使得代码审查过程非常灵活。技术负责人是一个项目的所有者，并且具有代码的语言可读性，可以只使用另一个工程师的LGTM提交代码更改。没有这种权限的实习生可以将相同的更改提交给相同的代码库，前提是他们获得具有语言可读性的所有者的批准。上述三个许可“标识”可以任意组合。作者甚至可以从不同的人处请求多个LGTM，方法是明确地将更改标记为希望从所有审阅者处获得LGTM。

在实践中，大多数需要不止一次批准的代码评审通常经历两个步骤：从同行工程师那里获得LGTM，然后从适当的代码所有者/可读性审查员处寻求批准。这使得这两个角色可以专注于代码审查的不同方面，并节省审查时间。主要审查员可以关注代码正确性和代码更改的一般有效性；在实践中，大多数需要一个以上批准的代码审查通常要经过两个步骤：从同行工程师那里获得LGTM，然后再从适当的代码所有者/可读性审查者那里寻求批准。这使得两个角色可以专注于代码审查的不同方面，并节省审查时间。主要的审查者可以专注于代码的正确性和代码修改的一般有效性；代码所有者可以关注此更改是否适合他们的部分，而不必关注每行代码的细节。换句话说，审批者所寻找的东西往往与同行评审者不同。毕竟，有人是想把代码签入他们的项目/目录。他们更关心的是诸如以下问题。"这段代码是容易还是难以维护？" "它是否增加了我的技术债务？" "我们的团队中是否有维护它的专业知识？"

如果这三种类型的审查都可以由一个审查员处理，为什么不直接让这些类型的审查员处理所有的代码审查呢？简单的答案是规模。将这三种角色分开，可以增加代码审查过程的灵活性。如果你和同行一起在一个实用程序库中开发一个新的函数，你可以让你团队中的某人来审查代码的正确性和理解性。经过几轮（也许是几天的时间），你的代码让你的同行审查员满意，你就会得到一个LGTM。现在，你只需要让该库的*所有者*（而所有者往往具有适当的可读性）批准这项修改。

当一个小团队在专门的版本库中工作时，通常会授予整个团队对版本库中所有内容的访问权。毕竟，你认识其他的工程师，这个领域很窄，你们每个人都可以成为专家，而且人数少限制了潜在错误的影响范围。

随着团队规模的扩大，这种方法可能无法扩展。其结果要么是混乱的版本库分裂，要么是用不同的方法来记录谁在版本库的不同部分拥有哪些知识和职责。在谷歌，我们把这套知识和职责称为*所有权*，把行使这些知识和职责的人称为*所有者*。这个概念不同于对源代码集合的占有，而是意味着一种管理意识，以公司的最佳利益对代码库的某个部分采取行动。(事实上，如果我们重新来过，"管家 "几乎肯定是一个更好的术语）。

特别命名的OWNERS文件列出了对一个目录及其子目录有所有权责任的人的用户名。这些文件也可能包含对其他OWNERS文件或外部访问控制列表的引用，但最终它们会解析为个人列表。每个子目录也可能包含一个单独的OWNERS文件，而且这种关系是分层递增的：一个给定的文件通常由目录树中它上面的所有OWNERS文件的成员共同拥有。OWNERS文件可以有任意多的条目，但我们鼓励一个相对较小和集中的列表，以确保责任明确。

对谷歌代码的所有权传达了对其权限范围内的代码的批准权，但这些权利也伴随着一系列的责任，如了解所拥有的代码或知道如何找到拥有这些代码的人。不同的团队在授予新成员所有权方面有不同的标准，但我们通常鼓励他们不要把所有权作为入职仪式，并鼓励离职的成员在可行的情况下尽快放弃所有权。

这种分布式所有权结构使我们在本书中概述的许多其他做法成为可能。例如，根OWNERS文件中的一组人可以作为大规模修改的全球批准者（见第22章），而不必打扰本地团队。同样，OWNERS文件作为一种文档，使人们和工具很容易找到对某段代码负责的人，只要沿着目录树走就可以了。当新项目被创建时，没有一个中央机构需要注册新的所有权权限：一个新的OWNERS文件就足够了。

这种所有权机制简单而强大，在过去的20年里得到了良好的扩展。这是谷歌确保数以万计的工程师能够在单一资源库中有效操作数十亿行代码的方法之一。

纵观整个行业，代码审查本身并不存在争议，尽管它还远不是一种普遍的做法。许多（甚至可能是大多数）其他公司和开源项目都有某种形式的代码审查，而且大多数人认为这个过程很重要，是对引入新代码到代码库的合理检查。软件工程师理解代码审查的一些更明显的好处，即使他们个人可能不认为它适用于所有情况。但在谷歌，这个过程通常比其他大多数公司更彻底、更广泛。

谷歌的文化，就像许多软件公司的文化一样，是基于给工程师们在工作中的自由度。人们认识到，对于需要对新技术做出快速反应的充满活力的公司来说，严格的流程往往不起作用，而官僚主义的规则往往不适合创造性专业人士。然而，代码审查是一项任务，是谷歌所有软件工程师都必须参与的少数全流程之一。谷歌要求对代码库的每一次代码修改都要进行代码审查，无论多么微小。这个任务确实对工程速度有成本和影响，因为它确实减缓了将新代码引入代码库的速度，并可能影响任何特定代码更改的生产时间。(这两点是软件工程师对严格的代码审查过程的常见抱怨）。那么，为什么我们要要求这个过程？为什么我们相信这是一个长期有利的？

一个精心设计的代码审查过程和认真对待代码审查的文化会带来以下好处：

随着时间的推移，这些好处对一个软件组织来说是至关重要的，其中许多好处不仅对作者有利，而且对审查员也有利。下面的章节将对这些项目中的每一项进行更详细的说明。

代码审查的一个明显的好处是，它允许审查者检查代码更改的 "正确性"。让另一双眼睛来审视一个更改，有助于确保这个更改能达到预期效果。审查员通常会检查一个变更是否有适当的测试，设计是否合理，功能是否正确和有效。在许多情况下，检查代码正确性就是检查特定的更改是否会将bug引入代码库。

许多报告指出了代码审查在防止软件未来出现错误方面的有效性。IBM的一项研究发现，在一个过程的越早发现缺陷，无疑会减少以后修复缺陷所需的时间。对代码审查时间的投入节省了原本用于测试、调试和执行回归的时间，前提是代码审查过程本身经过了优化，以保持其轻量级。如果代码审查过程很重，或者扩展不当，那么这些过程将变得不可持续。我们将在本章后面介绍一些保持过程轻量级的最佳实践。

为了防止正确性评估变得更加主观而非客观，作者通常会遵循其特定方法，无论是在设计中还是在引入变更的功能中。审查员不应该因为个人意见而提出替代方案。审查员可以提出替代方案，但前提是这些替代方案能够改善理解性（例如，通过降低复杂性）或功能性（例如，通过提高效率）。一般来说，鼓励工程师批准改进代码库的更改，而不是等待就更“完美”的解决方案达成共识。这种关注倾向于加速代码审查。

随着工具越来越强大，许多正确性检查会通过静态分析和自动测试等技术自动执行（尽管工具可能永远不会完全消除基于人工的代码检查的价值，更多信息请参见第20章）。尽管这种工具有其局限性，但它明确地说明了需要依靠基于人工的代码检查来检查代码的正确性。

这就是说，在最初的代码审查过程中检查缺陷仍然是一般“左移”策略的一个组成部分，旨在尽早发现和解决问题，从而避免在开发周期中进一步增加成本和资源。代码审查既不是万灵药，也不是检查这种正确性的唯一方法，但它是深入防御软件中此类问题的一个要素。因此，代码审查不需要“完美”才能取得成果。

令人惊讶的是，检查代码的正确性并不是谷歌从代码审查过程中获得的最大好处。检查代码正确性通常可以确保更改有效，但更重要的是确保代码更改是可以理解的，并且随着时间的推移和代码库本身的扩展而变得有意义。为了评估这些方面，我们需要查看除代码在逻辑上是否“正确”或理解之外的其他因素。

代码审查通常是作者以外的人检查修改的第一个时机。这种视角使审查者能够做到最好的工程师也做不到的事情：提供不受作者视角影响的反馈。*代码审查通常是对一个特定的变更是否能被更多的人理解的第一个测试*。这种观点是非常重要的，因为代码被阅读的次数要比它被写的次数多得多，而理解和领悟是非常重要的。

找到一个与作者观点不同的读者通常是很有用的，特别是一个审查员，作为他们工作的一部分，可能需要维护或使用修改中提出的代码。与审查员在设计决策方面应该给予作者的尊重不同，用 "客户永远是对的 "这一格言来对待代码理解方面的问题往往是有用的。在某种程度上，你现在得到的任何问题都会随着时间的推移而成倍增加，所以要把每个关于代码理解的问题看作是有效的。这并不意味着你需要改变你的方法或逻辑来回应批评，但这确实意味着你可能需要更清楚地解释它。

代码正确性和代码理解力的检查共同构成了另一个工程师的LGTM的主要标准，这也是一个被批准的代码审查所需的批准之一。当一个工程师将代码审查标记为LGTM时，他们是在说，代码做了它所说的事情，而且它是可以理解的。然而，谷歌也要求代码是可持续维护的，所以我们在某些情况下对代码还需要额外的批准。

在规模上，你写的代码会被别人依赖，并最终由其他人维护。许多人需要阅读你的代码并了解你的工作。在你转移到另一个项目之后很长时间后，其他人（包括自动化工具）可能需要重构你的代码。因此，代码需要符合一些一致性的标准，这样它才能被理解和维护。代码也应避免过于复杂；简单的代码对其他人来说也更容易理解和维护。审查员可以在代码评审中评估这些代码是否符合代码库本身的标准。因此，代码审查的作用应该是确保*代码的健康*。

正是为了可维护性，代码审查的LGTM状态（表示代码的正确性和理解力）与可读性批准的状态是分开的。可读性的批准只能由成功通过特定编程语言的代码可读性培训过程的人授予。例如，Java代码需要有 "Java可读性 "的工程师来批准。

可读性审查员的任务是审查代码，以确保它遵循该特定编程语言的商定的最佳做法，与谷歌代码库中该语言的代码库一致，并避免过于复杂。一致和简单的代码更容易理解，当需要重构时，工具也更容易更新，使其更有扩展性。如果一个特定的模式在代码库中总是以一种方式完成，那么写一个工具来重构它就会更容易。

此外，代码可能只编写一次，但它将被读取数十次、数百次甚至数千次。在整个代码库中使用一致的代码可以提高对所有工程的理解，这种一致性甚至会影响代码评审本身的过程。一致性有时与功能冲突；可读性审查员可能更喜欢不太复杂的更改，这些更改在功能上可能不是“更好”，但更容易理解。

有了一个更加一致的代码库，工程师就更容易介入并审查别人项目的代码。工程师们可能偶尔需要向团队外寻求代码审查方面的帮助。能够伸出援手，请专家来审查代码，知道他们可以期望代码本身是一致的，使这些工程师能够更正确地关注代码的正确性和理解。

代码审查还有重要的文化好处：它向软件工程师强调代码不是“他们的”，而是事实上集体事业的一部分。这种心理上的好处可能很微妙，但仍然很重要。没有代码审查，大多数工程师自然会倾向于个人风格和他们自己的软件设计方法。代码审查过程迫使作者不仅要让别人提出意见，而且要为了更大的利益做出妥协。

以自己的手艺为荣，不愿意公开自己的代码接受他人的批评，这是人类的天性。对于自己写的代码的批评性反馈，也很自然地不愿意接受。代码审查过程提供了一个机制，以减轻可能是一个情绪化的互动。如果代码审查效果最佳，它不仅会对工程师的假设提出质疑，而且还会以规定的、中立的方式提出质疑，如果以未经请求的方式提出批评，则可能会对作者提出批评。毕竟，这个过程需要批判性的审查（事实上，我们把我们的代码审查工具称为 "批判"），所以你不能责怪审查者做他们的工作和批评。因此，代码审查过程本身可以充当 "坏警察"，而审查者仍然可以被看作是 "好警察"。

当然，不是所有的，甚至是大多数的工程师都需要这样的心理措施。但是，通过代码审查的过程来缓解这种批评，往往能为大多数工程师提供一个更温和的指导，让他们了解团队的期望。许多加入谷歌的工程师，或者一个新的团队，都被代码审查所吓倒。我们很容易认为任何形式的批评性审查都会对一个人的工作表现产生负面影响。但是，随着时间的推移，几乎所有的工程师都期望在发送代码审查时受到挑战，并开始重视通过这一过程提供的建议和问题（尽管，无可否认，这有时需要一段时间）。

代码审查的另一个心理好处是验证。即使是最有能力的工程师也可能患上冒名顶替综合症，过于自我批评。像代码评审这样的过程是对一个人工作的确认和认可。通常，该过程涉及思想交流和知识共享（将在下一节中介绍），这对审核人和被审核人都有好处。随着工程师领域知识的增长，他们有时很难获得关于如何改进的积极反馈。代码审查过程可以提供这种机制。

启动代码审查的过程也迫使所有作者对他们的更改多加注意。许多软件工程师并不是完美主义者；大多数人都会承认，"能完成工作 "的代码要比完美但开发时间太长的代码要好。我们中的许多人会抄近路是很自然的，即使我们完全打算在以后纠正这些缺陷。"当然，我没有完成所有的单元测试，但我可以以后再做。" 代码审查迫使工程师在发送修改前解决这些问题。从心理上讲，为代码审查而收集修改的组成部分，迫使工程师确保他们所有的事情都是一帆风顺的。在发送修改前的那一小段思考时间是阅读修改的最佳时机，以确保你没有遗漏任何东西。

代码审查的一个最重要的，但被低估的好处是知识共享。大多数作者挑选的审查员都是被审查领域的专家，或者至少在所审查的领域有知识。审查过程允许审查员向作者传授领域知识，允许审查员向作者提供建议、新技术或咨询信息。(审查员甚至可以把一些评论标记为 "仅供参考"，不需要采取任何行动；它们只是作为作者的一种帮助而被添加进来）。在代码库某个领域特别精通的作者通常也会成为所有者，而后者又可以作为其他工程师的评审员。

反馈和确认的代码评审过程的一部分包括询问为什么以特定方式进行更改。这种信息交流有助于知识共享。事实上，许多代码评审都涉及双向信息交换：作者和审查员都可以从代码评审中学习新的技术和模式。在谷歌，审查员甚至可以直接在代码审查工具中与作者分享建议的编辑。

工程师可能不会阅读每一封发给他们的电子邮件，但他们往往会回复每一封发送的代码审查。这种知识共享也可以跨越时区和项目，利用谷歌的规模将信息迅速传播到代码库的各个角落的工程师。代码审查是知识转移的最佳时机：它是及时和可操作的。(谷歌的许多工程师首先通过代码审查 "认识 "其他工程师！）。

考虑到谷歌工程师花在代码审查上的时间，积累的知识相当重要。当然，谷歌工程师的主要任务仍然是编程，但他们的大部分时间仍然花在代码审查上。代码评审过程提供了软件工程师相互交流和交换编码技术信息的主要方式之一。通常，新模式是在代码审查的上下文中发布的，有时是通过重构（如大规模更改）发布的。

此外，由于每个更改都成为代码库的一部分，所以代码评审充当历史记录。任何工程师都可以检查谷歌的代码库，并确定何时引入某些特定的模式，并提出有关的实际代码审查。通常情况下，这种考古学为比原作者和审查者更多的工程师提供了洞察力。

诚然，代码审查会给一个组织带来阻力和延迟。这些问题大多不是代码审查本身的问题，而是他们选择的代码审查实施的问题。在谷歌保持代码审查过程的顺利运行也不例外，它需要大量的最佳实践来确保代码审查是值得的。大多数实践都强调保持流程的敏捷性和快速性，以便代码评审能够适当地扩展。

正如本书文化部分所指出的，谷歌大力培育信任和尊重的文化。这将深入到我们对代码审查的观点中。例如，软件工程师只需要一个来自其他工程师的LGTM就可以满足我们对代码理解的要求。许多工程师在作出更改后提出意见和LGTM，并理解这些更改可以在做出更改后提交，而无需进行任何额外的审查。也就是说，即使是最有能力的工程师，代码审查也会带来焦虑和压力。在专业领域中，坚定地保留所有反馈和批评是至关重要的。

一般来说，审查员应该在特定的方法上听从作者的意见，只有在作者的方法有缺陷时才指出替代方法。如果作者能证明几种方法同样有效，审查员应该接受作者的偏好。即使在这些情况下，如果发现一个方法有缺陷，也要把审查看作是一个学习的机会（对双方都是如此！）。所有的评论都应该严格保持专业性。审查员应该注意不要根据代码作者的特定方法就下结论。在假设该方法是错误的之前，最好先问一下为什么要这样做。

审查员应及时提供反馈。在谷歌，我们希望在24（工作）小时内得到代码审查的反馈。如果审查员无法在这段时间内完成审查，那么良好的做法是（也是我们所期望的）回应说他们至少已经看到了更改，并将尽快进行审查。审查员应该避免以零散的方式回应代码评审。没有什么事情比从审查中得到反馈，解决它，然后继续在审查过程中得到无关的进一步反馈更让作者恼火。

就像我们期望审查员有专业精神一样，我们也期望作者有专业精神。记住，你不是你的代码，你提出的这个修改不是 "你的"，而是团队的。在你把这段代码检查到代码库中后，它无论如何都不再是你的了。要乐于接受关于你的方法的问题，并准备好解释你为什么以某种方式做事情。记住，作者的部分责任是确保这段代码是可以理解的，并且可以为将来维护。

重要的是要把代码审查中的每个审查者的评论当作一个TODO项目；一个特定的评论可能不需要被无条件接受，但它至少应该被解决。如果你不同意一个评审员的评论，让他们知道，并让他们知道为什么，在双方都有机会提供替代方案之前，不要把评论标记为已解决。如果作者不同意审查员的意见，保持这种辩论的一个常见方法是提供一个替代方案，并要求评审员PTAL（请再看看）。记住，代码审查对于审查者和作者来说都是一个学习的机会。这种洞察力往往有助于减少任何分歧的场景。

同样的道理，如果你是代码的所有者，并且在你的代码库中对代码审查做出回应，那么就应该对来自外部作者的改动持宽容态度。只要这个改动是对代码库的改进，你就应该尊重作者的意见，即更改表明了一些可以而且应该改进的地方。

要保持代码审查过程的灵活性，最重要的做法可能是保持小的更改。理想情况下，代码审查应该是容易理解的，并且对审查者和作者来说，都是集中在一个问题上。谷歌的代码审查过程不鼓励由完全成型的项目组成的大规模修改，审查员可以理所当然地拒绝这样的更改，因为它对于一次审查来说太大。较小的改动也可以防止工程师浪费时间等待对较大变更的审查，减少停滞时间。这些小更改在软件开发过程中也有好处。如果一个特定的变更足够小，那么确定该变更中的错误来源就容易得多。

尽管如此，必须承认，依赖于小更改的代码审查过程有时很难与主要新特性的引入相协调。一组小的、渐进式的代码修改可能更容易单独消化，但在一个更大的方案中却更难理解。不可否认，谷歌的一些工程师并不喜欢小改动。存在管理这种代码变化的技术（在集成分支上开发，使用不同于HEAD的diff base管理变化），但这些技术不可避免地涉及更多的开销。考虑到对小改动的优化只是一个优化，并允许你的过程适应偶尔的大更改。

因为代码审查通常是小规模的，所以在谷歌，几乎所有的代码审查都是由一个人审查，而且只有一个人。如果不是这样的话——如果一个团队被期望对一个共同的代码库的所有更改进行评估，那么这个过程本身就没有办法扩展。通过保持小规模的代码审查，我们实现了这种优化。多人对任何给定的更改发表评论的情况并不罕见--大多数代码审查被发送给一个团队成员，但也被抄送给适当的团队——但主要的审查员仍然是那个希望得到LGTM的人，而且对于任何给定的更改，只有一个LGTM是必要的。任何其他评论，尽管很重要，但仍然是可选的。

保持小的更改也允许 "批准 "审查员更快地批准任何特定的变化。他们可以快速检查主要的代码审查员是否尽职尽责，并纯粹关注这一变化是否增强了代码库，同时随着时间的推移保持代码的健康。

变更描述应该在第一行标注它的变更类型，作为一个摘要。第一行是最重要的，它被用来在代码审查工具中提供摘要，作为任何相关电子邮件的主题行，并成为谷歌工程师在代码搜索中看到的历史摘要的可见行（见第17章），所以第一行很重要。

虽然第一行应该是整个更改的摘要，但描述仍然应该详细说明更改的内容和*原因*。“Bug修复”的描述对审查员或未来的代码考古学家来说是没有帮助的。如果在变更中进行了多个相关修改，请在列表中列出这些修改（同时仍保留信息和小信息）。描述是此更改的历史记录，代码搜索等工具允许你查找谁在代码库中的任何特定更改中编写了哪一行。在试图修复bug时，深入了解原始更改通常很有用。

描述并不是为一个更改添加文档的唯一机会。在编写公共API时，你通常不想泄露实现的细节，但在实际实现中，你应该随意地进行注释。如果审查员不理解你为什么要这样做，即使它是正确的，这也是一个很好的指标，说明这样的代码需要更好的结构或更好的注释（或两者）。如果在代码审查过程中，有了新的决定，请更新修改说明，或在实现中添加适当的注释。代码审查不仅仅是你当前所做的事情；这是你为后继者所做的记录。

在谷歌，大多数的代码审查都是由一个审查员进行审查的。由于代码审查过程允许由一个人处理代码正确性、所有者接受度和语言可读性等方面的问题，代码审查过程在谷歌这样的组织规模中具有相当好的扩展性。

在行业内和个人都有一种趋势，即试图从工程师的各个方面获得额外的投入（和一致同意）。毕竟，每个额外的审查员都可以为所讨论的代码审阅添加他们自己的特定见解。但我们发现这会导致收益递减；最重要的LGTM是第一个，后续的LGTM不会像你想象的那样添加到等式中。额外审查员的成本很快就超过了他们的价值。

代码审查过程是围绕着我们对工程师的信任而优化的，他们会做正确的事情。在某些情况下，让一个特定的更改由多人审查可能是有用的，但即使在这些情况下，这些审查员也应该专注于同一变化的不同方面。

代码评审是一个人工过程，人的投入很重要，但是如果代码过程中的某些部分可以自动化，就尽量这样做。应该探索将人类的机械任务自动化的机会；在适当的工具上的投资可以获得回报。在谷歌，我们的代码审查工具允许作者自动提交修改，并在批准后自动同步到源代码控制系统（通常用于相当简单的修改）。

在过去的几年中，关于自动化的最重要的技术改进之一是对给定的代码修改进行自动静态分析（见第20章）。目前的Google代码审查工具并不要求作者运行测试、linters或格式化程序，而是通过所谓的*预提交*自动提供大部分的效用。预提交的过程是在一个更改最初被发送给一个审查员时运行的。在该更改被发送之前，预提交程序可以检测到现有更改的各种问题，拒绝当前的更改（并防止向审查者发送尴尬的电子邮件），并要求原作者首先修复该更改。这样的自动化不仅对代码审查过程本身有帮助，还能让审查员专注于比格式化更重要的问题。

所有的代码审查都是不一样的! 不同类型的代码审查需要对审查过程中的各个环节进行不同程度的关注。在谷歌，代码变更一般都属于以下几种情况（尽管有时会有重叠）：

最不常见的代码审查类型是全新的代码，即所谓的*绿地审查*。绿地审查是评估代码是否经得起时间考验的最重要时机：随着时间和规模的变化，代码的基本假设也会发生变化，它将更容易维护。当然，引入全新的代码并不令人惊讶。正如本章前面提到的，编码是一种责任，因此引入全新的代码通常应该解决一个真正的问题，而不仅仅是提供另一种选择。在Google，除了代码审查之外，我们一般要求新的代码和/或项目要经过广泛的设计审查。代码审查不是辩论过去已经做出的设计决定的时候（同样的道理，代码审查也不是介绍建议的API的设计的时候）。

为了确保代码是可持续性，绿地审查应该确保API与商定的设计相匹配（这可能需要审查设计文档），并进行*充分测试*，所有API端点都有某种形式的单元测试，而且当代码的假设发生变化时，这些测试会失效。(见第11章）。代码还应该有适当的所有者（一个新项目的第一次审查往往是对新目录的一个单一的OWNERS文件的审查），有足够的注释，如果需要的话，还应该提供补充文档。绿地审查也可能需要将项目引入持续集成系统。(参见第23章）。

谷歌的大多数更改一般都属于对代码库内现有代码进行更改的类型。这些新增内容可能包括对API端点的更改，对现有实现的改进，或对其他因素的优化，如性能。这类更改是大多数软件工程师的日常。

在每一种情况下，适用于绿地审查的指南也适用：这种更改是否必要，以及这种更改是否改善了代码库？对代码库的一些最佳更改实际上是删除！消除死代码或过时代码是改善代码库整体代码健康状况的最佳方法之一。

任何行为修改都必须包括对任何新API行为的适当测试的修订。应在持续集成（CI）系统中测试对实现的增强，以确保这些修改不会破坏现有测试的任何基本假设。此外，优化当然应该确保它们不会影响这些测试，并且可能需要包括性能基准，供审查员参考。一些优化可能还需要基准测试。

不可避免地，你将需要提交一个更改以修复你的代码库中的bug。*在这样做的时候，要避免一起处理其他问题的诱惑*。这不仅有可能增加代码审查的规模，也会使执行回归测试或其他人回滚你的更改更加困难。bug修复应该只关注于修复指定的bug，并且（通常）更新相关的测试以捕获最初发生的错误。

用修改后的测试来解决这个bug往往是必要的。这个bug的出现是因为现有的测试不充分，或者代码中的某些假设没有被满足。作为一个bug修复的审查员，如果适用的话，要求更新单元测试是很重要的。

有时，像谷歌这样庞大的代码库中的代码变更会导致一些依赖失效，而这些依赖要么没有被测试正确地检测到，要么就是发现了代码库中未经测试的部分。在这些情况下，谷歌允许这种变化被 "回滚"，通常是由受影响的下游客户进行。回滚由基本上撤消先前更改的更改组成。这种回滚可以在几秒钟内创建，因为它们只是将以前的更改恢复到已知状态，但仍然需要代码检查。

同样至关重要的是，任何可能导致潜在回滚的更改（这包括所有的变化！）都要尽可能小且原子化，这样，如果需要回滚，就不会导致其他依赖关系的进一步破坏，从而难以解开。在谷歌，我们看到开发人员在提交新代码后很快就开始依赖新代码，而回滚有时会破坏这些开发人员。小更改有助于缓解这些担忧，这既因为它们的原子性，也因为对小更改的审查往往很快完成。

谷歌的许多变更是自动生成的：变更的作者不是人，而是机器。我们在第22章中讨论了更多关于大规模变更(LSC)的过程，但即使是机器生成的变更也需要审查。在被认为是低风险的情况下，它被指定的审查员审查，他们对我们的整个代码库有批准权。但对于那些可能有风险或需要本地领域专业知识的变化，个别工程师可能被要求审查自动生成的变化，作为他们正常工作流程的一部分。

乍一看，对自动生成的变更的审查应与任何其他代码审查一样进行处理：审查员应检查变更的正确性和适用性。但是，我们鼓励审查员限制相关更改中的注释，只标记特定于其代码的关注点，而不是生成更改的底层工具或LSC。虽然具体的变更可能是机器生成的，但生成这些变更的整个流程已经过审查，单个团队不能对该流程拥有否决权，否则就不可能在整个组织中扩展此类变更。如果对基础工具或流程存在担忧，审查员可以将带外问题上报给LSC监督小组，以获取更多信息。

我们还鼓励自动更改的审查者避免扩大其范围。当审查一项新功能或一项由团队成员编写的变更时，通常有理由要求作者解决同一变更中的相关问题，只要该请求仍然遵循先前的建议，以保持较小的变更。这不适用于自动生成的变更，因为运行工具的人可能有数百个变更在进行，即使是带有审查意见或无关问题的一小部分更改，也会限制人员有效操作该工具的范围。

代码审查是谷歌最重要、最关键的流程之一。代码评审充当着工程师之间的粘合剂，代码评审过程是开发人员的主要工作流程，几乎所有其他过程都必须依赖于此，从测试到静态分析再到CI。代码评审过程必须适当扩展，因此，最佳实践（包括小变更、快速反馈和迭代）对于保持开发人员满意度和适当的生产速度非常重要。
