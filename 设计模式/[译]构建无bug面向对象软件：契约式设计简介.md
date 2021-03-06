这是一篇翻译，原文[Building bug-free O-O software: An Introduction to Design by Contract](https://www.eiffel.com/values/design-by-contract/introduction/)

在我们看来，下面所概述的技术和其他对象技术一样重要——就像类、对象、继承、多态性和动态绑定一样重要，而这些技术又是它们的补充 —— 尽管到目前为止，只有面向对象文献中的一个子集对它给予了关注。

为了超越本文提供的理论理解，体验其思想的实际威力，请看一下EiffelStudio环境，也就是它们的直接实现。

## 1 - 介绍

在思考新的软件开发方法和工具时，很多人往往将生产力视为主要的预期收益。而在对象技术中，尤其是在艾菲尔，生产力的收益不仅仅是来自于方法的直接利益，而是来自于它对质量的重视。用NEC公司C&C软件开发集团副总裁K.藤野的话来说。

>当追求质量时，生产力也就随之而来

(引自Carlo Ghezzi、Dino Mandrioli和Mehdi Jazayeri，《软件工程》，Prentice Hall 1991年)。

软件质量的一个重要组成部分是可靠性：系统按照规范执行工作的能力（正确性）和处理异常情况的能力（鲁棒性）。更简单地说，可靠性就是不存在bug。

可靠性虽然在软件构建中无论采用哪种方法都是可取的，但在面向对象方法中，可靠性尤为重要，因为该方法赋予了可重用性的特殊作用：除非我们能够获得可重用的软件组件，而这些组件的正确性比我们对一般的运行软件的正确性更值得信赖，否则，可重用性是一个失败的命题。

我们怎样才能构建可靠的面向对象软件呢？答案是有几个组成部分。比如说，静态类型，在不一致的问题还没有来得及变成bug之前，静态类型是一个重要的帮助。像垃圾回收这样的技术，虽然有时被认为是一个实现细节，但实际上也是必不可少的，它可以消除内存管理错误的幽灵。就其本身而言，可重用性也有帮助：如果你能够重用由一个可信任的外部来源制作并（大概是）验证过的组件库，而不是为你遇到的每一个问题开发自己的解决方案，你可以开始信任软件的一部分，不亚于信任它所运行的机器。实际上，可重用的库成为了 "硬件-软件机器"（硬件、操作系统、编译器）的一部分。

但这还不够。为了确保我们的面向对象软件能够正常运行，我们需要一种系统的方法来指定和实现软件系统中的面向对象软件元素及其关系。本文将介绍这样一种方法，即所谓的Design by Contract理论。在Design by Contract理论下，软件系统被看成是一组通信组件，它们之间的相互作用是基于精确定义的相互义务规范--契约。

契约设计的好处包括：

- 更好地了解面向对象的方法，并且更广泛地了解软件的构造。
- 一种构建无错误的面向对象系统的系统方法。
- 一个有效的框架，用于调试，测试以及更广泛的质量保证。
- 一种记录软件组件的方法。
- 更好地理解和控制继承机制。
- 一种处理异常情况的技术，可为异常处理提供安全有效的语言构造。

下面提出的想法是Eiffel的一部分，敦促读者在这里将其视为一种编程语言，而不是一种软件开发方法。

## 2 - 规格和调试

为了提高软件的可靠性，第一个也可能是最困难的问题是尽可能精确地定义每个软件元素的目的。最直接的反对意见是，指定一个模块的目的并不能确保它实现这个规范；这显然是对的，但是。

人们可以颠倒一下这个命题，注意到，如果我们不说明一个模块应该做什么，那么它做的可能性很小。(排除法则的奇迹)。
在实践中，仅仅说明一个模块应该做什么，就能帮助确保它做什么，这一点是很了不起的。
正如下面将看到的那样，规范的存在，即使不能完全保证模块的正确性，也是系统测试和调试的良好基础。

那么，"契约设计 "理论建议将规范与每个软件元素关联起来。这些规范（或契约）支配着这个元素与世界其他部分的交互。

然而，本介绍并不主张使用完整的正式规范。尽管关于正式规范的工作一般都很吸引人，但我们还是选择了一种不一定是详尽的规范的方法。这样做的好处是规范语言被嵌入到设计和编程语言中（这里是Eiffel），而正式的规范语言通常是不可执行的，或者说，如果是可执行的，只能用于原型。这里，我们的标准要求更高：我们希望我们的语言能够用于实际的商业开发，从而产生高效的实现。这就保留了一个广为人知的面向对象过程的一个关键属性：它的无缝性，使我们能够在整个软件生命周期中，从分析到实现和维护，使用单一的符号和单一的概念集，确保了从解决方案到问题的更好的映射，从而使进展更加顺畅。

## 3 - 契约的概念

在人事事务中，当一方（供应商）为另一方（客户）执行某些任务时，契约是在双方之间订立的。 各方都希望从契约中受益，并接受一些义务作为回报。 通常，当事一方视为义务是另一方的利益。 契约文件的目的是阐明这些好处和义务。

如下所示的表格形式（说明了航空公司与客户之间的契约）通常可以方便地表达此类契约的条款：

||义务|权利|
|--|---|---|
|客户|（必须确保前置条件）请在预定起飞时间至少5分钟之前到达圣塔芭芭拉机场。 仅携带可携带的行李。 支付门票价格。|（可能受益于后置条件）到达芝加哥。|
|提供方|（必须确保后置条件）带客户去芝加哥。|（可以假定为前置）无需携带迟到，行李不可接受或未支付机票价格的乘客。|

契约文件通过指定应完成的工作量来保护客户，并通过声明供应商对未能执行超出指定范围的任务不承担责任来保护供应商。

同样的想法也适用于软件。 考虑一个软件元素E。为了实现其目的（履行其自己的契约），E使用了一种确定的策略，其中涉及许多子任务t1，…tn。 如果子任务ti不平凡，则可以通过调用某个例程R来实现。换句话说，E将子任务分包给R。这种情况应由明确定义的义务和收益名册（契约）来控制。 。

例如，假设ti是将某个元素插入到有限制容量的字典（一个表，其中每个元素由用作键的某个字符串标识的表）中的任务。 契约将是：

||义务|权利|
|--|---|---|
|客户|（必须确保前置条件）确保表未满并且键是非空字符串。|（可能受益于后置条件）获取与给定键关联的给定元素现在出现的更新表。|
|提供方|（必须确保后置条件）在表中记录与给定键关联的给定元素。|（可以假定为前置）如果表已满，或者键为空字符串，则无需执行任何操作。|

该契约支配着例程与任何潜在调用者之间的关系。 它包含有关例程的最重要信息：契约中的每一方必须保证正确的通话，以及每一方有权获得的回报。

这些信息确实非常重要，以至于我们不能对上述非正式契约说明感到满意。 本着无缝性的精神（鼓励我们在一个软件文本中将各个级别的所有相关信息都包括在内），我们应该在常规文本中配备适当条件的清单。 假设该例程被称为put，它将作为通用类DICTIONARY [ELEMENT]的一部分在Eiffel语法中如下所示：

```eiffel
put (x: ELEMENT; key: STRING) is
		-- Insert x so that it will be retrievable through key.
	require
		count <= capacity
		not key.empty
	do
		... Some insertion algorithm ...
	ensure
		has (x)
		item (key) = x 
		count = old count + 1
	end
```

require子句引入了输入条件或前置条件； ensure子句引入输出条件或后置条件。 这两个条件都是与软件元素相关联的断言或逻辑条件（契约条款）的示例。 在此前置下，count是当前元素数，容量是最大元素数； 在后置条件中，has是一个布尔查询，它告诉是否存在某个元素，并且item返回与某个键相关联的元素。 旧计数表示法是在进入例程时的计数值。

## 4 - 在契约中分析

上面的示例是从描述实现的例程中提取的（尽管字典的概念实际上与任何实现关注无关都有意义）。 但是这些概念在分析级别上同样有趣。 想象一下一个化工厂的模型，它具有诸如TANK，PIPE，VALVE，CONTROL_ROOM之类的类。 这些类中的每一个都描述某种特定的数据抽象—一种特定类型的实际对象，其特征在于适用的功能（操作）。 例如，TANK可能具有以下功能：

- 是/否查询：is_empty，is_full…
- 其他查询：in_valve，out_valve（均为VALVE类型），gauge_reading，capacity...
- 命令：fill，empty，…

然后，为了表征诸如填充之类的命令，我们可以使用上述的前置条件和后置条件：

```eiffel
fill is
		-- Fill tank with liquid
	require
		in_valve.open
		out_valve.closed
	deferred
		-- i.e., no implementation
	ensure
		in_valve.closed
		out_valve.closed
		is_full
	end
```

这种分析风格避免了经典的分析和规范难题：要么使用编程符号，否则就有做出过早实施承诺的风险； 或者您坚持使用更高级别的符号（“泡泡和箭头”），并且必须保持模糊，放弃分析过程的主要好处之一，即陈述和阐明系统微妙特性的能力。 这里的表示法是精确的（由于使用断言机制，该机制可用于捕获各种操作的语义），但是避免了任何实现承诺。 （在上面的示例中，没有这样的承诺的危险，因为它描述的内容不包括软件，实际上还没有计算机！在这里，我们将这种表示法用作建模工具。）

正如Waldén和Nerson所描述的，Business Object Notation是唯一的面向对象方法，可以在分析和设计级别上完全集成这些想法，并为本文中提出的想法提供图形表示。

## 5 - 不变式

前置条件和后置条件适用于各个例程。 其他类型的断言将描述一个类的整体，而不是其单个例程。 一个描述一个拥有类的所有实例的属性的断言称为类不变式。 例如，DICTIONARY的不变量可以说明

```eiffel
invariant
	0 <= count
	count <= capacity
```

而TANK的不变式可以表明is_full的确意味着“大约已满”：

```eiffel
invariant
	is_full = (0.97 * capacity <= gauge) and gauge <= 1.03 * capacity)
	... Other clauses ...
```

类不变式是描述类语义的一致性约束。 这个概念对于配置管理和回归测试非常重要，因为它描述了类的更深层次的属性：不仅是类在其发展的某个时刻所具有的特性，而且还必须适用于后续更改。

从契约理论看，不变式是一个通用条款，适用于定义类别的整个契约。

## 6 - 文档

契约的另一个关键应用是提供一种记录软件元素（类）的标准方法。 为了为客户程序员提供对类的接口属性的正确描述，只需给他们提供该类的一个版本，即简称形式，即可删除所有实现信息，但保留必要的使用信息：契约。

在EiffelStudio环境中，您可以通过单击“类工具”的“简短”按钮以交互方式获取简短形式。 输出可以是纯文本，也可以通过环境的预定义过滤器之一转换为任何文本处理格式（Microsoft RTF，Web发布HTML，FrameMaker的MIF或MML，TEX，troff，Postscript等），您可以将其转换为 由于该机制是完全开放的，因此您可以添加任何自己的过滤器。

简短形式保留导出功能的标头和断言以及不变式，但丢弃其他所有内容。 例如：

```eiffel
class DICTIONARY [ELEMENT]
feature
	put (x: ELEMENT; key: STRING) is
			-- Insert x so that it will be retrievable
			-- through key.
		require
			count <= capacity
			not key.empty 
		ensure
			has (x)
			item (key) = x
			count = old count + 1
		end

	... Interface specifications of other features ...

invariant
	0 <= count
	count <= capacity
end
```

此简短形式是文档库和其他软件元素的基本工具。 它还充当开发人员之间的中央交流工具。 我们从客户和我们的经验中学到，强调简短的形式有助于软件设计和项目管理，因为它鼓励开发人员和管理人员讨论关键问题（接口，规范，模块间协议）而不是内部细节。

## 7 - 测试，调试，质量保证

给定一个带有断言的类文本，我们理想地应该能够在数学上证明例程的实现与断言是一致的。在没有可行的工具来做到这一点的情况下，我们可以适应下一个最好的事情，那就是使用断言进行测试。

编译选项使开发人员可以按类别逐个确定断言应具有的效果：不进行断言检查（在此条件下断言根本不起作用，用作标准化注释的一种形式），仅前置条件（默认），前置条件和后置条件，以上所有内容以及类不变量，所有断言。

这些机制为发现错误提供了强大的工具。断言监视是一种根据软件作者的想法检查软件性能的方法。这产生了一种调试，测试和质量保证的有效方法，其中错误的寻找不是盲目的，而是基于开发人员自身提供的一致性条件。

以我的经验，这些机制的可用性是转向该技术的最重大后果之一。它导致错误数量的急剧下降，并导致开发人员对软件可靠性的新态度。

## 8 - 契约和继承

契约设计理论的一个重要后果是对继承、多态性、重定义和动态绑定等面向对象的核心概念有了更好的理解。

例如，一个继承自类A的类B可能为A的某个继承特征r提供一个新的声明。然而，这样的重新定义是潜在的危险，因为重新定义的版本原则上可能有完全不同的语义。在多态性的情况下，这一点尤其令人担忧，这意味着在调用

```eiffel
a.r
```

调用的目标a，虽然静态地声明为A类型，但实际上在运行时可以附加到B类型的对象上。

这是一种分包的形式。A将相应类型的目标r分包给B，将相应类型的目标分包给B。但分包者必须受原契约的约束。客户端如果执行一个形式为

```eiffel
if a.pre then
	a.r
end
```

必须保证契约上承诺的结果：由于前置条件被满足，调用将被正确地执行（假设pre意味着r的前置条件）；并且在退出时a.post将为真，其中post是r的后置条件。

从这些观察中我们可以得出分包原则：r的重新定义的版本可能会保留或削弱前置条件，也可能保留或加强后置条件。强化前置条件，或者削弱后置条件，都是 "不诚实的分包"，可能导致灾难。Eiffel语言的断言重定义规则支持了转包原则。

这些意见阐明了继承的真正意义：不只是一种重用、子类化和类机制，而是通过其他手段确保语义兼容的方式。它们还为如何正确使用继承提供了有益的指导。

## 9 - 异常捕获

在契约理论的许多其他应用中，我们可能会注意到，该理论自然会导致系统地解决棘手的异常处理问题-处理异常案件。

软件元素始终是达成特定契约（无论是否明确）的一种方式。 一个例外是元素由于任何原因而无法履行契约：发生硬件故障，被调用的例程失败，软件错误导致无法履行契约。

在这种情况下，只有三个回应才有意义：

1. 重试：有一种替代策略。 该例程将使用新策略恢复不变式，并再次尝试。
2. 有组织的panic：没有其他替代方法。 通过触发新异常来恢复不变式，终止并向调用者报告故障。 （呼叫者本身必须在相同
三个回应。）
3. 错误警报：实际上，有可能在采取一些纠正措施后继续发生。 这种情况很少发生（遗憾的是，因为它最容易实现！）。

异常机制直接来自此分析。它基于与例程相关联的“救援条款”和实现重试的“重试指令”的概念。这类似于人工契约中出现的条款，以允许出现特殊的计划外情况。如果存在Rescue子句，则在例程执行期间发生的任何异常都会中断主体（do子句）的执行并开始执行Rescue子句。该子句包含一个或多个指令；其中之一是重试，这将导致例程主体的重新执行（do子句）。诸如失败之类的整数局部实体在例程进入时总是初始化为零（但重试之后当然不会初始化）。

这是说明该机制的示例。我们假设一个低级过程unsafe_transmit用于通过网络传输消息。我们无法控制该过程，但是知道它可能会失败，在这种情况下，我们想再次尝试，尽管在100次失败尝试之后，我们将放弃，将异常传递给调用者。救援/重试机制简单直接地支持此操作：

```eiffel
attempt_transmission (message: STRING) 
		-- Attempt to transmit message over a communication line
		-- using the low-level (e.g. C) procedure 

unsafe_transmit
		-- which may fail, triggering an exception.
		-- After 100 unsuccessful attempts, give up (triggering
		-- an exception in the caller).
	local
		failures: INTEGER
	do
		unsafe_transmit (message)
	rescue
		failures := failures + 1
		if failures < 100 then
			retry
		end
	end
```

## 10 - 下一步发展

本文对《契约设计》的基本思路进行了概述。这是一个非常活跃的应用和进一步研究的领域，目前有几本书正在编写中。正在发展中的两个领域是：

- 并发和分布式：Design by Contract的原理产生了一个迷人的解决方案，解决了面向对象的并发和分布式编程的问题（避免了所谓的 "继承异常"和其他非面向对象并发计算的问题，这是由于对对象技术的误解导致的）。
- 一个扩展的规范语言，允许表达更丰富的断言集。

契约设计（Design by Contract）已经得到了广泛的应用；该理论为整个面向对象方法提供了一条强有力的主线，解决了许多人在开始认真应用面向对象技术和语言时遇到的问题：应用什么样的"方法论"，基于什么样的概念来进行分析步骤，如何指定组件，如何记录面向对象软件，如何指导测试过程，最重要的是，如何构建软件，使BUG不会在第一时间出现。

在软件开发中，可靠性应该是内在的，而不是事后考虑。