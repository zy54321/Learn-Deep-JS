[深入理解JavaScript，这一篇就够了](https://www.cnblogs.com/feng-gamer/p/5907926.html)
[TOC]

### 对象和原型链
JavaScript 是一门基于对象的编程语言，在 JavaScript 中一切都是对象，包括函数，也是被当成第一等的对象对待，这正是 JavaScript 极其富有表现力的原因。在 JavaScript 中，创建一个对象可以这么写：
```
var someThing = new Object();
```
　　这和在其它面向对象的语言中使用某个类的构造函数创建一个对象是一模一样的。但是在 JavaScript 中，这不是最推荐的写法，使用对象字面量来定义一个对象更简洁，如下：
```
var anotherThing = {};
```
　　这两个语句其本质是一样的，都是生成一个空对象。对象字面量也可以用来写数组以及更加复杂的对象，这样：
```
var weekDays = ["Monday", "Tuesday", "Wednesday", "Thursday", "Friday", "Saturday", "Sunday"];
```
　　这样：
```
var person = {
    name : "youxia",
    age : 30,
    gender : "male",
    sayHello : function(){ return "Hello, my name is " + this.name; }
}
```
　　甚至这样数组和对象互相嵌套：
```
var workers = [{name : "somebody", speciality : "Java"}, {name : "another", speciality : ["HTML", "CSS", "JavaScript"]}];
```
　　需要注意的是，对象字面量中的分隔符都是逗号而不是分号，而且即使 JavaScript 对象字面量的写法和 JSON 的格式相似度很高，但是它们还是有本质的区别的。

　　在我们捣鼓 JavaScript 的过程中，工具是非常重要的。我这里介绍的第一个工具就是 Chromium 浏览器中自带的 JavaScript 控制台。在 Ubuntu 中安装 Chromium 浏览器只需要一个命令就可以搞定，如下：
```
sudo apt-get install chromium
```
　　启动 Chromium 浏览器后，只需要按 F12 就可以调出 JavaScript 控制台。当然，在菜单中找出来也可以。下面，让我把上面的示例代码输入到 JavaScript 控制台中，一是可以看看我们写的代码是否有语法错误，二是可以看看 JavaScript 对象的真面目。如下图：
　　
![image](https://images2015.cnblogs.com/blog/16576/201609/16576-20160905213836238-96163993.png)

对于博客园中广大的前端攻城狮来讲，Chromium 的 JavaScript 控制台已经是一个烂大街的工具了，在控制台中写console.log("Hello, World!");就像是在 C 语言中写printf("Hello, World!");一样成为了入门标配。在控制台中输入 JavaScript 语句后，一按 Enter 该行代码就立即执行，如果要输入多行代码怎么办呢？一个办法就是按 Shift+Enter 进行换行，另外一个办法就是在别的编辑器中写好然后复制粘贴。其实在 Chromium 的 JavaScript 控制台中还有一些不那么广泛流传的小技巧，比如使用console.dir()函数输出 JavaScript 对象的内部结构，如下图：

![image](https://images2015.cnblogs.com/blog/16576/201609/16576-20160905214616254-450258957.png)

从图中，可以很容易看出每一个对象的属性、方法和原型链。

　　和其它的面向对象编程语言不同， JavaScript 不是基于类的代码复用体系，它选择了一种很奇特的基于原型的代码复用机制。通俗点说，如果你想创建很多对象，而这些对象有某些相同的属性和行为，你为每一个对象编写单独的代码肯定是不合算的。在其它的面向对象编程语言中，你可以先设计一个类，然后再以这个类为模板来创建对象。我这里称这种方式为经典的面向对象体系。而在 JavaScript 中，解决这个问题的方式是把一个对象作为另外一个对象的原型，拥有相同原型的对象自然拥有了相同的属性和行为。对象拥有原型，原型又有原型的原型，最终构成一个原型链。当访问一个对象的属性或方法的时候，先在对象本身中查找，如果找不到，则到原型中查找，如果还是找不到，则进一步在原型的原型中查找，一直到原型链的最末端。在现代 JavaScript 模式中，硬是用函数、闭包和原型链模拟了经典的面向对象体系。

　　原型这个概念本身并不复杂，复杂的是 JavaScript 中的隐式原型和函数对象。什么是隐式原型，就是说在 JavaScript 中不管你以什么方式创建一个对象，它都会自动给你生成一个原型对象，我们的对象中，有一个隐藏的__proto__属性，它指向这个自动生成的原型对象；并且在 JavaScript 中不管你以什么方式创建一个对象，它最终都是从构造函数生成的，以对象字面量构造的对象也有构造函数，它们分别是Object()和Array()，每一个构造函数都有一个自动生成的prototype属性，它也指向那个自动生成的原型对象。而且在 JavaScript 中一切都是对象，构造函数也不例外，所以构造函数既有prototype属性，又有__proto__属性。再而且，自动生成的原型对象也是对象，所以它也应该有自己的原型对象。你看，说起来都这么拗口，理解就更加不容易了，更何况 JavaScript 中还内置了Object()、Array()、String()、Number()、Boolean()、Function()这一系列的构造函数。看来不画个图是真的理不顺了。下面我们来抽丝剥茧。

　　先考察空对象someThing，哪怕它是以对象字面量的方式创建的，它也是从构造函数Object()构造出来的。这时，JavaScript 会自动创建一个原型对象，我们称这个原型对象为Object.prototype，构造函数Object()的prototype属性指向这个对象，对象someThing的__proto__属性也指向这个对象。也就是说，构造函数Object()的prototype属性和对象someThing的__proto__属性指向的是同一个原型对象。而且，这个原型对象中有一个constructor属性，它又指回了构造函数Object()，这样形成了一个环形的连接。如下图：
　　
![image](https://images2015.cnblogs.com/blog/16576/201609/16576-20160908200826051-445142356.png)

要注意的是，这个图中所显示的关系是对象刚创建出来的时候的情况，这些属性的指向都是可以随意修改的，改了就不是这个样子了。下面在 JavaScript 控制台中验证一下上图中的关系：

![image](https://images2015.cnblogs.com/blog/16576/201609/16576-20160905223438644-390504374.png)

　　请注意，构造函数Object()的prototype属性和__proto__属性是不同的，只有函数对象才同时具有这两个属性，普通对象只有__proto__属性，而且这个__proto__属性是隐藏属性，不是每个浏览器都允许访问的，比如 IE 浏览器。下面，我们来看看 IE 浏览器的开发者工具：

![image](https://images2015.cnblogs.com/blog/16576/201609/16576-20160905225424941-428542560.png)

这是一个反面教材，它既不支持console.dir()来查看对象，也不允许访问__proto__内部属性。所以，在后面我讲到继承时，需要使用特殊的技巧来避免在我们的代码中使用__proto__内部属性。上面的例子和示意图中，都只说构造函数Object()的prototype属性指向原型对象，没有说构造函数Object()的__proto__属性指向哪里，那么它究竟指向哪里呢？这里先留一点悬念。

　　下一步，我们自己创建一个构造函数，然后使用这个构造函数创建一个对象，看看它们之间原型的关系，代码是这样的：
```
function Person(name, age, gender){
    this.name = name;
    this.age = age;
    this.gender = gender;
}
Person.prototype.sayHello = function(){ return "Hello, my name is " + this.name; };
var somebody = new Person("youxia", 30, "male");
```
　　输入到 Chromium 的 JavaScript 控制台中，然后使用console.dir()分别查看构造函数Person()和对象somebody，如下两图：
　　

![image](https://images2015.cnblogs.com/blog/16576/201609/16576-20160905231503160-940338372.png)

![image](https://images2015.cnblogs.com/blog/16576/201609/16576-20160905231514754-1128127188.png)

　　用图片来表示它们之间的关系，应该是这样的：

![image](https://images2015.cnblogs.com/blog/16576/201609/16576-20160908200843644-1649302056.png)

我使用蓝色表示构造函数，黄色表示对象，如果是 JavaScript 自带的构造函数和 prototype 对象，则颜色深一些。从上图中可以看出，构造函数Person()有一个prototype属性和一个__proto__属性，__proto__属性的指向依然留悬念，prototype属性指向Person.prototype对象，这是系统在我们定义构造函数Person()的时候，自动创建的一个和构造函数Person()相关联的原型对象，请注意，这个原型对象是和构造函数Person()相关联的原型对象，而不是构造函数Person()的原型对象。当我们使用构造函数Person()创建对象somebody时，somebody的原型就是这个系统自动创建的原型对象Person.prototype，就是说对象somebody的__proto__属性指向原型对象Person.prototype。而这个原型对象中有一个constructor属性，又指回构造函数Person()，形成一个环。这和空对象和构造函数Object()是一样的。而且原型对象Person.prototype的__proto__属性指向Object.prototype。如果在这个图中把空对象和构造函数Object()加进去的话，看起来是这样的：

![image](https://images2015.cnblogs.com/blog/16576/201609/16576-20160908201222488-2105593911.png)

有点复杂了，是吗？不过这还不算最复杂的，想想看，如果把JavaScript 内置的Object()、Array()、String()、Number()、Boolean()、Function()这一系列的构造函数以及与它们相关联的原型对象都加进去，会是什么情况？每一个构造函数都有一个和它相关联的原型对象，Object()有Object.prototype，Array()有Array.prototype，依此类推。其中最特殊的是Function()和Function.prototype，因为所有的函数和构造函数都是对象，所以所有的函数和构造函数都有构造函数，而这个构造函数就是Function()。也就是说，所有的函数和构造函数都是由Function()生成，包括Function()本身。所以，所有的构造函数的__proto__属性都应该指向Function.prototype，前面留的悬念终于有答案了。如果只考虑构造函数Person()、Object()和Function()及其关联的原型对象，在不解决悬念的情况下，图形是这样的：

![image](https://images2015.cnblogs.com/blog/16576/201609/16576-20160908200914707-1041290374.png)

可以看到，每一个构造函数和它关联的原型对象构成一个环，而且每一个构造函数的__proto__属性无所指。通过前面的分析我们知道，每一个函数和构造函数的__proto__属性应该都指向Function.prototype。我用红线标出这个关系，结果应该如下图：

![image](https://images2015.cnblogs.com/blog/16576/201609/16576-20160908200952301-1531409732.png)

　　如果我们画出前面提到过的所有构造函数、对象、原型对象的全家福，会是个什么样子呢？请看下图：


![image](https://images2015.cnblogs.com/blog/16576/201609/16576-20160908201004941-1139737223.png)

### 作用域链、上下文环境和闭包
关于变量的作用域这个问题应该不用多讲，凡是接触编程的童鞋，无不都要从这个基础的概念开始。变量作用域的通用规则其实很简单，无非三条：1.内层的代码可以访问外层代码定义的变量，外层代码不能访问内层代码定义的变量；2.变量要先定义后使用；3.退出代码的作用域时，变量会被销毁。以 C 语言代码为例：
```
int a0 = 0;
{
    int a1 = 1;
    printf("%d\n", a0);  //可以访问外层变量，打印 0
    printf("%d\n", a2);  //错误，变量 a2 还没定义呢
    int a2 = 2;         //变量要先定义后使用
}
/* 而且，退出作用域后，变量 a1 和 a2 会被自动销毁 */
printf("%d\n", a1);      //错误，外层代码不能访问内层变量
```
但是在 JavaScript 中，以上三条规则都有可能会被打破。从现在开始，我们就要开始踩坑了，在 JavaScript 语言满满的陷阱中，关于变量这一块的最多。首先第一个坑， JavaScript 中没有块作用域，只有函数作用域。也就是说，要在 JavaScript 中实现以上类似 C 语言的效果，我们的代码应该这样写：

```
var a0 = 0;
function someFunc(){
    var a1 = 1;
    console.log(a1); //可以访问外层变量，打印 0
    console.log(a2); //你以为会出现错误，因为变量没有定义，但是你错了，这里不会发生错误，而是打印 undefined
    var a2 = 2;
}
someFunc();
/* someFunc()执行完之后，变量 a1 和 a2 会被自动销毁 */
console.log(a1);      //错误，外层代码不能访问内层变量
```
　　把这段代码复制到控制台中验证一下，我就不截图了，毕竟我这是一篇超长的熊文，图片太多会被骂的，大家自己验证就可以了。注意，定义函数后需要调用它，函数内的代码才会执行，为了方便，我以后把它写成定义完后立即调用的自执行格式。这里碰到的第二个坑就是变量提升，在 JavaScript 中，你本以为没有定义变量 a2 就使用会出现错误，哪知道定义在后面的var a2 = 2;被提升到代码块的前面了，结果就输出 undefined。把上面的例子稍微改一改，就可以看到经典的变量提升的坑，如下：

```
var a0 = 0;
(function (){
    var a1 = 1;
    console.log(a0); //本以为会访问外层变量a0，打印 0，哪知道定义在后面的 var a0 = 1; 被提升了，所以打印 undefined
    var a0 = 1;
})();  //为了省事，写成匿名函数自执行格式
console.log(a1);      //错误，外层代码不能访问内层变量
```
　　本以为这里会访问外层变量a0，打印 0，哪知道定义在后面的 var a0 = 1; 被提升了，所以打印 undefined。为什么是 undefined 而不是 1 呢？那是因为变量提升只是提升了变量的定义，没有提升变量的赋值。不仅变量定义会被提升，函数定义也会被提升，这也是一个经典的坑。如下代码：

```
if(true){    //因为条件恒为true，所以肯定会执行这个分支
    function someFunc(){
        console.log("true");
    }
}else{
    function someFunc(){
        console.log("false");
    }
}
someFunc(); //本以为会输出 true，结果却输出 false，就是因为定义在 else 分支中的函数被提升了，覆盖了定义在 true 分支中的函数
```
　　当然，以上 Bug 只会在部分浏览器中出现，在 Chromium 和 FireFox 中还是能正确输出 true 的。为了避免函数定义的提升造成的问题，在这种情况下，我们可以使用函数表达式而不是函数定义，代码如下：

```
if(true){    //因为条件恒为true，所以肯定会执行这个分支
    var someFunc = function(){
        console.log("true");
    }
}else{
    var someFunc = function(){
        console.log("false");
    }
}
someFunc();
```
关于函数定义和函数表达式的区别，我这里就不深入讨论了。

　　内层代码可以访问外层变量，所以内层代码在访问一个变量的时候，会从内层到外层逐层搜索该变量，这就是变量作用域链，理解这一点有时有助于我们优化 JavaScript 代码的执行速度，对变量的搜索的路径越短，代码执行就越快。另外，除了全局变量外，定义在函数内部的变量只有在函数执行的时候后，这个变量才会被创建，这就是执行上下文，装逼说法叫 context，每一个函数执行的时候就会创建一个 context。前面提过，在 C 语言中，一个代码块退出的时候，这个代码块的 context 和里面的变量也会被销毁，但是在 JavaScript 函数执行结束后，函数的 context 和里面的变量会被销毁吗？那可不一定哦。如果一个函数中定义的变量被捕获，那么这个函数的 context 和里面的变量就会保留，比如闭包。这个不叫坑，叫语言特性。

　　在博客园中，有很多人写闭包，但是都写得无比复杂，定义也不是很准确。其实闭包就是定义在内层的函数捕获了定义在外层函数中的变量，并把内层函数传递到外层函数的作用域之外执行，则外层函数的 context 不能销毁，就形成了闭包。把内层函数传递到外层函数的作用域之外有很多方法，最常见的是使用return，其它的方法还有把内层函数赋值给全局对象的属性，或者设置为某个控件的事件处理程序，甚至使用setTimeout和setInterval都可以。

　　其实闭包并不是 JavaScript 语言特有的概念，只要是把函数当成头等对象的语言都有。C 语言和早期的 C++ 和 Java 没有，想想看，我们根本就没办法在上述语言中定义函数内部的函数。不过自从 C++ 和 Java 引入了 lambda 表达式之后，就有了闭包的概念了。

　　下面，我们来探索 JavaScript 中的函数执行上下文和闭包。为了印象深刻，我这里定义了一个嵌套四层的函数，函数first()返回定义在first()内的second()，second()返回定义在second()内的third()，third()再返回一个匿名函数，代码如下：

```
var a0 = 0;
var b0 = "Global context";
    
function first(){
    var a1 = 1;
    var b1 = "first() context";
    
    function second(){
        var a2 = 2;
        var b2 = "second() context";
    
        function third(){
            var a3 = 3;
            var b3 = "third() context";
    
            return function(){
                var a4 = 4;
                var b4 = "what's matter, can I see it?";
                console.log([ a1, a2, a3, a4 ]);
                console.log([ b1, b2, b3, b4]);
            }
        }
        return third;
    }
    return second;
}
```
然后，调用var what = first()()();返回最内层的匿名函数，使用console.dir(what)；来查看这个匿名函数，如下图：

![image](https://images2015.cnblogs.com/blog/16576/201609/16576-20160911204728983-602940438.png)

从图中可以看到，返回的最内层函数被命名为function anonymous()，其中有一个<function scope>属性，将它展开，可以看到由于function anonymous()对外层变量a1、a2、a3、b1、b2、b3的捕获而产生了三个 Closure，也就是闭包，而function anonymous()不仅可以访问这三个闭包中的变量，还可以访问 Global 中的变量。

　　下面问题来了，为什么我们看不到我们定义的变量a4和b4呢？因为a4和b4只有在function anonymous()被执行后才会产生。我们这里只是返回了function anonymous()，还没有执行它呢。其实就算执行它我们也看不到变量a4和b4所在的 context，因为函数的执行总是一闪而过，如果没有形成闭包，函数一执行完该 context 就销毁了。除非我们能让该函数执行到快完的时候定住。有什么办法呢？你是不是想到了调试器？只要我们在这个函数中设置一个 breakpoint，是不是就可以看到它的 context 了呢？

　　Chromium 当然是自带调试功能的。不过要想在 Chromium 中调试代码就得把以上 JavaScript 代码加到 HTML 页面中。我懒得这么做。这里，我就要祭出 Node.js 和 Visual Studio Code 了。在 Ubuntu 中安装 Node.js 非常方便，只需要使用如下命令：

sudo apt-get install nodejs
sudo apt-get install nodejs-legacy
　　为什么要安装nodejs-legacy呢？那是因为nodejs中的命令是nodejs，而nodejs-legacy中的命令是node，同时安装这两个包可以兼容不同的命令调用方式，其实它们本质是一样的。而编辑器技术哪家强？自从有了 Visual Studio Code 自然就不考虑其它的了。不过 Visual Studio Code 需要自己去它的 官网 下载。

　　把上面的代码写成一个.js文件，然后在编辑器中每个函数的返回点设置断点，直接使用 Node.js 的调试功能，就可以查看所有的函数执行时的 context 了，如下动图：

![image](https://images2015.cnblogs.com/blog/16576/201609/16576-20160911212021122-55896192.gif)

把断点设置在每一个函数的最后一条语句，按 F5 开始调试，每次暂停都可以看到这个函数执行时产生的 context，在这个 context 中，可以看到该函数中定义的变量和函数，也就是其中显示的Local范围的变量，以及该函数可以访问的外层变量，也就是其中显示的Closure和Global范围的变量。使用调试功能，我们终于可以看到a4和b4了，同时还可以发现，在每一个函数的 context 中，都有一个特殊的变量this，下一节，我们来讨论函数和this，函数、原型、闭包和this是使用 JavaScript 模拟经典的基于类的面向对象编程的基本要素。不过在进入下一节之前，我还要来展示一下 Eclipse。

　　Eclipse 的最新版本 neon 终于改进了，在前一个版本中，它只支持 ECMAScript 3，而且其网页预览还是使用的 Webkit-1.0，在今年发布的这个新版本中，终于支持 ECMAScript 5了，Webkit 也用到了最新版。还加入了对 Node.js 的支持。不过 Eclipse 中关于 JavaScript 的智能提示似乎还是很差劲。Eclipse 的更新速度实在是太慢了。不过用 Eclipse 配合 Node.js 调试 JavaScript 也还不错，下面直接上图：
　　
　　![image](https://images2015.cnblogs.com/blog/16576/201609/16576-20160912093227977-1045415569.gif)
　　
　　还有 Eclipse 的死对头，IntelliJ IDEA 和 WebStorm 调试 JavaScript 也是不错的，我就不多说了。

　　关于内层函数怎么捕获变量的问题，在编程语言界还有一个经典的争议，那就是关于词法作用域和动态作用域的争议。所谓词法作用域，就是在函数定义时的环境中去寻找外层变量，而动态作用域，就是在函数运行时的环境中去寻找外层变量。大多数现在程序设计语言都是采用词法作用域规则，而只有为数不多的几种语言采用动态作用域规则，包括APL、Snobol和Lisp的某些方言，还有 C 语言中的宏定义。很显然， JavaScript 采用的是词法作用域，变量的作用域链是在函数定义的时候就决定了的。而对于动态作用域的例子，我们可以看看如下的用 LISP 语言定义的一个函数：

```
(let ((y 7))
  (defun scope-test (x) (list x y)))
```
在 emacs lisp 的运行结果为(3 5)，而在采用词法作用域规则的编程语言中，如 common lisp，它会在定义函数的环境中寻找自由变量y的值，所以这段代码的运行结果为(3 7)。

　　另外，还有一个关于闭包和循环的一个经典的坑，当闭包遇到循环的时候，如下代码：

```
(function(){
    var i;
    for(i = 1; i <= 10; i++){
        setTimeout(function(){console.log(i);}, 500); //本以为会输出数字 1-10，结果输出了 10 次 11 
    }
})();
```
在上面代码中，我为了简洁，都使用了匿名函数。之所以会出现这样意想不到的结果，就是因为定义在内层的匿名函数都捕获了外层函数中的变量i，所以当它们运行的时候，都是输出的这个i的最终的值，那就是11。如果要想得到预期的输出 1-10 这样的结果，就应该在定义内层函数的时候让它接受一个参数，然后把i当做参数传递给它。代码改成这样就行：

```
(function(){
    var i;
    for(i = 1; i <= 10; i++){
        setTimeout((function(a){console.log(a);})(i), 500);
    }
})();
```

　　全部写成匿名函数自调用格式简洁是简洁了不少，但是可读性就差了许多。网上的关于这个坑的描述所用的示例代码往往是将内层函数设置为某个按钮的onClick事件处理程序，而我不想在我的示范中和 BOM、DOM 产生太多的耦合，所以我选择了setTimeout()。如果不信，可以自己在 Chromium 的 JavaScript 控制台中验证效果。
　　
### 函数和this
从前面的调试过程中我们可以看出，每一个函数执行的 context 中都有一个特殊的变量this。对this大家都不会陌生，很多面向对象的编程语言中都有，但是在 JavaScript 中，this会稍有不同，它的取值会随着函数的调用方式不同而变化。JavaScript 中函数的调用方式多种多样，总结起来主要有四种：

    1,做为构造函数调用，比如前面的new Person();、new Object();；
    2,做为对象的方法调用，比如前面的somebody.sayHello();；
    3,做为普通函数调用，这是用得最多的，比如前面的first();、what();；
    4,通过apply、call、bind方式调用，这种调用方式我后面会举例。
　　在第一种调用方式中，this的取值就是该构造函数即将创建的对象。在第二种方式中，this的取值就是该方法所在的对象。这两种调用方式和经典的面向对象编程语言没有什么不同，非常容易理解。第三种方式，做为普通函数调用，这时，函数中的this永远都指向全局对象，不管函数的定义嵌套得有多深，切记切记。而第四中调用方法最特别，它可以改变函数中this的取值，因此，这种方式调用最灵活，妙用最多，这个需要几个例子才能说明。先回顾一下我前面定义的Person()构造函数以及somebody对象：
```
　　function Person(name, age, gender){
    this.name = name;
    this.age = age;
    this.gender = gender;
}
Person.prototype.sayHello = function(){ console.log("Hello, my name is " + this.name); };
var somebody = new Person("youxia", 30, "male");
```
如果我们调用：

```
somebody.sayHello();  //sayHello()中的this指向somebody，所以输出"Hello, my name is youxia"
```
　　那么这个sayHello();方法中的this指向somebody对象，所以输出结果很符合预期。但是，如果该函数不是通过对象的方法调用，结果就会大不相同。比如这样：

```
var sayHi = somebody.sayHello;
sayHi(); //做为普通函数调用，该函数中的this指向全局变量所以输出"Hello, my name is "
```
在上面的例子中，因为全局变量中没有name属性，所以输出的结果中就没有名字了。

　　然后，我为了偷懒，不想定义一个构造函数，只使用对象字面量定义了一个对象worker，代表一个具有Java技术的程序员，如下：

```
var worker = {name:"javaer", speciality:"Java"};
```
　　这个对象没有sayHello()方法，但是我们可以这样借用somebody的sayHello()方法：

```
somebody.sayHello.call(worker); //输出"Hello, my name is javaer"
```

所有的函数都可以通过.call()、.apply()、.bind()的形式调用，因为这三个方法是定义在Function.prototype中的，而所有的函数的原型链中都有Function.prototype。这三个函数都会把调用函数的this设置为这几个方法的第一个参数。所不同者，.call()是接受任意多个参数，而.apply()只接受两个参数，其第二个参数必须是一个数组，而.bind()返回另外一个函数，这个函数的this绑定到.bind()的参数所指定的对象。

　　可以看到，如果某个对象具有和其它对象相同的属性，比如这里的name属性，就通过.call()的方式借用别的对象的方法。由于.apply()接受的第二个参数是一个数组，所以，如果有某个函数本身只接受不定数量的参数，而要操作的确是一个数组的时候，就可以用.apply()来在它们之间适配。最常见的例子就是Math.max()方法，该方法接受的是不定数量的参数，假如我们手头只有一个数组，比如这样：

```
var numbers = [3, 2, 5, 1, 7, 9, 8, 2];
```
　　而我们又要找出数组中的最大值的话，可以这样调用：

```
Math.max.apply(null, numbers);
```

把第一个参数设置为null，则Math.max()中的this就会自动指向全局对象。不过在这个例子中，this的值不重要。这里只是改变了Math.max()方法接受参数的形式。

　　在 JavaScript 中经常使用.call()调用来借用内置对象的方法，最常见的是借用Object.prototype.toString()方法。虽然我们所有的对象都是从Object继承，所有的对象都有从Object继承的toString()方法，但是，这些方法可以随时被重写。比如在我们前面定义的Person类中，我们可以重写它的toString()方法，如下：

```
Person.prototype.toString = function(){
    return 'Person {name: "' + this.name + '", age: ' + this.age + ', gender: "' + this.gender + '"}'; 
}
```
　　这时，调用somebody的toString()方法，会得到这样的输出：

```
somebody.toString();
//输出 "Person {name: "youxia", age: 30, gender: "male"}"
```
　　但是如果借用Object.prototype.toString()方法，则会得到另外一种输出：

```
Object.prototype.toString.call(somebody);
//输出 "[object Object]"
```
　　所以这种技术常被各种库用来判断对象的类型。如下：

```
Object.prototype.toString.call(somebody);
//输出 "[object Object]"
Object.prototype.toString.call(Person);
//输出 "[object Function]"
Object.prototype.toString.call("Hello, World!");
//输出 "[object String]"
Object.prototype.toString.call(["one", "two", "three"]);
//输出 "[object Array]"
Object.prototype.toString.call(3.14);
//输出 "[object Number]"
```
从上面可以看出，使用.call()借用别的对象中的方法，不会受到本对象中重写的同名方法的影响。所以，也可以在子类中使用此技巧调用父类中的方法，后面我讲面向对象和继承的时候会用到这个技巧。

　　下面又要开始踩坑了，这个坑是关于this的。上面提到过，凡是作为普通函数调用的函数，其 context 中的this都是指向全局对象的。所以，如果我们在某个对象的构造函数或方法中定义了内部函数，本以为使用this可以访问这个新构造的对象，结果会事与愿违。如下代码：

```
function Worker(name, speciality){
    this.name = name;
    this.speciality = speciality;
    this.doWork = function(){
        function work(){console.log(this.name + " is working with " + this.speciality);}
        work();
    }
}
var worker = new Worker("youxia", "Java");
worker.doWork();
```
　　本以为会输出"youxia is working with Java"，但是由于其中定义的work()是一个普通函数，所以其中的this指向全局对象，而全局对象的name和speciality属性是没有定义的，所以会输出"is working with undefined"。如果要解决这个问题，可以在构造函数中先临时保存this的值，在网络中，大家一般喜欢用that这个词。更改后的代码如下：

```
function Worker(name, speciality){
    this.name = name;
    this.speciality = speciality;
    var that = this;
    this.doWork = function(){
        function work(){console.log(that.name + " is working with " + that.speciality);}
        work();
    }
}
var worker = new Worker("youxia", "Java");
worker.doWork();
```

　　这回输出就完全正确了。同时，这里也提示出一个小技巧，那就是当我们位于一个闭包中时，如果想访问全局对象，只需要定义一个普通函数，然后访问这个普通函数的this即可。

### 用JavaScript模拟经典的面向对象编程
经典的面向对象编程语言比如 C++、C#、Java 等都是基于类的，它们都有一套成熟的体系，包括对象的构造、类的继承、对象的多态、对象属性的访问控制等。在 JavaScript 中，多态这个问题可以不用考虑，因为 JavaScript 语言本身就是动态的，所以不存在类型不符合就编译不通过这样的问题。在 JavaScript 中主要考虑的问题就是对象的构造和继承的问题。

　　对象的构造是需要首先考虑的问题，其目标就是要获得一个合理的对象内存布局。在 JavaScript 中没有类的概念，但是有构造函数和this就足够了，所以我们可以这样简单地创建对象：

```
function Person(name, age, gender){
    this.name = name;
    this.age = age;
    this.gender = gender;
    this.sayHello = function(){ console.log("Hello, my name is " + this.name); };
}
var somebody = new Person("somebody", 30, "male");
var another = new Person("another", 20, "female");
```
　　这和经典的面向对象编程语言在形式上是很像的，经典的面向对象的编程语言是在类里面定义属性和方法，而这里是在构造函数中定义属性和方法。然而，仔细分析的话，其在内存布局上还有不合理的地方，经典的面向对象编程语言中每个对象的属性是单独的，但是方法在内存中只有一个拷贝，而上述 JavaScript 代码每构建一个对象，都会为每个对象定义一个方法，如果对象数量很大的话，就会浪费很多内存。

　　根据所有对象共享方法的原则，以及 JavaScript 的语言特色，我们应该把方法放到其原型中，所以代码应更改如下：

```
function Person(name, age, gender){
    this.name = name;
    this.age = age;
    this.gender = gender;
}
Person.prototype.sayHello = function(){ console.log("Hello, my name is " + this.name); };
var somebody = new Person("youxia", 30, "male");
var another = new Person("another", 20, "female");
```
　　同时，如果为了防止别人在调用构造函数的时候忘记使用new而踩入this的陷阱的话，该代码还可以继续这样完善：

```
function Person(name, age, gender){
    if(!(this instanceof Person)){
        return new Person(name, age, gender);
    }
    this.name = name;
    this.age = age;
    this.gender = gender;
}
Person.prototype.sayHello = function(){ console.log("Hello, my name is " + this.name); };
var somebody = new Person("youxia", 30, "male");
var another = new Person("another", 20, "female");
```
　　下面再来看继承。假设我们每个人都有一个工作者身份，我们会使用我们掌握的某项技能进行工作，这里用 Worker 代表工作者，而 Worker 从 Person 继承。我们先来写 Worker，由于 JavaScript 是一个基于原型的语言，所以理论上讲，要让 Worker 继承自 Person，只需要把 Person 类的一个对象加入到 Worker 的原型链中即可，如下：

```
function Worker(name, age, gender, speciality){
    this.name = name;
    this.age = age;
    this.gender = gender;
    this.speciality = speciality;
}
Worker.prototype = new Person(name, age, gender);
Worker.prototype.doWork = function(){console.log(this.name + " is working with " + this.speciality);}
```
　　很显然，这也不是很合理的，在这里需要构建一个 Person 类的对象（这里暂且这么称呼吧，虽然 JavaScript 中没有类），而构建 Person 类的对象时又要传递参数，这些参数哪里来呢？很显然编码不是很方便。同时，既然在 Person 类的对象中构造了name、age、gender等属性，再在 Worker 类的对象中构建一次就重复了。而且修改了Worker.prototype后，constructor属性也变了，还要一条语句改回来。如果从经典的面向对象编程语言的角度来考虑，我们需要继承的仅仅只是 Person 类中的方法而已。如果从 JavaScript 语言的角度分析，我们只需要把Person.prototype加入到 Worker 类的对象的原型链中即可。代码是这样：

```
Worker.prototype.__proto__ = Person.prototype;
```

我们还可以使用前面提到的.call()来借用 Person 类的构造函数让代码更简洁。完整的继承代码如下：

```
function Worker(name, age, gender, speciality){
    Person.call(this, name, age, gender);
    this.speciality = speciality;
}
Worker.prototype.__proto__ = Person.prototype;
Worker.prototype.doWork = function(){console.log(this.name + " is working with " + this.speciality);}
```
　　这样使用它：

```
var worker = new Worker("youxia", 30, "male", ["JavaScript","HTML","CSS"]);
worker.sayHello(); //从Person类继承的
worker.doWork();   //Worker类中自己定义的
```
　　这是目前最接近经典面向对象语言的 JavaScript 模拟了。不过还有一个小小的问题，在 JavaScript 中，__proto__是一个隐藏属性，不是所有的 JavaScript 平台都支持的，比如前面展示的 IE 浏览器这个反面教材。这时，还是要把Worker.prototype设置为一个 Person 类的对象，但是，构建一个 Person 类的对象是个浪费，所以我们可以借助一个空构造函数来完成这个事情：

```
function EmptyFunc(){}
EmptyFunc.prototype = Person.prototype;
Worker.prototype = new EmptyFunc();
```
　　当然，别忘了把constructor改回来：

```
Worker.prototype.constructor = Worker;
```
　　这几条语句有点多，所以可以写一个辅助函数来解决这个问题：

```
function inherit(Sub, Super){
    function F(){}
    F.prototype = Super.prototype;
    Sub.prototype = new F();
    Sub.constructor = Sub;
}
```
　　完整代码如下：

```
function inherit(Sub, Super){
    function F(){}
    F.prototype = Super.prototype;
    Sub.prototype = new F();
    Sub.constructor = Sub;
}

function Person(name, age, gender){
    if(!(this instanceof Person)){
        return new Person(name, age, gender);
    }
    this.name = name;
    this.age = age;
    this.gender = gender;
}
Person.prototype.sayHello = function(){ console.log("Hello, my name is " + this.name); };
var somebody = new Person("youxia", 30, "male");
var another = new Person("another", 20, "female");

function Worker(name, age, gender, speciality){
    if(!(this instanceof Person)){
        return new Worker(name, age, gender, speciality);
    }
    Person.call(this, name, age, gender);
    this.speciality = speciality;
}
inherit(Worker, Person);
Worker.prototype.doWork = function(){console.log(this.name + " is working with " + this.speciality);}

var worker = new Worker("youxia", 30, "male", ["JavaScript","HTML","CSS"]);
worker.sayHello(); //从Person类继承的
worker.doWork();   //Worker类中自己定义的
```
　　上面的代码运行结果如下图：

![image](https://images2015.cnblogs.com/blog/16576/201609/16576-20160912230227258-1706350436.png)

### JavaScript的模块化写法
更进一步，还要解决一个问题，那就是怎么把这么大一坨代码封装起来，专业的说法，那叫模块化写法。

　　JavaScript 的一个缺陷就是它没有模块化的机制，像前文中我所写的所有构造函数都是直接暴露在全局作用域中的，这很不科学，一是污染了全局作用域，二是容易和别人写的代码发生冲突。当代码量增大的时候，肯定要考虑将我们自己的代码组织成一个模块。怎么办呢？很显然，在 JavaScript 中只能用自执行函数和闭包来模拟。比如这样：

```
(function(){

    function inherit(Sub, Super){
        ...
    }

    function Person(name, age, gender){
        ...
    }
    Person.prototype.sayHello = function(){ console.log("Hello, my name is " + this.name); };

    function Worker(name, age, gender, speciality){
        ...
    }
    inherit(Worker, Person);
    Worker.prototype.doWork = function(){console.log(this.name + " is working with " + this.speciality);}

    window.myModule = {
        inherit : inherit,
        Person : Person,
        Worker : Worker
    };
})();
```
　　代码比较长，我省略了一部分。最关键的代码其实是最后的几句，我们通过window对象的myModule属性来暴露我们想暴露的函数和构造函数。然后，我们可以在 HTML 页面中这样使用：

<!DOCTYPE html>
```
<html >
    <head>
        <meta charset="utf-8">
        <script src="./myModule.js"></script>
    </head>
    <body>
        <script>
            var worker = new myModule.Worker("youxia", 30, "male", ["Java","C++"]);
            worker.doWork();
        </script>
    </body>
</html>
```
　　下面是我在 FireFox 浏览器的开发者工具中的截图，在调试器中可以看到源文件，添加一个断点，就可以在右侧看到定义在myModule中的函数和构造函数了。Chromium 很牛，FireFox 也不错。

![image](https://images2015.cnblogs.com/blog/16576/201609/16576-20160913112831039-768768379.png)

当然，这只是一个最简单的前端模块。我是直接把模块的 JavaScript 文件路径写死在 HTML 中的。我在 HTML 中直接引用我自己的模块是没有问题的。但是在使用别人写的模块的时候就不一定有这么简单了，因为别人的模块中可能会引用更多另外的模块，而这些互相引用的模块我们不可能全部都写死在 HTML 中，我们更加不可能控制这些模块的加载顺序。因此，需要有统一的模块规范来解决模块加载和依赖的问题。目前，在浏览器中常用的规范有 AMD 规范和 CMD 规范。

　　AMD 规范是指异步模块定义，它是 RequireJS 在推广过程中对模块定义的规范化产出。在 AMD 中，所有的模块将被异步加载，模块加载不影响后面语句运行。所有依赖某些模块的语句均放置在回调函数中。AMD规范定义了一个全局 define 的函数：

define( id?, dependencies?, factory );
　　第一个参数 id 为字符串类型，表示了模块标识，为可选参数。第二个参数，dependencies 是一个数组，表示当前模块依赖的模块。第三个参数，factory，就是用来定义我们自己模块的工厂方法， factory 接受的参数和 dependencies 完全一致。加入我们自己的模块依赖于模块module1、module2、module3的话，我们的模块定义就应该这样写：

```
define("myModule", ["module1", "module2", "module3"], function(module1, module2, module3){

    // 这里定义我们自己的模块的功能，可以使用 module1、module2、module3中提供的功能
    var myModule = {
        a: function(){...},
        b: function(){...}
    }
    return myModule; //必须return个什么才能被别人使用

}
```
　　如果要使用 myModule 就应该这么写：

```
define("", ["myModule"], function(myModule){

       myModule.a();
       myModule.b();

}
```
　　CMD 规范是指通用模块定义，它是是 SeaJS 在推广过程中对模块定义的规范化产出。和 AMD 比较类似的是，它也定义了一个全局 define 函数，而且其形式也很类似，都是

define( id?, dependencies?, factory );
　　所不同者，是它的 factory 接受的参数必须是 require、exports 和 moudle，在 factory 内部，可以使用 require 引用依赖的模块，可以使用 exports 导出自己的功能，这和 Node.js 自带的 CommonJS 规范是比较相似的，其用法如下：

```
define( 'module', ['module1', 'module2'], function( require, exports, module ){
    var a = require("./a");
    a.doSomething();
    exports.do = function(){...};
} );
```
　　AMD 和 CMD 的区别就是：1.对于依赖的模块，AMD 是提前执行，CMD 是延迟执行。2.CMD 推崇依赖就近，AMD 推崇依赖前置。示例代码是这样：

```
// CMD
define(function(require, exports, module) {
    var a = require('./a')
    a.doSomething()
    ...
    var b = require('./b')   //依赖可以就近书写
    b.doSomething()
    ...
})

// AMD 默认推荐的是
define(['./a', './b'], function(a, b) {   //依赖必须一开始就写好
    a.doSomething()
    ...
    b.doSomething()
    ...
})
```
　　当然，这里面都有一个约定俗成的规则，那就是每一个模块都是一个同名的.js文件，我们在写模块名的时候，可以省略这个文件的扩展名。以上规范都是定义在前端的浏览器中的，而在后端的 Node.js 中就简单多了。Node.js 采用的是 CommonJS 模块规范，每一个文件就是一个模块，也不需要定义 define 什么的，也不需要定义自执行函数。在这个文件中，可以直接使用 exports 和 module。

　　有时，我们需要让我们编写的模块在前后端都能使用，这个要求不过分哦，比如我们想在 Node.js 中对模块进行单元测试，然后再发布到浏览器执行。利用之前提到的每种模块定义规范的特点，我们可以写出前后端通用的模块，代码片段如下：

```
var hasDefine = (typeof define !== "undefined");
var hasModule = (typeof module !== "undefined" && typeof module.exports !== "undefined");
if(hasDefine){ //运行在符合 AMD 或 CMD 规范的环境中
    define("myModule",function(){
        return {
            inherit : inherit,
            Person : Person,
            Worker : Worker
        };
    });
}else if(hasModule){ //运行在Node.js中
    module.exports = {
        inherit : inherit,
        Person : Person,
        Worker : Worker
    };
}else{  //否则直接加入到全局对象中
    window.myModule = {
        inherit : inherit,
        Person : Person,
        Worker : Worker
    };
}
```
　　下面测试一下我们写的模块是否能在前后端通用。先在 Node.js 中测试，写一个main.js，其内容如下：

```
var myModule = require("./myModule");
var worker = new myModule.Worker("youxia", 30, "male", ["HTML","CSS","JavaScript"]);
worker.doWork();
```
　　运行结果如下图：
　　
![image](https://images2015.cnblogs.com/blog/16576/201609/16576-20160913134328883-1523538868.png)

如果直接写死在 HTML 中呢？运行结果如下图：

![image](https://images2015.cnblogs.com/blog/16576/201609/16576-20160913134403883-772031325.png)

最后，我们看看使用 RequireJS 的情况。先到 RequireJS 的官网下载require.js文件，然后编写一个main_in_amd.js文件，内容如下：

```
requirejs(["./myModule"], function(myModule){
    var worker = new myModule.Worker("youxia", 30, "male", ["HTML","CSS","JavaScript"]);
    worker.doWork();
});
```
　　再然后，写一个 HTML 文件，这样引用require.js和main_in_amd.js文件：

```
<!DOCTYPE html>
<html >
    <head>
        <meta charset="utf-8">
        <script src="./require.js" defer async="true" data-main="./main_in_amd.js"></script>
    </head>
    <body>
    </body>
</html>
```
　　最后的运行结果如下图：

![image](https://images2015.cnblogs.com/blog/16576/201609/16576-20160913134725117-1285407230.png)

从图中可以看出，我们的模块确确实实是前后端可以通用的。该模块的完整代码如下：

```
(function(){
    function inherit(Sub, Super){
        function F(){}
        F.prototype = Super.prototype;
        Sub.prototype = new F();
        Sub.constructor = Sub;
    }

    function Person(name, age, gender){
        if(!(this instanceof Person)){
            return new Person(name, age, gender);
        }
        this.name = name;
        this.age = age;
        this.gender = gender;
    }
    Person.prototype.sayHello = function(){ console.log("Hello, my name is " + this.name); };

    function Worker(name, age, gender, speciality){
        if(!(this instanceof Person)){
            return new Worker(name, age, gender, speciality);
        }
        Person.call(this, name, age, gender);
        this.speciality = speciality;
    }
    inherit(Worker, Person);
    Worker.prototype.doWork = function(){
        console.log(this.name + " is working with " + this.speciality);
    }

    var hasDefine = (typeof define !== "undefined");
    var hasModule = (typeof module !== "undefined" && typeof module.exports !== "undefined");
    if(hasDefine){ //运行在符合 AMD 或 CMD 规范的环境中
        define("myModule", function(){
            return {
                inherit : inherit,
                Person : Person,
                Worker : Worker
            };
        });
    }else if(hasModule){ //运行在Node.js中
        module.exports = {
            inherit : inherit,
            Person : Person,
            Worker : Worker
        };
    }else{  //否则直接加入到全局对象中
        window.myModule = {
            inherit : inherit,
            Person : Person,
            Worker : Worker
        };
    }
})();
```

### 关于函数的更多探讨
前面探讨了 JavaScript 模拟经典面向对象的写法，而实际应用中，也有不少场景会使用 JavaScript 模拟函数式编程语言的写法，比如偏函数和柯里化什么的。JavaScript 之父本身是一个 Scheme（一种 LISP 方言） 高手，所以他在创建 JavaScript 之初就从函数式编程语言中吸收了很多东西，将函数当成一等公民对待就是其证明。在 JavaScript 中，函数是一等公民，可以把函数当参数传递给另外的函数，也可以从函数中返回函数，所以在 JavaScript 中使用高阶函数是一个很简单很常见的事情。

　　在前面的示例的截图中可以看到，每一个函数中都包含几个特殊的变量。前面已经介绍了其中一个特殊变量this，另外还有一个特殊变量arguments，它代表了传递给函数的所有参数，它是一个类数组的对象（说明它不是数组，但是可以利用前面介绍的.call()借用数组的方法）。什么时候会用到arguments呢？就是当函数的形参个数和实参个数不一样的时候，可以使用arguments变量访问传递给函数的所有参数。这使得定义可变数量参数的函数成为可能。如下示例，随便定义一个函数，没有定义形参，但是调用时可以指定任意的实参：

```
function someFunc(){
    console.log("arguments count: " + arguments.length);
    for(var i = 0; i < arguments.length; i++){
        console.log(arguments[i]);
    }
}
```
　　不管用多少个参数调用该函数，都可以输出参数的个数和所有的参数，如下：

```
someFunc(1, 2, 3);
//输出以下内容
// arguments count: 3
// 1
// 2
// 3

someFunc("one", "two", "three", "four");
//输出以下内容
// arguments count: 4
// one
// two
// three
// four
```
　　在 JavaScript 中，常使用一种叫偏函数的技巧来简化某些需要太多参数的函数的使用。有时有些函数需要接受很多参数，但是其中一些参数是经常重复的，但是调用时又必须输入这些参数，比较麻烦。这时，就可以创建这些函数的偏函数版本，把那些经常重复的参数预先设定为固定的值，调用这些偏函数时，只需要传递少量参数就行了。举例说明，可以利用前面提到的判断对象类型的方法定义一个isType()函数，如下：

```
function isType(obj, type){
    if(Object.prototype.toString.call(obj) === "[object " + type + "]"){
        return true;
    }else{
        return false;
    }
}
```
　　可以这样调用该函数：

```
var msg = "Hello, World!";
var obj = {};
var arr = [];
isType(msg, "String");
isType(obj, "Object");
isType(arr, "Array");
```
　　每次调用都需要输入两个参数，第二个参数通过字符串来指定需要判断的类型，如果需要判断类型的对象特别多，这样调用就特别麻烦，而且容易出错。所以可以创建这个函数的偏函数版本，比如isFunction()、isString()、isArray()、isObject()等，这些函数只需要接受一个参数就可以了。怎么创建呢？先要写一个返回偏函数的函数：

```
function partial(fn, type){    //接受需要偏函数化的原函数和需要预先设置的参数作为参数
    return function(obj){      //返回偏函数
        return fn(obj, type);
    }
}
　　然后利用这个函数返回isType()的各个偏函数版本，如下：

var isFunction = partial(isType, "Function");
var isString = partial(isType, "String");
var isArray = partial(isType, "Array");
var isObject = partial(isType, "Object");
```
　　然后这样调用这些函数：

```
isString(msg);
isObject(obj);
isArray(arr);
isFunction(Person);
```
　　是不是简单多了，也不容易出错。但是上面只是把本来需要两个参数的函数节约了一个参数，收益并不是很大。其实，可以使用偏函数的理论创建一个将任意函数进行偏函数化的函数partialAny()，如下：

```
function partialAny(fn){    //接受原函数
    var originalArgs = Array.prototype.slice.call(arguments, 1); //获得原始参数，其参数个数和原函数需要的个数相同，其中有占位符
    return function(){      //返回偏函数
        var partialArgs = Array.prototype.slice.call(arguments); //获得偏函数的参数，其个数应该和占位符的个数相同
        var newArgs = [];
        for(var i=0; i < originalArgs.length; i++){
            if(originalArgs[i] === "_"){ //如果碰到占位符，则用偏函数的参数填补
                newArgs[i] = partialArgs.shift();
            }else{
                newArgs[i] = originalArgs[i];
            }
        }
        // 如果有任何多余的参数，则添加到尾部
        return fn.apply(this, newArgs.concat(partialArgs));
    }
}
```
　　先用它了创建一个前面的isType()函数的偏函数试一试：

```
var isString = partialAny(isType, "_", "String");
isString("abc"); // 返回 true
```
　　我这里选择了下划线做为占位符，大家可以根据自己的情况酌情选择。下面来个更复杂的例子，比如经常需要创建 RGB 颜色的函数：

```
function makeColor(r, g, b){
    return "#" + r + g + b;
}
```
　　该函数一般情况下需要三个参数，但是可以通过偏函数的方式让 R、G、B 的任何一个分量固定，比如这样：

```
var redMax = partialAny(makeColor, "ff", "_", "_");
var blueMax = partialAny(makeColor, "_", "_", "ff");
var greenMax = partialAny(makeColor, "_", "ff", "_");
var magentaMax = partialAny(makeColor, 'ff', "_", 'ff');
```
　　然后这样调用它们：

```
redMax("33", "44");   // 输出"#ff3344"
blueMax("55", "66");  // 输出"#5566ff"
greenMax("77", "88"); // 输出"#77ff88"
magentaMax("99");     // 输出"#ff99ff"
```
　　函数也是对象，所以可以重写函数对象的toString()和valueOf()方法来达到意想不到的效果。举例说明，假如我们想创建一个add()函数，它既可以这样调用：

```
add(1,2);
```
又可以这样调用：

```
add(1,2)(3);
add(1)(2)(3)(4);
```
　　可以看出，这很类似于函数式编程语言中的柯里化（currying）。仔细观察可以发现，要实现以上效果，add()必须返回一个函数，这样才能继续后面的调用，如下：

```
function add(){
    var result = 0;
    for(var i=0; i < arguments.length; i++){  //先计算第一层调用
        result += arguments[i];
    }
    function temp(){
        for(var i=0; i < arguments.length; i++){  //再计算后续的调用
            result += arguments[i];
        }
        return temp;  //返回函数，所以可以无限调用下去
    }
    return temp;      //返回函数，所以可以无限调用下去
}
```
　　这样很方便就解决了函数连续调用的问题，但是又引出了新问题：该函数永远返回的是函数，那怎样才能得到求和的值呢？这时就该toString()和valueOf()上场了，我们只需要重写函数temp()的toString()和valueOf()方法，就可以在函数调用结束后，获得该表达式的值，如下：

```
temp.toString = temp.valueOf = function(){ return result; };
　　完整代码如下：

function add(){
    var result = 0;
    for(var i=0; i < arguments.length; i++){  //先计算第一层调用
        result += arguments[i];
    }
    function temp(){
        for(var i=0; i < arguments.length; i++){  //再计算后续的调用
            result += arguments[i];
        }
        return temp;  //返回函数，所以可以无限调用下去
    }
    temp.toString = temp.valueOf = function(){ return result; };  //这样可以获得函数的值
    return temp;      //返回函数，所以可以无限调用下去
}
```
　　然后，就可以这样随意调用add()了：

```
add(1,2);  //得到3
add(1,2)(3);  //得到6
add(1,2)(3,4)()(5);  //得到15
add(1,2)(3)(4)(5)()(6,7,8,9); //得到45
```
　　当然，这并不是最严格的柯里化，柯里化只是函数式编程语言中的一个特性，严格的柯里化每次只接受一个参数，直到总共接受了指定数量的参数后函数才执行。使用柯里化的好处是参数复用和延迟执行。所以，从本质上讲，前面提到的偏函数更接近函数式编程语言中柯里化的功能。上面的例子虽然形式上类似柯里化，但是比柯里化更灵活，可以接受无限多个参数和无限次调用，然而，这并没有什么卵用，炫耀技巧而已。


转自 http://www.cnblogs.com/youxia/p/javascript.html