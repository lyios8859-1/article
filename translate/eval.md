## eval()不是魔鬼，只是被误解了

原文来自：[https://www.nczonline.net/blog/2013/06/25/eval-isnt-evil-just-misunderstood/](https://www.nczonline.net/blog/2013/06/25/eval-isnt-evil-just-misunderstood/) 作者：Nicholas C.Zakas

在JavaScript中，我不确定是否有比eval()受到更多诽谤的。它就是个简单的函数被设计用来将字符串转换为可被执行的JavaScript代码。在我的早期的职业生涯里，它比任何其他的东西更受关注和误解。

大多数人认为‘eval()是魔鬼’这句话是Douglas Crockford说的。他说：“eval函数（及其亲属，Function，setTimeout，和setInterval）提供对JavaScript编译器的访问。这有时是必要的，但大多数情况，它证明这个存在是极其糟糕的代码。因为eval()这个特性常常被误用”。

因为道格拉斯的大多数作品并没有注明日期，所以，我不确定他是否是在2002年创造了这个术语。不管怎么说吧，不管是否真正理解了eval()的使用，他都成为了一个热门的短语。

尽管这种理论流行开来了（道格拉斯的坚持），但这并不意味着eval()的存在就有问题。使用eval()不会自动触发XSS攻击，或者你没有意识到的但存在的安全漏洞。就像工具一样，你要知道如何使用它，但即使你使用不正确，但潜在风险依然很低，并且是可容忍的。

### 误用（滥用）

eval()之所以成为了魔鬼，是因为那些对JavaScript语言理解不够深的人误用的原因。你可能会奇怪，误用跟安全和性能好型没有关系吧。误用是不理解如何在JavaScript中构造和使用引用。假设你有几个表单input的名字包含数字，如：option1，option2，常见的代码实现如下：

```
function isChecked(optionNumber) {
    return eval("forms[0].option" + optionNumber + ".checked");
}

var result = isChecked(1);
```
在这种情况下，开发人员在尽力尝试写forms[0].option1.checked，而没有想我不用eval()该如何做。这样的情况出现在很多10年左右工作经验的开发者，它们不明白如何更好地使用。这里也不是说eval()在这里不合适，而是因为没有必要，你完全可以更简单的实现，用如下的代码：

```
function isChecked(optionNumber) {
    return forms[0]["option" + optionNumber].checked;
}

var result = isChecked(1);
```

在很多情况下，你可以使用`[]`表示法来替换eval()的使用去构造属性名，这也是 `[]` 存在的一个原因。包括道格拉斯在内的早期博主们都是在讨论这个问题。

### 可调式性

不使用eval()的一个重要理由是为了达到调试的目的。以前，如果出现问题，不可能进入eval()代码。这就意味着你的代码运行在一个黑盒中，然后从中取出。现在Chrome开发工具可以调试eval()内的代码，但是有一个问题是，你必须等代码执行一次后才出现在源面板中。

不使用eval()可以令我们的代码调试起来更容易，跟方便的查看源代码。但这并不能说明eval()是魔鬼，这只是开发工作流程中的一点问题。

### 性能

对eval()的另一个重要影响是它的性能。在旧的浏览器中，你遇到了双重解释惩罚，也就是说，你的代码被解释，而eval()中的代码被解释。在没有编译JavaScript引擎的浏览器中，结果可能会慢十倍(甚至更糟)。

在现代编译JavaScript的引擎中，eval()仍然是一个问题。大多数引擎可以用两种方式运行代码:快速路径或慢路径。快速路径代码是一种稳定且可预测的代码，因此可以为更快的执行而编译。缓慢的路径代码是不可预测的，这使得编译很难，并且可能仍然使用一个解释程序运行。在你的代码中仅仅存在eval()意味着它是不可预测的，因此将在解释器中运行它以“旧浏览器”的速度运行，而不是“新浏览器”的速度(10倍的差异)。

同样的，eval()使YUI压缩器不可能在调用eval()的范围内munge变量名。由于eval()可以直接访问任何这些变量，重命名它们会引入错误(其他工具如闭包编译器和UglifyJS可能仍然会蒙混这些变量——最终导致错误)。

因此，在使用eval()时，性能仍然是一个大问题。但这很难让它成为邪恶，但这是一个需要注意的警告。

### 安全

在说到eval()的安全时，这是大部分人认为eval是魔鬼的有力佐证。大多数情况下，就是说XSS攻击，以及eval()如何向它们打开代码。在表面上，这种混淆是可以理解的，因为eval()在页面上下文中可执行任意代码。如果你接受用户输入并通过eval()运行它，这将是危险的。但是，如果你的输入不是来自用户，是否存在真正的危险?

我收到了不止一个的抱怨，在我的CSS解析器中使用eval()的一段代码中使用的代码使用eval()将字符串标记从CSS转换为JavaScript字符串值。除了创建自己的字符串解析器外，这是获得token的正确字符串值的最简单方法。到目前为止，还没有人能够或愿意提出一种攻击方案，在这种情况下，这段代码会引起麻烦，因为:

* eval()的值来自于tokenizer
* tokenizer已经验证了它是一个有效的字符串
* 代码最常在命令行上运行。
* 即使在浏览器中运行，该代码也被封闭在闭包中，不能直接调用。

当然，由于这段代码的主要目标是命令行，所以这个故事有点不同。

设计用于浏览器的代码面临不同的问题，但是eval()的安全性通常不是其中之一。同样，如果你以某种方式接收用户输入并将其传递给eval()，那么你就是在自找麻烦，不要这样做。但是，如果你使用eval()的输入，只有你控制并且不能被用户修改，那么就没有安全风险。

最常见的攻击是来自服务器的eval()代码。这一模式以引入JSON而著名，它之所以流行，是因为它可以通过eval()快速转换成JavaScript。实际上，Douglas Crockford自己在他的原始JSON实用程序中使用eval()，因为它可以转换速度。他确实添加了检查以确保没有真正的可执行代码，但是实现从根本上是eval()。

现在，大多数人都使用浏览器内置的JSON解析功能来实现这一目的，尽管有些人仍然通过eval()来获取任意的JavaScript，作为延迟加载策略的一部分。一些人认为，这才是真正的安全漏洞。如果正在进行中间人攻击，那么你将在页面上执行任意攻击代码。

中间人攻击被认为是eval()的永远存在的危险，会受到蠕虫的的攻击。但是，这是一个与我无关的场景，因为任何时候你不能相信你正在联系的服务器，就意味着有可能出现很多不好的事情。中间人攻击可以通过多种方式向页面注入代码:

* 返回通过 `<script></script>` 加载的JavaScript代码。
* 通过返回攻击者控制的JSON-P请求代码。
* 通过从一个Ajax请求返回attacker控制的代码，然后eval()。

此外，这样的攻击可以很容易地窃取cookie和用户数据，而不会改变任何东西，更不用说通过返回攻击者控制的HTML和CSS来进行网络钓鱼的可能性了。

简单地说，eval()不会像加载外部JavaScript那样打开中间人攻击。如果你不能信任服务器上的代码，那么你将遇到比eval()调用更大的问题。

### 结论

我不是说你应该跑出去，开始使用eval()。实际上，很少有好的用例来运行eval()。对于代码清晰性、调试性，以及不应该忽略的性能，确实存在一些问题。但是在eval()有意义的情况下，你不应该害怕使用它。不要第一次使用它，但是不要让任何人吓到你，认为你的代码在使用eval()时更加脆弱或不安全。
