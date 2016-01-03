#翻译 iOS Architecture Patterns Demystifying MVC, MVP, MVVM and VIPER
[原文地址](https://medium.com/ios-os-x-development/ios-architecture-patterns-ecba4c38de52#.lo17irk94)

_英语水平一般，仅供参考，欢迎提出建议(第一版完成 v1.0 2016.1.3)_
_还有一些地方不知道如何翻译以及翻译不到位，希望大家指出，并提供建议_

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

- 分散不同实体间的责任（待修改:Balanced distribution of responsibilities among entities with strict roles.）
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

前三种类型认为应该将app内的实体分成三类：

- Model（模型）-- 负责操作数据的数据层或是数据访问层，想象一下“Person”类 或者 “PersonDataProvider” 类
- View（视图）-- 视图层，负责呈现界面，iOS内认为所有的View都应该以“UI”作为前缀
- Controller/Presenter/ViewModel(控制器) -- Model 和 View的粘合剂或者说中介，负责根据用户在View上的行为而作出反应，改变Model，或是反过来，随着Model的改变而更新View的内容。

有了这些实体的分离才使得我们：

- 更好的理解这些实体（早知道了）
- 复用（主要适用在View 和 Model）
- 单独的测试他们

让我们从MV（X）开始之后再讨论Viper

###MVC
######它曾经的样子
在讨论Apple版的MVC之前，让我们先看看传统类型的[MVC](https://zh.wikipedia.org/wiki/MVC)

![image](https://cdn-images-1.medium.com/max/600/1*E9A5fOrSr0yVmc7Kly5C6A.png)

在这个例子中，View是无状态的（stateless）。  
一旦Model变化，它很容易被Controller控制渲染。想象一下你点击了某个链接然后网页完全重新加载了一次。这种传统的MVC模式当然也可以用在iOS应用开发中，由于每个实体都紧紧的和其他两个耦合，所以这并没有太多意义。他大大的减少了可复用性--这并不是你想要的。因此，我们尽量避免使用这种**典型的MVC**

> 传统的MVC看起来并不适合于现代的iOS开发

###Apple's MVC
######Expectation（理想）

![image](https://cdn-images-1.medium.com/max/600/1*c0aGaDNX41qu6e8E4OEgwQ.png)

在这里Controller是View和Model的中介，所以View和Model无法知道对方。Controller最难复用，不过可以接受，毕竟我们必须找一个地方存放那些繁琐的业务逻辑。  
理论上来说，这看起来很简单直白，但是不是总觉得有什么地方不对劲？你可能听到过人们这么解释MVC：**厚重的View Controller**（太对了我的viewController里充满了乱七八糟的逻辑）。此外，[减轻View Controller](https://www.objc.io/issues/1-view-controllers/lighter-view-controllers/) 已经成为iOS开发者们讨论的一个重要话题。Apple拿来传统的MVC并且改进了它，为什么还是发生了这样的事情？  

###Apple's MVC
######Reality(现实)

![image](https://cdn-images-1.medium.com/max/800/1*PkWjDU0jqGJOB972cMsrnA.png)

因为在View的生命周期中View和ViewController太难分开，所以Cocoa MVC 鼓励你写厚重的View Controller。当然你还是可以将一些业务逻辑和数据转移到Model中，当你需要这样做的时候，并没有太多的选择，大多数情况下，View的责任是传递事件到Controller。View Controller最终变成了所有事情的delegate和 data source，同时他又要负责分发和取消网络请求之类的。。。你懂的。  

你见过下面的代码多少次  

	var userCell = tableView.dequeueReusableCellWithIdentifier("identifier") as UserCell
	userCell.configureWithUser(user)

这个cell，是由View通过Model来设置的，这其实和MVC规则相悖，但这种情况一直存在，一般来说人们并不会感觉到不妥。如果你严格的遵循MVC，那你应该支持从Controller来设置Cell，不要将Model耦合进View中，这样的话就会越发增加你Controller的体积。
> Cocoa MVC 被称为 **厚重的View congtroller**是有道理的

单元测试以前这些问题可能并不明显（但愿你的项目中有单元测试）。自从你将View Controller和View紧密的耦合在一起之后，它就变得难以测试了。（because you have to be very creative in mocking views and their life cycle 不知道怎么翻译才好），当你用这种方式来写View Controller代码的时候，你的业务逻辑和布局代码得尽量分离开来。    

让我们看看下面这个例子:  
	
	import UIKit
	struct Person { // Model
    let firstName: String
    let lastName: String
	}

	class GreetingViewController : UIViewController { // View + Controller
    var person: Person!
    let showGreetingButton = UIButton()
    let greetingLabel = UILabel()
    
    override func viewDidLoad() {
        super.viewDidLoad()
        self.showGreetingButton.addTarget(self, action: "didTapButton:", forControlEvents: .TouchUpInside)
    }
    
    func didTapButton(button: UIButton) {
        let greeting = "Hello" + " " + self.person.firstName + " " + self.person.lastName
        self.greetingLabel.text = greeting
        
    }
    // layout code goes here
	}
	// Assembling of MVC
	let model = Person(firstName: "David", lastName: "Blaine")
	let view = GreetingViewController()
	view.person = model;

> MVC 的组装可以在当前的View Controller中呈现 （MVC assembling can be performed in the presenting view controller）

看上去并不容易测试对吗？我们可以将greeting 放到一个新的 GreetingModel 类中 然后可以分离测试，但是我们无法在GreetingViewController中脱离UIView（viewDidLoad,didTapButton），直接测试任何表现逻辑（presentation logic）（当然例子中并没有太多表现逻辑）,可能会造成加载全部的Views，这对于单元测试是不好的。  

事实上，在模拟器中加载和测试UIView（比如iphone4s）并不一定能保证在别的设备上正常运行（比如ipad）,所以我建议你从单元测试目标中移除你的**主应用程序**，在模拟器上抛开主应用运行你的测试。  
> View 和 Controller 的通信是可以进行[单元测试](http://ashfurrow.com/blog/whats-worth-unit-testing-in-objective-c/)的

我们都说，选择Cocoa MVC可能是个糟糕的选择。不过让我们从文章开头的定义来评估一下它吧：  

- 分离----View和Model分离，但View 和 Controller紧密耦合
- 可测试性---由于糟糕的解耦，你可能只能测试你的Model
- 易用性----和其他模式比最少的代码。并且大家都熟悉它，因此，就算是最没有经验的开发者，也是很容易掌握的。

如果你并没有打算投入过多的时间在你的设计模式上，并且觉得高的维护成本对于你的小项目并没有什么影响，Cocoa MVC将会是一个好的选择.(Cocoa MVC is the pattern of your choice if you are not ready to invest more time in your architecture, and you feel that something with higher maintenance cost is an overkill for your tiny pet project.)

> Cocoa MVC 对于追求开发速度的开发者来说是最好的设计模式选择、

###MVP
###### Cocoa MVC的 真正实现者者

![image](https://cdn-images-1.medium.com/max/800/1*hKUCPEHg6TDz6gtOlnFYwQ.png)

它看上去是不是和Apple的MVC非常相似？是的没错，他的名字是[MVP](https://en.wikipedia.org/wiki/Model%E2%80%93view%E2%80%93presenter)(Passive View variant)，不过等一下，那是不是意味着Apple的MVC实际上就是MVP？并不是，回想一下，View和Controller紧密耦合在一起，但是MVP的中介，Presenter就完全不参与View Controller的生命周期里，View可以容易的被mocked（仍然不知道怎么翻译），因此在Presenter中就不会有任何**布局代码**，但是它仍然负责更新View的数据和状态。  

> 如果我告诉你 UIViewController 就是 View

在MVP中，UIViewController的子类实际上是View而不是Presenter。由于你必须手动操作数据以及事件的绑定，所以以开发速度为代价，使它提供了卓越的可测试性。例子：

	import UIKit

	struct Person { // Model
    let firstName: String
    let lastName: String
	}

	protocol GreetingView: class {
    func setGreeting(greeting: String)
	}

	protocol GreetingViewPresenter {
    init(view: GreetingView, person: Person)
    func showGreeting()
	}

	class GreetingPresenter : GreetingViewPresenter {
    unowned let view: GreetingView
    let person: Person
    required init(view: GreetingView, person: Person) {
        self.view = view
        self.person = person
    }
    func showGreeting() {
        let greeting = "Hello" + " " + self.person.firstName + " " + self.person.lastName
        self.view.setGreeting(greeting)
    }
	}

	class GreetingViewController : UIViewController, GreetingView {
    var presenter: GreetingViewPresenter!
    let showGreetingButton = UIButton()
    let greetingLabel = UILabel()
    
    override func viewDidLoad() {
        super.viewDidLoad()
        self.showGreetingButton.addTarget(self, action: "didTapButton:", forControlEvents: .TouchUpInside)
    }
    
    func didTapButton(button: UIButton) {
        self.presenter.showGreeting()
    }
    
    func setGreeting(greeting: String) {
        self.greetingLabel.text = greeting
    }
    
    // layout code goes here
	}
	// Assembling of MVP
	let model = Person(firstName: "David", lastName: "Blaine")
	let view = GreetingViewController()
	let presenter = GreetingPresenter(view: view, person: model)
	view.presenter = presenter

####关于聚合（assembly）的重要说明
MVP是第一个揭示了由于有三层“明确分离”层次而产生聚合问题的设计模式。自从我们不希望View知道（直接交流）Model之后，将聚合表现在presenting View Controller（其实是View）里是错误的，所以我们必须找个别的地方来做这个。比方说，我们可以建立一个 **app-wide** 路由服务，它将负责 整合 以及 View-to-View 的presentation（跳转？）。我们不得不在MVP以及其他模式中解决这个问题。

让我们来看一下MVP的特点

- 分离---我们将大部分责任分给了Presenter和Model，还有一个相当愚蠢的View（上面例子里的Model一样愚蠢）
- 可测试性---很棒，因为有这个愚蠢的View，我们可以测试大部分业务逻辑
- 易用性--在我们不实际的简单例子中，我们代码的数量两倍于MVC，但是同时，MVP的思路很清晰

> MVP 在iOS 中意味着极好的可测试性 和 大量的代码

###MVP
######With Bindings and Hooters（这是什么鬼 翻译不能）
还有另一种形式的MVP--Supervising Controller MVP。这个变种中View和Model直接绑定，即使Presenter（The Supervising Controller）仍然管理View的事件，它还是可以改变View。![image](https://cdn-images-1.medium.com/max/600/1*bkB6Ho_G5De47IkJpaX5XQ.png)

但是，就像我们前面学到的一样，模糊的责任分离是不好的，View和Model之间紧密的耦合也一样。这和在Cocoa desktop上开发很相似。

和传统的MVC一样，我看不到这个有瑕疵的模式中的重点，不知如何写例子。（Same as with the traditional MVC, I don’t see a point in writing an example for the flawed architecture.）

###MVVM
######最新以及最伟大的MV（X）种类
MVVM是最新的一种MV（X）类型，让我们期待它的出现是为了解决以前MV（X）所存在的问题。

理论上，Model-View-ViewModel 看上去很不错。我们已经很熟悉View和Model了，而ViewModel则相当于中介
![image](https://cdn-images-1.medium.com/max/800/1*uhPpTHYzTmHGrAZy8hiM7w.png)

这跟MVP非常相似:

- MVVM 将 View Controller当作View
- View 和 Model 没有耦合

它的绑定看起来有点像 超级版本的 MVP，然而，这一次的关系不是在View和Model之间，而是在View 和 View Model之间。

在iOS中到底什么是View Model呢？It is basically UIKit independent representation of your View and its state. （翻译不出来）。View Model改变Model，并且根据 Model的变化而更新它自己，当我们绑定了View 和 View Model，便产生了第一次更新。

#####Bingdings（绑定）
我曾经在MVP那部分中简单的提到过绑定，让我们再多讨论一些。绑定来源于OX X的开发中，但是在iOS中我们没有对应的工具箱。当然了我们有KVO 和 notifications，但是他们不像绑定那样方便。

所以假如我们不想自己写它们，我们有两个选择：

- 基于KVO的 第三方库 [RZDataBinding](https://github.com/Raizlabs/RZDataBinding) 或者 [SwiftBond](https://github.com/SwiftBond/Bond)
- 函数式编程 比如  [ReactiveCocoa](https://www.google.co.uk/url?sa=t&rct=j&q=&esrc=s&source=web&cd=1&cad=rja&uact=8&ved=0CB4QFjAAahUKEwj2l6rZv5jJAhUFUhQKHWahCKs&url=https%3A%2F%2Fgithub.com%2FReactiveCocoa%2FReactiveCocoa&usg=AFQjCNHM-pOkluiSuPsaVwVujCDTknVFUA&sig2=54zu-ATo8vDMvtXbxZYTvQ)(这个大家都知道)，还有[RxSwift](https://github.com/ReactiveX/RxSwift/) 以及 [PromiseKit](https://github.com/mxcl/PromiseKit)

事实上，如今，如果你听到“MVVM”，你会联想到ReactiveCocoa，反之亦然。当然了通过简单的绑定可以让你使用MVVM，RAC（或者 sibings）会让你获得大部分MVVM。

关于reactive 框架 有一个苦涩的事实：能力越大，责任越大。它很容易让你把所有事情搞的一团糟。换句话说，如果你做错了什么，可能会花费非常多的时间来处理bug，看看下面这个请求![image](https://cdn-images-1.medium.com/max/800/1*WGIs3XQL1MtKiyApr-m9bg.png)

在我们这个简单的例子中，FRF框架甚至说KVO都是大麻烦，相反的，我们希望View Model使用showGreeting方法来更新，使用简单的greetingDidChange回调函数。


	import UIKit

	struct Person { // Model
    let firstName: String
    let lastName: String
	}

	protocol GreetingViewModelProtocol: class {
    var greeting: String? { get }
    var greetingDidChange: ((GreetingViewModelProtocol) -> ())? { get set } // function to call when greeting did change
    init(person: Person)
    func showGreeting()
	}

	class GreetingViewModel : GreetingViewModelProtocol {
    let person: Person
    var greeting: String? {
        didSet {
            self.greetingDidChange?(self)
        }
    }
    var greetingDidChange: ((GreetingViewModelProtocol) -> ())?
    required init(person: Person) {
        self.person = person
    }
    func showGreeting() {
        self.greeting = "Hello" + " " + self.person.firstName + " " + self.person.lastName
    }
	}

	class GreetingViewController : UIViewController {
    var viewModel: GreetingViewModelProtocol! {
        didSet {
            self.viewModel.greetingDidChange = { [unowned self] viewModel in
                self.greetingLabel.text = viewModel.greeting
            }
        }
    }
    let showGreetingButton = UIButton()
    let greetingLabel = UILabel()
    
    override func viewDidLoad() {
        super.viewDidLoad()
        self.showGreetingButton.addTarget(self.viewModel, action: "showGreeting", forControlEvents: .TouchUpInside)
    }
    // layout code goes here
	}
	// Assembling of MVVM
	let model = Person(firstName: "David", lastName: "Blaine")
	let viewModel = GreetingViewModel(person: model)
	let view = GreetingViewController()
	view.viewModel = viewModel
 

来看一下它的特征：

- 分离---在我们简单的例子中并不清晰，但是事实上，MVVM的View比MVP的View有更多的责任。因为第一次更新，它的状态是从View Model绑定来的，当第二次更新，则只传递了全部事件到Presenter并没有更新View本身。
- 可测试性--View Model对于View一无所知，这让我们可以容易的测试。View基本上也可以测试，但是当它开始依赖于UIKit时，你可能会想跳过它。
- 易用性---在我们的例子中，它和MVP有差不多的代码量，但是在实际项目中，你需要将所有的事件从View传递到Presenter里去，然后手动更新视图，如果使用绑定（bingings）MVVM会更瘦身。

> 自从MVVM合适的结合了上面的这些特征，MVVM就变得非常有吸引力了，此外，由于绑定属于View那边，所以它不需要额外的代码提供给View来更新。尽管如此，可测试性还保持在一个不错的等级。（待修改）

###VIPER
######搭建乐高积木般的iOS APP设计
[VIPER](https://www.objc.io/issues/13-architecture/viper/)是我们最后讨论的对象，因为它并不是从MV（X）类型中发展而来的所以特别有意思。

到如今，你一定会同意granularity in responsibilities（细分责任？）是非常好的。VIPER提供了一个新的分离责任方案，这一次我们有5层。

![image](https://cdn-images-1.medium.com/max/800/1*0pN3BNTXfwKbf08lhwutag.png)

- Interactor（关联）--- 把数据（data）或是网络请求 和业务逻辑关联起来，就好像从实体或是网络请求来创建一个新的实例。为了达到这些目的，你会需要Service或是Managers，它们并不是VIPER的一部分，更像是外在的依赖。
- Presenter---包括UI（UIKit还是独立）和业务逻辑的关系，在Interactor中创建方法。
- Entities---数据对象，并不是数据注入层，那是Interactor 的责任
- Router-- 负责VIPER Modules之间的segues（跳转？路由？）

基本上，VIPER模块可以是只有一屏幕或者完整的用户流程--想一下身份验证，他们可以只有一屏幕或是多个关联到一个。“乐高”块的大小完全由你决定。

如果我们比较一下VIPER和MV（X）类型，我们会发现他们对于分离责任的一些不同：

- Model（数据关联）逻辑被挪动到了Interactor中，关联着愚蠢的实体结构（as dumb data structures 是这么翻译的吗？）
- Controller/Presenter/ViewModel的UI交互任务被移动到了Presenter里，但是没有改变数据的能力
- VIPER是第一个有明确的路径导航责任Router的设计模式。

> 用合适的方式处理路由对于iOS应用来说是个挑战，MV（X）模式则不会有这个问题。

这个例子没有包含模块之间的routing（路由）或是 Interactor，因为在MV（X）模式下根本没有这些话题。

	import UIKit

	struct Person { // Entity (usually more complex e.g. NSManagedObject)
    let firstName: String
    let lastName: String
	}

	struct GreetingData { // Transport data structure (not Entity)
    let greeting: String
    let subject: String
	}

	protocol GreetingProvider {
    func provideGreetingData()
	}

	protocol GreetingOutput: class {
    func receiveGreetingData(greetingData: GreetingData)
	}

	class GreetingInteractor : GreetingProvider {
    weak var output: GreetingOutput!
    
    func provideGreetingData() {
        let person = Person(firstName: "David", lastName: "Blaine") // usually comes from data access layer
        let subject = person.firstName + " " + person.lastName
        let greeting = GreetingData(greeting: "Hello", subject: subject)
        self.output.receiveGreetingData(greeting)
    }
	}

	protocol GreetingViewEventHandler {
    func didTapShowGreetingButton()
	}

	protocol GreetingView: class {
    func setGreeting(greeting: String)
	}

	class GreetingPresenter : GreetingOutput, GreetingViewEventHandler {
    weak var view: GreetingView!
    var greetingProvider: GreetingProvider!
    
    func didTapShowGreetingButton() {
        self.greetingProvider.provideGreetingData()
    }
    
    func receiveGreetingData(greetingData: GreetingData) {
        let greeting = greetingData.greeting + " " + greetingData.subject
        self.view.setGreeting(greeting)
    }
	}

	class GreetingViewController : UIViewController, GreetingView {
    var eventHandler: GreetingViewEventHandler!
    let showGreetingButton = UIButton()
    let greetingLabel = UILabel()

    override func viewDidLoad() {
        super.viewDidLoad()
        self.showGreetingButton.addTarget(self, action: "didTapButton:", forControlEvents: .TouchUpInside)
    }
    
    func didTapButton(button: UIButton) {
        self.eventHandler.didTapShowGreetingButton()
    }
    
    func setGreeting(greeting: String) {
        self.greetingLabel.text = greeting
    }
    
    // layout code goes here
	}
	// Assembling of VIPER module, without Router
	let view = GreetingViewController()
	let presenter = GreetingPresenter()
	let interactor = GreetingInteractor()
	view.eventHandler = presenter
	presenter.view = view
	presenter.greetingProvider = interactor
	interactor.output = presenter
	
是的，又到了评测：

- 分离--毋庸置疑的，VIPER是做的最好的。
- 可测试性--没有意外的，更好的解耦，更好的可测试性
- 易用性--最终，上面两条带来的维护代价你已经猜到了。你必须写非常庞大的代码来实现很少的责任。

####那么乐高怎么样
当使用VIPER，你也许会感觉是在用乐高积木建造帝国大厦，那是你[出现问题](https://inessential.com/2014/03/16/smaller_please)的信号。也许，对于你的应用来说使用VIPER为时过早，你应该思考一些更简单的。有些人选择忽视这一点，继续用高射炮打蚊子。我假定VIPER在未来会适合于他们的应用，但即使现在，它的维护成本仍然高的可怕。如果你和我一样相信这一点，那么我建议你使用[Generamba](https://github.com/rambler-ios/Generamba)--一个构建VIPER构架的工具。当然我个人感觉，有点像使用自动瞄准系统的加农炮而不是简单的用弹弓。

###结论
我们看过了几个设计模式，我希望你已经找到了一些曾经困扰你的问题的答案，不过我相信你也认识到并不存在**银色子弹**，所以根据你的特定处境来，利弊权衡后，选择合适的设计模式。

所以，同一个app中混合多个设计模式这是正常的。比方说开始时使用MVC，之后你意识到某些特定的情况下MVC很难维持高效，然后你转向了MVVM，但也仅仅为了这个特定情况下才使用。没有必要重构别的在MVC下工作良好的部分（screen），毕竟每个模式都能很好的兼容。

> ##Make everything as simple as possible, but not simpler — Albert Einstein



