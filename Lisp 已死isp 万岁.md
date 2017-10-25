# Lisp 已死, Lisp 万岁！

作者：王垠


之前我提到了 Lisp（Scheme）的很多优点，可是似乎从来没提它的缺点，所以我打算把剩下的话说一说。今天我就讲一下为什么 Lisp Machine 会失败，以及为什么 Lisp 现在不是很流行。


你也许已经知道，Lisp 身上最重要的一些优点，其实已经“遗传”到了几乎每种流行的语言（Java，C#，JavaScript，Python, Ruby，Haskell，……）身上，所以 Lisp 其实是长生不老的。这就是为什么我说“Lisp 万岁”。


由于我已经详细叙述过其中一些优点，所以我现在只把这些 Lisp 的优点简单列出来（关键部分加了链接），然后接下去讲一下某些 Lisp “方言”的历史遗留缺点。


    Lisp 的语法是世界上最精炼，最简单，最美观，也是语法分析起来最高效的语法。这是 Lisp 独一无二的，其他语言都没有的优点。有些人喜欢设计看起来很炫的语法，其实都是自找麻烦。为什么这么说呢，请参考这篇《谈语法》。

    Lisp 是第一个可以在程序的任何位置定义函数，并且可以把函数作为值传递的语言。这样的设计使得它不存在很多面向对象语言的繁琐（比如 Java 的“设计模式”）。很多语言（比如 JavaScript，Ruby 和 Python）试图学习这种功能，可惜几乎都没有正确的实现。

    Lisp 有世界上最强大的宏系统（macro system）。没有任何其它语言可以有超越 Lisp 的宏，因为这种宏系统的设计已经达到了理论的极限。如果你只见过 C 语言的“宏”，那我可以告诉你，它是完全没法跟 Lisp 相提并论的。Haskell 和 MetaOCaml 引入了自己的宏系统，但是它们的语法使得这些宏系统用起来非常蹩脚。

    Lisp 是世界上第一个使用垃圾回收（garbage collection）的语言。这种超前的功能后来被 Java，C# 等语言借鉴。


想不到吧，现代语言的很多优点，其实都是来自于 Lisp — 世界上第二古老的程序语言。所以有人才会说，每种现代语言都在朝着 Lisp 的方向“进化”。如果你相信了这话，也许就会疑惑，为什么 Lisp 和 Lisp Machine 今天没有成为主流。其实除了商业原因之外，还有技术上的问题。这里我就来说说这些被 Lisp 狂热分子抹杀的细节。


早期的 Lisp 其实普遍存在一个非常严重的问题：它使用 dynamic scoping。所谓 dynamic scoping 就是说，如果你的函数定义里面有“自由变量”，那么这个自由变量的值，会随着函数的“调用位置”的不同而发生变化。


比如下面我定义一个函数 f，它接受一个参数 y，然后返回 x 和 y 的积。


(setq f

      (let ((x 1)) 

        (lambda (y) (* x y))))


这里 x 对于 (lambda (y) (* x y)) 函数来说是个“自由变量”（free variable），因为它不是它的参数。


看着这段代码，你会很自然的认为，因为 x 的值是 1，那么 f 被调用的时候，结果应该等于 (* 1 y)，也就是说应该等于 y 的值。可是这在 dynamic scoping 的语言里结果如何呢？我们来看看吧。


（你可以在 emacs 里面试验以下的结果，因为 Emacs Lisp 使用的就是 dynamic scoping。）


我们在函数调用的外层把 x 绑定到 2：


(let ((x 2))

  (funcall f 2))


结果返回 4 （本来期望的是 2）。


再来，把 x 绑定到 3：


(let ((x 3))

  (funcall f 2))


结果返回 6 （本来期望的还是 2）。


看到问题了吗？因为这个函数的行为会随着外部变量的值而改变，这会导致非常难以发现的错误，这也就是早期的 Lisp 最令人头痛的地方。我的老师 Dan Friedman 当年就为此痛苦了很多年，直到 Scheme 的出现，他才拍案而起大叫道：“终于有人把它给做对了！！”


（附带说一句，Scheme 不是 Dan Friedman 发明的，而是 Guy Steele 和 Gerald Sussman。然而，Friedman 对程序语言的本质理解，其实超越了 Lisp 的范畴，并且对 Scheme 的后期设计做出了重要的贡献。以至于 Sussman 在 Friedman 的 60 大寿时发表演说，戏称自己比起 Friedman 来，“只是 Scheme 的用户”。）


好在现在的大部分语言其实已经吸取了这个教训，所以你不会再遇到这种让人发疯的痛苦。不管是 Scheme, Common Lisp, Haskell, OCaml, Python, JavaScript…… 都不使用 dynamic scoping。


