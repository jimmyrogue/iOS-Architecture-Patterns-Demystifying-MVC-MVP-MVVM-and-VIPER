#翻译 iOS Architecture Patterns Demystifying MVC, MVP, MVVM and VIPER
[原文地址](https://medium.com/ios-os-x-development/ios-architecture-patterns-ecba4c38de52#.lo17irk94)

_英语水平一般，仅供参考，欢迎提出建议(尚未完成)_

#iOS Architecture Patterns（iOS设计模式）
#####揭秘 MVC, MVP, MVVM and VIPER
----
使用MVC模式开发iOS过程中，你是否有过疑问，为什么要使用MVC？有考虑MVVM吗？听说过VIPER但是不确定这样做是否有价值？  
继续读下去，也许你就会找到答案。    

你即将了解到关于iOS开发过程中使用的设计模式知识。我们将简短的讨论几个流行的设计模式，比较它们，然后讨论几个小例子。  


掌握设计模式固然是吸引人的，不过小心：读完之后你可能会有更多的疑问，像是:

- 网络请求应该属于那一层？Model 还是 Controller？
- 我该如何将一个Model传入一个新View中的View Model？
- VIPER的一个新组件该由谁该创建？Router 还是 Presenter？

##为什么要关心选择何种设计模式？
因为如果不关心，某天，当你debugging一个超大的类，包含一大堆不同的东西，你会发现自己无法在这个类中找到或是处理bugs，自然的，这庞大的类无法“保存”在你的大脑中，因此你总会遗失一些重要的细节。如果你和你的应用已经陷入到这样的情况中，那么很有可能：

- 这个庞大的类是UIViewController或者它的子类
- 你在UIViewController里直接存储数据
- 你的UIView基本上没什么用处
- 你的Model结构很糟糕
- 你的单元测试毫无作用

尽管你遵循了Apple的开发准则以及使用了Apple的MVC模式，但这样的情况仍然是有可能发生的，别太难过。[Apple的 MVC模式](https://developer.apple.com/library/ios/documentation/General/Conceptual/DevPedia-CocoaCore/MVC.html)有些问题，不过我们一会再讨论。  

让我们来定义一下好的模式所具有的特征：

- 分散好不同实体间的责任（待修改:Balanced distribution of responsibilities among entities with strict roles.）
- 可测试性
- 易用性以及较低的后期维护性

###为什么要分离？
我们试着弄清楚这是如何工作的时候，分离不同层可以让我们的大脑处于一个合适的负荷。如果你觉得开发的越多你的大脑越能理解复杂的难题，那么你是对的。但是这种能力并非线性增长且不能一下子就达到上线。所以解决复杂问题最简单的方法就是将其分离，分配给多个实体。看看这个[单一功能原则](https://zh.wikipedia.org/wiki/%E5%8D%95%E4%B8%80%E5%8A%9F%E8%83%BD%E5%8E%9F%E5%88%99)

###可测试性？
对于那些因为增加新功能或是重构一些错综复杂的类而失败的，对于单元测试觉得感激的开发者来说，并不算是问题。可能就是这些测试让开发者能够在上线前就找到问题，而不是当应用已经在用户的设备里，然后花了一个星期才将问题解决好，再送到用户手中。


###易于使用？
这个问题不需要回答，但是值得一提的是：the best code is the code that has never been written。所以你写的代码越少，问题也就越少。对于完全“懒惰”开发者来说渴望更少的代码是不需要解释的，你应该不赞成闭上眼睛是一个更聪明的解决方案。**（待修改）**

###MV(X)的要点
如今，当我们说到设计模式的时候已经有很多选择了

- [MVC](https://zh.wikipedia.org/wiki/MVC)
- [MVP](https://zh.wikipedia.org/wiki/Model_View_Presenter)
- [MVVM](https://en.wikipedia.org/wiki/Model%E2%80%93view%E2%80%93viewmodel)
- [VIPER](https://www.objc.io/issues/13-architecture/viper/)



