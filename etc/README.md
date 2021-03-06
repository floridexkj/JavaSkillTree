**早上看了一篇王者荣耀的网络同步方案的文章：**



之前做《霸三国》的时候采用 Client-Server 的模式，服务器判定客户端表现，那为什么我们在做《王者荣耀》的时候选用帧同步的方式呢？

Client-Server 模式的好处在于：

首先，安全。因为都是服务器计算，客户端只是负责表现层面的功能，不会影响各种判定的结果。

另外，Client-Server 模式因为是基于结果的表现，所以中间可以出现丢包，丢包是可以被接受和处理的，只要最终结果补发一致即可。

帧同步在端游用得比较多，大家比较熟悉的 DotA，还有《星际争霸》，都是用的帧同步技术。

帧同步本身对网络要求更加严苛，下发的执行序列是不允许丢包的，需要严格保证顺序性，包是 12345，就必须是 12345，如果丢包，必须要等到丢的包到达之后才能顺序后续执行。

MOBA 本身的单位比较多，同屏时客户端最多有将近 100 个单位，假如一个 AOE 技能打到 20 个单位，然后种了一个 debuff，Client-Server 状态模式需要发这些信息下去，可能潜在的同步状态信息是比较多的。

另外一个 Client-Server 模式本身开发的方式，客户端表现与服务器的判定，要完美的匹配是比较困难的。

我们之前做端游 MOBA 的时候，一个英雄技能我们开发要两三周的时间。《王者荣耀》当时开发周期是三、四个月，这样的时间压力下，我们用 Client-Server 的方式搞不定，时间不够。

当时团队心里会比较紧张，因为那时候市面上并没有看到用这种方式做强 PvP、高及时性手游的。

帧同步网络抗抖动能力比较弱，因为不能丢包。帧同步的基本原理，大家有兴趣可以下来自己了解一下。

一般会有区分，是网络还是主机模式。该技术的要点在于局内的运算都是基于客户端运算，10 个人中，每个人都会各自算一份，有相同的起始、相同的输入、完全相同的中间运算逻辑，不存在随机过程，这时候运算的结果，理论上应该是一致的。

甚至包括浮点数运算都不应该存在，它有精度的问题。包括很多碰撞，动画，还有基本的数学运算库都是后台自己实现的，要去浮点整形化，避免客户端的本地逻辑，这是最容易犯的错误，这是出现不同步最常见的原因。

如果某个经验不是很足的客户端程序，写程序时候用本地的代码做相应的逻辑，可能跑得越来越远，10 个人都是平行的世界。

整体的网络结构，大体看来分三层：服务器、客户端逻辑层，客户端表现层。

服务器主要负责的功能有两部分：
收集所有玩家上行的输入，把它按定时的间隔打包成输入的序列，投放给所有客户端。
当客户端出现丢包的时候，服务器进行补发；还有把客户端上行冗余的信息替换掉，比如有新的输入到了，就把老的输入 Drop 或者替换掉。
在《王者荣耀》里，我们的逻辑是 66 毫秒一次，1 秒同步 15 个包，这是不能少的，因为帧同步不能丢包，数据包必须有严格的执行序列。

客户端逻辑层理解为客户端本地的服务，就是所有客户端运行的结果必须强一致，不能有真的随机、不能有本地逻辑、不能有浮点数运算。拿到相同的输入，产生结果必须一致。

客户端表现层会根据逻辑层的数据去做 Copy 或者镜像，然后在表现层进行平滑，帧数不一样，但是不会影响最终的运算结果，只影响动画和动作的表现。

PvP 最开始上线时，我们用的是 TCP 技术。TCP 在局域网的情况下表现还是不错的，没有什么问题，但是当外网出现丢包或者抖动的时候，受限于实现方式。

比如窗口、慢启动各方面的原因，会发现当出现重连的时候游戏非常卡，所以后来我们没有用 TCP，改为了采用 UDP。如果出现丢包，服务器会在应用层做补发。

UDP 受限于 MTU（最大传输单元）的大小，大于 MTU，会出现分包，可能也会出现整包的丢失。

所以我们也会有些比较大的包会在 App 层由服务器做分包，中间出现丢包再由服务器补发，把零碎的包拼成整包再做解包。

比较有价值的是 UDP 包，如果手机因为信号抖动等出现丢包，下发的时候通过冗余方式，是比较有效的解决方法。

帧同步的消息比较小，按照理论 1 秒 15 个驱动帧来算，20 分钟的录像是 10M 左右。但是我们外网统计，正常的 5V5 对局 20 分钟，录像的大小大概是 3M 左右。

服务器会把玩家的操作做纯内存的存储，当出现丢包的时候，服务器会通过编号快速找到缓存信息进行下发。同时根据丢包的情况，我们会计算给这个人发送冗余量的变化量。