那现在也许你了解了，什么是让人深恶痛绝，千刀万剐的 dynamic scoping。如果我告诉你，Lisp Machine 所使用的语言 ZetaLisp（也叫 Lisp Machine Lisp）使用的也是 dynamic scoping，你也许就明白了为什么 Lisp Machine 会失败，因为它跟现在的 Common Lisp 和 Scheme，真的是天壤之别。我宁愿写 C++，Java 或者 Python，也不愿意写 ZetaLisp 或者 Emacs Lisp！

话说回来，为什么早期的 Lisp 会使用 dynamic scoping 呢？这是因为使用 dynamic scoping，会让函数实现非常容易。如果你在 emacs 里面显示以上定义的 f 的值，它会打印出：'(lambda (y) (* x y))。


你发现什么问题了吗？这个 f 的值，其实是一个 S 表达式，而不是像 Scheme 一样的“函数对象”（或者叫“闭包”, closure）。原来，Emacs Lisp 直接把函数定义处的 S 表达式 ‘(lambda (y) (* x y)) 作为了函数的“值”，这是一种幼稚的做法。如果你是个程序语言设计的门外汉，第一次实现函数的时候，你很有可能就这样做。Lisp 的设计者当年也跟你一样的想法。所以 dynamic scoping 根本就不是一个有意的“设计”，而是一个无意的“巧合”。他们其实什么也没有做，就成那个样子了。


简单倒是简单，麻烦事接着就来了。调用 f 的时候，比如 (funcall f 2)，y 的值当然来自参数 2，可是 x 的值是多少呢？答案是：不知道！不知道怎么办？到“外层环境”去找呗。所以你就看到了之前出现的现象，外面的 x 等于几，函数的返回值就随之变化。


那么正确的实现函数定义的做法是什么呢？是制造“闭包”(closure)。这也就是 Scheme，Common Lisp 以及 Python，C# 的做法。在函数定义被解释或者编译的时候，当时的自由变量（比如 x）的值，会和函数的代码绑在一起，被放进一种叫做“闭包”的结构。比如上面的函数，就可以表示成这个样子：(Closure '(lambda (y) (* x y)) '((x . 1)))。


在这里我用 (Closure ...) 表示一个“结构”（就像 C 语言的 struct）。它的第一个部分，是这个函数的定义。第二个部分是 '((x . 1))，它是一个“环境”，其实就是一个从变量到值的映射（map）。利用这个映射，我们才能记住函数定义里的那个 x 在“定义时间”的值。(x . 1) 的意思就是“x 的值是 1”。


我不想在这里深入细节。如果你对实现语言感兴趣的话，可以参考我的另一篇博文《怎样写一个解释器》。它教你如何实现一个正确的，没有以上毛病的解释器。


与 dynamic scoping 相对的就是“lexical scoping”。我刚才告诉你的闭包，就是 lexical scoping 的实现方法。第一个实现 lexical scoping 的语言，其实不是某种 Lisp，而是 Algol 60。“Algol”之所以叫这名字，是因为它的设计初衷是用来实现算法（algorithm）。其实 Algol 比起 Lisp 有很多不足，但在 lexical scoping 这一点上它却做对了。Scheme 从 Algol 60 身上学到了 lexical scoping，成为了第一个使用 lexical scoping 的“Lisp 方言”。9 年之后，Lisp 家族的“集大成者” Common Lisp 诞生了，它也采用了 lexical scoping。看来英雄所见略同。

你也许发现了，Lisp 其实不是一种语言，而是很多种语言。这些被人叫做“Lisp 家族”的语言，其实共同点只是它们的“语法”：它们都是基于 S 表达式。如果你因此对它们同样赞美的话，那么你赞美的其实只是 S 表达式。如果你不相信的话，我就告诉你，我曾经给 Java，C++ 和 TeX 设计了 Lisp 的语法。比如我的 SchTeX 文件（Scheme + TeX）看起来是这个样子：


(documentclass article (11pt))

(document

  (abstract (...))

  (section (First Section)

      ... )

  (section (Second Section)

      ... )

)


虽然这样它看起来是 Lisp，本质却跟 Scheme 有天壤之别。所以人们把 Scheme 叫做 Lisp 的“方言”，其实是非常不准确的做法。Scheme 和 Emacs Lisp，Common Lisp 其实是非常不同的三种语言。Racket 曾经叫做 PLT Scheme，但是它也跟 Scheme 有较大的区别。所以现在 PLT 把它改名为 Racket，是有他们的道理的。


综上所述，到现在看来 Lisp Machine 的失败，其实是死有余辜，却又死而无憾的。它的失败，其实还有另外一个重要的原因，那就是因为早期 Lisp 的编译器生成的代码效率非常低下。这个问题，我留到下一篇博文再讲。
