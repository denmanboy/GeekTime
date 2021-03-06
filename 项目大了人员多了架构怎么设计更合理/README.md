# 扩展阅读

[iOS 设计模式相关资料整理](https://github.com/skyming/Trip-to-iOS-Design-Patterns)

[23种设计模式iOS实现](https://github.com/huang303513/Design-Pattern-For-iOS)

# 项目大了人员多了，架构怎么设计更合理？

将业务完全解耦，将通用功能下沉，每个业务都是一个独立的 Git 仓库，每个业务都能够生成一个 Pod 库，最后再集成到一起。

架构重构考虑得越晚，重构起来就越困难，快速迭代的需求开发和漫长的重构之间的矛盾，如同在飞行的飞机上换引擎。及早考虑架构设计就显得尤为重要。

## 大项目、多人、多团队架构思考
iOS 组件，应该是包含 UI 控件、相关多个小功能的合集，是一种粒度适中的模块。

我们可以先按照物理划分，也就是将多个相同功能的类移动到同一个文件夹下，然后做成 CocoaPods 的包进行管理。然后再解除耦合关系。

### 层级

* 底层可以是与业务无关的基础组件，比如网络和存储等；
* 中间层一般是通用的业务组件，比如账号、埋点、支付、购物车等；
* 最上层是迭代业务组件，更新频率最高

不用把所有的功能都做成组件，只有那些会被多个业务或者团队使用的功能模块才需要做成组件。因为，改造成组件也是需要时间成本的，很少有公司愿意完全停下业务去进行重构，而一旦决定某业务功能模块要改成组件，就要抓住机会，严格按照 SOLID 原则去改造组件，因为返工和再优化的机会可能不会再有。

## 我心目中好的架构是什么样的？

协议式架构设计主要采用的是协议式编程的思路：在编译层面使用协议定义规范，实现可在不同地方，从而达到分布管理和维护组件的目的。这种方式也遵循了依赖反转原则，是一种很好的面向对象编程的实践。

但是，这个方案的缺点也很明显，主要体现在以下两个方面：

1. 由于协议式编程缺少统一调度层，导致难于集中管理，特别是项目规模变大、团队变多的情况下，架构管控就会显得越来越重要。

2. 协议式编程接口定义模式过于规范，从而使得架构的灵活性不够高。当需要引入一个新的设计模式来开发时，我们就会发现很难融入到当前架构中，缺乏架构的统一性。

虽然协议式架构有这两方面的局限性，但由于其简单易用的特点依然被很多公司采用。

**另一种常用的架构形式是中间者架构。它采用中间者统一管理的方式，来控制 App 的整个生命周期中组件间的调用关系**。同时，iOS 对于组件接口的设计也需要保持一致性，方便中间者统一调用。

但是，这种运行时直接硬编码的调用方式也有些缺点，主要表现在两个方面：

1. 直接硬编码的调用方式，参数是以 string 的方法保存在内存里，虽然和将参数保存在 Text 字段里占用的内存差不多，同时还可以避免.h 文件的耦合，但是其对代码编写效率的降低也比较明显。

2. 由于是在运行时才确定的调用方法，调用方式由 [obj method] 变成 [obj performSelector:@""]。这样的话，在调用时就缺少类型检查，是个很大的缺憾。因为，如果方法和参数比较多的时候，代码编写效率就会比较低。

在考虑架构设计时，我们**更多的还是需要在功能逻辑和组件划分上做到同层级解耦合，上下层依赖清晰，这样的结构才能够使得上层组件易插拔，下层组件更稳固。而中间者架构模式更容易维护这种结构，中间者的易管控和易扩展性，也使得整体架构能够长期保持稳健与活力。所以中间者架构就是我心目中好的架构**。