最开始发送每个包会冗余前面3帧的信息，如果丢包严重，我们会尝试冗余更多信息再下发。客户端拿到之后会尽量压缩逻辑执行的过程。

帧同步有比较麻烦的模式在于，它不像 Client-Server 的模式随进随出，崩溃之后重回必须从一开始运行，中间运算过程不能少掉。

当然，我们也尝试过其他的一些方法。比如客户端上行之后，不需要服务器定时的间隔去做收集然后下发，而是通过染色帧编号直接下发，这样响应更及时，操作反馈更强、更快。

当时我们做出来的结果是，这对手感的提升微乎其微，但是带来的负面问题却很大，因为不再是一秒 15 个包固定的下发，下发包的数量非常多，完全和这个人的操作习惯有关系。

有可能一个人一秒之内产生了十几二十个输入，就需要把这些输入打包之后对客户端下发。客户端因为收包很多，设备也会明显发烫。

我们也有和其他部门合作，做类似于 TCP 的技术，大家直观想到如果丢包就在 IO 层做重发。

但是实际的结果会发现，做的这个技术偏底层，所以对丢包的控制性不那么灵活，而且可能出来的结果还没有 TCP 本身好。

传统的帧同步的方式会做延迟投递，这个我们也有尝试过。如果间隔时间内出现丢包，或者出现包下行时的网络波动，可以通过延迟投递这种方式抹平抖动和丢包的情况。

我们尝试过这个方案但最终没有这样做的原因在于：《王者荣耀》里面一些英雄体验起来感觉偏动作，对反应要求比较快，延迟投递虽然抗抖动和抗丢包的能力确实不错，但是手感上达不到我们的要求。

另外，做 Client-Server 方式的实现，一般都会有一个套路，客户端提前表现，根据服务器的表现做平滑或者拉扯。

这个方案我们也尝试过，但最终还是放弃了，因为这个技术会让角色本身的表现有点发飘。

客户端本地动，马上客户端表现就跟着动，但根据服务器的下行，其实会做一些偏移或者修正。当网络抖动出现的时候，角色会有一点发飘，所以这个方案我们放弃掉了。

帧同步方案，所有客户端进行运算，期望产生一致的结果，但如果因为 Bug 或者某个人使用修改器，跑出来的结果会和其他人不一样，当不一样出现，我们的说法是不同步了。

我们会定时把一些关键信息提取出来做 Hash，不同步的人的 Hash 和其他人会不一样。

《王者荣耀》不同步率上线时大概是 2%，也就是 100 局可能有 2 局出现一个人或者多个人结果和其他人不一样。我们现在把不同步率做到了万分之三，一万局里面只有三局出现这个情况。

这是怎么提升的呢？如果你用帧同步一定会遇到不同步的问题，客户端写错了，用了本地逻辑，可能浮点数的运算误差达到那样的临界点，它就会产生运算结果不一致。

我们的方法有很多：自动化测试，用机器人不断跑，比如上新英雄之前，有脚本测试不断跑，看会不会产生不同步的结果；有专门的体验服、抢先服大区，发布到正式网络之前先测试，先暴露问题，再解决问题。

另外，当不同步的时候，我们会把这局整个录像和客户端间的 Log 上传和保存下来，这样可以根据录像和中间执行的日志序列快速的定位是哪个地方出现问题。

我们对延迟和单局质量也有相应的监控，这一局有没有卡或者卡多少次，有没有出现丢包，丢包多少，最大的延迟、最大的抖动是多少，我们都是有相应的记录和统计。

运营部的同学给我们提供了很多帮助，我们会有相关的网络测速、问题分析的 SDK 的合入。

按照我们自己的统计，游戏卡顿主要的原因有几个：
小区的带宽比较繁忙，很多小区其实都是公用带宽出口，比如有人在下电影、看直播，占用了很高带宽，你玩游戏就可能会卡。
Wi-Fi 路由器延迟比较高，家里的 Wi-Fi 路由器长期没有重启，就会存在终端过多、信道干扰、其他大流量的应用下载情况，这也会影响你玩《王者荣耀》。
手机信号差、信号抖动，Wi-Fi、4G 空口丢包等。

我们在网络优化上做了很多的尝试，例如根据丢包情况加大冗余，然后优化我们各方面执行的效率，去减少 CPU 的占用。

《王者荣耀》后台方面，有两个点是我们一直努力在做的，网络优化和匹配机制，我们尝试用各种各样的方法，甚至后面也会尝试用 AI 深度学习的方法，来更加精准的定位玩家本身的真实水平，让他能够匹配到更加真实的同等水平的对手和队友。


> 简书相关文章：
http://www.jianshu.com/p/fbd8eda9df62

>提到的导航推测（Dead Reckoning）算法。
相关讲解http://blog.csdn.net/czh3642210/article/details/53412875

不同平台间浮点数计算一定有差异
http://blog.csdn.net/tercel_zhang/article/details/52537726

