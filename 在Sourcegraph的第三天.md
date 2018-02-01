这个星期一我就正式加入 Sourcegraph 了。做自己感兴趣的东西真是不感觉累。这里没有人是老板，每个人都是老板，每个人都写很不错的代码。虽然每天工作的时间比起在 Voxer 多了很多，但是感觉也好了很多。Quinn 和 Beyang 每天都工作到半夜，但是仍然每天精神饱满。我比他们稍微偷懒一些。工作不是因为有压力，而是有一种像打电玩一样的“瘾”，这样的工作还能叫工作吗？那叫“玩物不丧志” :-)

我们的条件是艰苦的，办公室又小又乱。虽然条件很快就会改善，我却一点也不在乎这个。Steve Jobs 不也是在车库里创造了 Apple 吗？这才像是个 startup 嘛。我们的目标是：精确的，按语义的搜索全世界的代码，成为程序语言界的 Google。

(上图为我们的 co-founder 和 CTO Beyang 同学，他非常能吃，冰箱里的剩菜都是他吃掉的。还吃不饱就拿电炉煮泡面。）

Sourcegraph 的系统界面做得相当干净友好，所以来到这里的第三天，我就已经成功的把 PySonar2 加入到了 Sourcegraph 里面，并且改进了 PySonar2 的很多代码。不久你就可以在 sourcegraph.com 上面看到你的 GitHub Python 代码的类型。我还会把 PySonar2 移植给 Ruby 和其他一些语言。

另外，我推荐 Quinn 和 Beyang 一天之间写出来的小玩意：sourcegraph 的 Chrome 插件。安装之后，当你浏览 GitHub 的 repo 的时候，就会自动显示出 Sourcegraph 为你索引出的引用次数最多的那些代码：

PySonar2 其实具有一种非常强大的类型系统，包含了 intersection type, union type 等类型，这些都是 Java, Haskell, OCaml 等语言不具有的灵活而强大的类型。PySonar2 也可以推导出高阶函数类型。

比如下面这个例子是 Flask 框架的 route 函数（显示在我修改过的本地 Sourcegraph 网站上）：

PySonar2 推导出它的类型是：
(Flask, str) -> (() -> str) -> () -> str

这里是一个高阶函数，它表示： route 接受一个 Flask  对象 (self) 和一个 str，然后生成一个中间函数。这个函数接受另一个函数（类型为 () -> str），然后又生成一个函数。这个函数被调用之后，会返回一个 str。这就是为什么你可以这样调用它：
app.route('/')(index)

其中 index 函数的类型是 () -> str（如图所示）。它不接受参数，返回一个 str。

目前这个功能还不在主网站上面。等完全调试通过后，不久就会放到上面。
