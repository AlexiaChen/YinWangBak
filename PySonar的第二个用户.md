湾区的世界真是机缘巧合众多。最近有人联系我，说他们做了一个代码搜索公司叫sourcegraph.com，其中的 Python 检索部分使用了 PySonar 的第一版开源代码。于是我很高兴的发现 PySonar 有了第二个用户（当然，Google 是第一个）。

Sourcegraph 的两位创始人 Quinn 和 Beyang 告诉我，PySonar 是他们试过的最精确，也是最友好的 Python 静态分析。这虽然是意料当中的，但是我还是很高兴有人发现了这一点。其实在设计 PySonar 之前我试过十多个 Python 分析器和 IDE，发现它们并不能真正的分析 Python 的语义，所以才开始从头发明。而 PySonar 的第二版采用的我自己发明的 "abstract interpretation" 超越了现有的所有静态分析器（包括所有语言的分析器在内，比如 Coverity 和 PL 学术界的 control-flow analysis 技术）。PySonar 第二版已经很接近理论的极限，所以基本不大可能有其它工具可以超过它。那里面说去说来就那么点东西，但是我刚发现很多人其实都不理解 ;-)

我也很欣赏 Quinn 和 Beyang 的聪明才智和雄心壮志。我希望 sourcegraph 发展壮大，成为世界上最优秀的代码搜索和分析网站。
