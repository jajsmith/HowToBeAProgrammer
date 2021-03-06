# 如何处理偶现的Bugs

偶现bug是外部不可见的50足的蝎子的亲戚。这种噩梦是如此稀少以至于它很难观察，但其出现频率使得它不能被忽视。你不能调试因为你不能找到它。

尽管在8个小时后你会开始怀疑，偶现的bug必须像其他事情一样遵循相同的逻辑规律。但困难的是它只发生在一些未知的情形。尝试记录这个bug会出现的情况，这样你可以猜测真实的影响变量是什么。情况可能跟数据的值相关，比如“这只是在我们把*Wyoming*作为一个值输入时发生”，如果这不是变量的根源，下一个怀疑应该是不合适的同步并发。

尝试，尝试，尝试去在一种可控的方式下重现这个bug。如果你不能重现它，用日志系统给它设置一个圈套，来在你需要的时候，在它真的发生的时候，记录你猜想的，需要的东西。重新设计这个圈套，如果这个bug只发生在产品中，且不在你的猜想中的话，这可能是一个长的过程。你从日志中得到的（信息）可能不能提供解决方案，但可能给你足够的信息去优化这个日志。优化后的日志系统可能花很长时间才能被放入产品中使用。然后，你必须等待bug重新出现以获得更多的信息。这个循环可能会继续好几次。

我曾创建过的最愚蠢的偶现bug出现在，一个函数式编程语言里为类工程做多线程实现。我非常仔细地保证了函数式程序的并发估计，还有好的CPU使用（在这个例子里，是8个CPU）。我简单地忘记了同步垃圾回收器。系统可能运行了很长一段时间，经常结束在我开始任何一个任务的时候，在任何能被注意到的事情出错之前。我很遗憾地承认在我理解我的错误之前，我甚至开始怀疑硬件了。

在工作中我们最近有这样一个偶现的bug让我们花了几个星期才发现。我们有一个多线程的基于Apache™的Java™web服务器,在维护第一个页面跳转的时候，我们在四个独立线程里以4个独立的线程而非页面跳转线程为一个小的集合执行所有的I/O操作。每一次跳转会产生明显的卡顿然后停止做任何有用的事情，直到几个小时后，我们的日志允许我们去了解发生了什么。因为我们有四个线程，在一个线程内部发生这种情况并不是什么大问题，除非所有的四个线程都阻塞了。然后被这些线程排空的队列会迅速填充所有可用的内存，然后导致我们的服务器崩溃。这个bug花了我们一个星期去揪住这个问题，但我们仍然不知道什么导致了这个现象，不知道它什么时候会发生，甚至不知道它们阻塞的时候，线程们在干什么。

这表明了有关使用第三方软件的一些风险。我们在使用一段授权的代码，从文本中移除HTML标签。受它的起源的影响，我们把它叫做法国脱衣舞者。尽管我们有源代码（由衷感谢！），我们没有仔细研究它，直到查看我们服务器的日志的时候，我们最终意识到“法国脱衣舞者”中，通信线程阻塞了。

这个工具在大多数时候工作得很好，除了处理一些长而不常见的文本时。在那些文本里，代码复杂度是N平方或者更糟。这意味着处理时间与文本的长度的平方成正比。由于这些文本通常都会出现，我们可以马上发现这个bug。如果他们从来都不会出现，我们永远都不会发现这个问题。当它发生时，我们花了几个星期去最终理解并且解决了这个问题。

Next [如何学习设计技能](11-How to Learn Design Skills.md)
