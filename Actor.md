
####Actor 定义
Actor是包含State，Behavior, Mailbox,Children和Supervisor Strategy的容器，所以这些都囊括在 actor Ref中。
#####Actor tips
最难的部分是决定 由谁来管理谁？显然是没有绝对唯一的答案，但是下面的一指导
可能有些用处：
* 如果一个actor管理另外的actor正在执行的work（例如通过传递子任务），那么
这个管理者应该supervise这个子actor；
* 如果一个actor处理非常重要的数据，则这个actor应该将任何有危险的子任务交个他的
子actor，并能handle这些子类的failures；
* 如果一个actor依赖另外的actor的执行，那么，它应该监视着那个actor的生命周期，并
在处理它结束所发来的message。

#####Actor 指导
* Actors应该像很好的工人：高效的完成自己的工作而不无理由的打扰别人避免资源
的占用和浪费。换成编程的术语就是用事件驱动方式去处理事件并产生响应。
* 不要传递可变的对象。为了确保这一点，应该使用不可变的messages。如果将
可变的状态暴露在外部，那就回到了Java concurrency 方式。
* Actors是包含行为和状态的容器。也就是说，不要在messages中发送behavior。以免
面临共享可变状态。
* 顶层actors是Error Kernel的最核心部分，所以最好用层级结构并少量的创建他们。

#####注意阻塞
有些情景下避免不了阻塞，当面临这问题是不能仅仅使用Futrure简单替代：这将很容易就
碰上线程或者内存的瓶颈。不算全面的解决方案如下：
* 在actor（或者通过router管理的一系列actors）内部处理阻塞调用时，确保建立一个适当有效的
线程池；
* 在Future中处理阻塞调用时，确保一个任何时间能够承受这些调用的上边界；
* 在Future中处理阻塞调用时，提供一个线程池限定最大线程数的上界；
* 决定用一个单线程管理一系列阻塞资源并作为actor的message派遣事件
