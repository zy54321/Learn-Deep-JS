[JavaScript 深入原型链](https://www.cnblogs.com/OceanHeaven/p/4965947.html)

https://www.cnblogs.com/OceanHeaven/p/4965947.html

前面把js作用域和词法分析都说了下，今天把原型链说下，写这个文章费了点时间，因为这个东西有点抽象，想用语言表达出来不是很容易，我想写的文章不是简单的是官方的API的copy，而是对自己的知识探索和总结的过程，以及在这个过程碰到的问题都一一写出来，我想大多数人应该也有这个疑惑，然后带着疑惑去找答案，当你把这个疑惑解决之后，才觉得很有成就感。下面不多说了，开始说说什么是原型链。要想了解原型链，还是要从简单的开始，什么是原型？首先看下代码：
```
function funcA() {
    this.show = function() {}
}
console.log(funcA.prototype);


```
![image](https://images2015.cnblogs.com/blog/562505/201511/562505-20151114232842119-1084625551.png)

由这个图可以看出funcA的原型是一个对象，他的名字也是funcA,有一个constructor属性指向funcA,同时有一个__proto__指向Object.prototype(这个是怎么得到？可以看下上面的__proto__下面的constructor属性指向是Object，说明他是Object的原型).funcA原型大概写成：
```
funcA.prototype={
    constructor:funcA,
    __proto__:Object.prototype

}
```

PS:这里的__proto__从哪里冒出来的？在js中，每个对象都有一个名为__proto__的内部隐藏属性，指向于它所对应的原型对象(chrome、firefox中名称为__proto__，并且可以被访问到)，而普通的对象没有prototype属性，只有__proto__属性。由上面的结果可以得到下面的这张图

![image](https://images2015.cnblogs.com/blog/562505/201511/562505-20151114232931259-2008837214.png)

现在我们对原型有了一个大概的了解，反正他就是这么一个对象,有几个不同寻常的属性，继续深入，当我们利用funcA创建对象的时候，这个又会发生什么？

```
function funcA() {
    this.show = function() {}
}
console.log(funcA.prototype);
var a = new funcA();
console.log(a);
console.log(a.__proto__);
console.log(a.__proto__ === funcA.prototype);
console.log(a.__proto__.constructor === funcA);
```

运行结果如下：

![image](https://images2015.cnblogs.com/blog/562505/201511/562505-20151114233044415-1291336101.png)

对上面的这个结果进行分析，a是funcA创建出来的对象，他有funcA里面的方法，同时他也有一个__proto__，可以用代码表示成:

```
var a={

show:function(){..…},

__proto__:funcA.prototype

}
```

同理，我们可以得到下面这张图：

![image](https://images2015.cnblogs.com/blog/562505/201511/562505-20151114233132681-547411238.png)

总结下上面的过程，从表象上看，funcA在创建对象的时候，这个对象a,把funcA里面的方法“复制过来”，然后产生了一个__proto__属性指向它的原型。

 其实new创建对象，分为三步

```
1、 创建了一个空对象a={};
2、 将这个空对象的__proto__成员指向了funcA函数对象prototype成员对象
3、 将funcA函数对象的this指针替换成a，然后再调用funcA函数，于是我们就给a对象赋值了一个新 的方法成员。
```

具体的实现代码可以表示如下：
```
var a ={};
a.__proto__=funcA.prototype;
funcA.call(a);

下面我们用代码来验证：
function funcA() {
    this.show = function() {}
}
console.log(funcA.prototype);

var b=new funcA();//new 来构建

var a ={};//define 手动定义
a.__proto__=funcA.prototype;
funcA.call(a);
console.log(a);
console.log(b);
```

实现结果如下 ，可以看到a和b的内部方法完全一个，唯一的区别是a来自于Object的prototype,b来自于funcA的prototype(导致输出的名字是不一样)。

![image](https://images2015.cnblogs.com/blog/562505/201511/562505-20151114233251244-1386401525.png)

现在这个可以对上面的几个过程做一个总结，什么是原型? Js所有的函数都有一个prototype，其实质就是对一个对象的引用，称之为原型（还是有点难理解，但先作为js的一种机制去理解，至于为什么，后面再往深入去探讨）。在构造新的对象的普通对象的时候会产生一个__proto__属性指向它。

**继续向下挖：**
好了，现在把js的原型和对象的产生理解清楚了，下面的事情就简单了，下面揭开上面图形中的Object的神秘面纱:funcA.prototype.__proto__指向的Object是一个什么东西？它的来源又是什么？我们在控制台上面输出当前的这个值结果如下：

![image](https://images2015.cnblogs.com/blog/562505/201511/562505-20151114233432759-2106297307.png)

根据上面的经验，我们可以推测出__proto__指向的应该是一个函数的原型，也就是说xxx.prototype=funcA.prototype.__proto__,这个xxx同时应该是当前这个Object的constrcutor,好了下面看看猜想的对不对。

![image](https://images2015.cnblogs.com/blog/562505/201511/562505-20151114233454603-1717496098.png)

上面的红色框框的是输出的结果，由这个我们可以说我们说的是对的。同理既然Object是一个对象，那他的prototype和__proto__又是什么呢？

![image](https://images2015.cnblogs.com/blog/562505/201511/562505-20151114233533353-1185974473.png)

由上面的内容可以看出，最终的原型是一个null，即说明 产生Object.prototype这个对象的“母体”的原型是null，总结上面的结果，可以总结到下面这张图上面：

![image](https://images2015.cnblogs.com/blog/562505/201511/562505-20151114233607494-1423956655.png)

好了，对于原型算是讨论差不多了，下面再说说比较特殊的Function，上点代码

```
var foo=new Function("var str='c';console.log(str);");
foo();
console.log(foo);
console.log(foo.prototype);
console.log(foo.__proto__);
//
```
运行结果如下：

![image](https://images2015.cnblogs.com/blog/562505/201511/562505-20151114233808259-989389996.png)

由上面的结果可知，由Function产生的对象还是一个函数对象，foo.__proto__是一个空函数对象，foo.prototype是一个Object的实例对象。由上面可以推断出来，foo.__proto__===Function.prototype;为true推出Function.prototype=function(){}(是一个空函数),既然Fuction,prototype是一个函数对象，那它的原型是什么呢？即查看Function.prototype.prototype=？这个值是多少呢？

![image](https://images2015.cnblogs.com/blog/562505/201511/562505-20151114233856478-428179309.png)

由此可见，这个function(){}没有原型，他应该是原型的终结了，即全部函数的__proto__就都是function(){},即是Function.prototype,即所有的函数都是Function的一个实例对象，这也就解释了，js中一切皆对象的这句话。

再思考：我们已经找到函数的根了，那Function.prototype对象的根在哪里？Function.prototype已经是到了原型的终结了，那它又是怎么来的呢？我们再看看Function.prototype.__proto__=?这个到底是什么

![image](https://images2015.cnblogs.com/blog/562505/201511/562505-20151114233942244-338876099.png)

从这个结果我们看到,Function.prototype.__proto__是一个Object的实例对象，从上面分析原型的时候我们都已经知道，这个“object”最终的__proto__的指向是null。到目前为止我们心里虽然有很多的疑问，但觉得一切变得明朗起来，函数对象和普通对象是相通的，最终的指向都是一个null.总结一下，如下图：

![image](https://images2015.cnblogs.com/blog/562505/201511/562505-20151114234016525-1649704719.png)

上图是我们分析后所有的元素的关系，这个看起来清楚了许多，所有的对象的__proto__元素的终点都是null，如果撇开null元素，即所有的普通对象的基对象都是Object(是Object函数的原型），包括Object的类型的普通对象;同理，所有的函数的__proto__都是function(){}(Function.prototype),当然也包含Function本身。看完这些是不是有一种从无中生一（null对象)，一生二，二生万物的感觉？

可能有人也注意到了右正解的红色的Function,他也是他自己产生的，先不要急， 为了更好的说明上面的这个图我们再看下面的代码说明下：

```
function funcA() {}
function funcB() {}
console.log(funcA.__proto__);
console.log(funcB.__proto__);
console.log(funcA.__proto__ === funcB.__proto__);
console.log(funcA.__proto__ === funcB.__proto__ && funcB.__proto__ === Number.__proto__ && Number.__proto__ === Function.__proto__);
console.log(Function.prototype == Function.__proto__);
```

运行结果如下：

![image](https://images2015.cnblogs.com/blog/562505/201511/562505-20151114234159681-414850702.png)

上面的运行结果也证明我的们想法，Function是一个特殊的对象，他的实例来源也来自于他自己，因为他的原型是一切函数的源头，他本身也是一个函数，所以就不奇怪Function.prototype===Function.__proto__是成立的。同理，一切函数对象的根源也都是一样的，所以下面的等式都成立了

```
function funcA() {}
function funcB() {}
funcA.__proto__ === funcB.__proto__ ;
funcB.__proto__ === Number.__proto__ ;
Number.__proto__ === Function.__proto__;
Function.__proto__===Function.prototype
Number.__proto__ === Function.prototype;  // true
Boolean.__proto__ === Function.prototype; // true
String.__proto__ === Function.prototype ; // true
Object.__proto__ === Function.prototype;  // true
Function.__proto__ === Function.prototype; // true
Array.__proto__ === Function.prototype ;  // true
RegExp.__proto__ === Function.prototype;  // true
Error.__proto__ === Function.prototype;   // true
Date.__proto__ === Function.prototype;    // true
```

说了这么多还没有讲到原型链，呵呵，上面如果都理解了，那么原型链这个东西就so easy了，不多说还是用代码说话：

```
var a={};
 a.__proto__.value="a";
 a.show=function(){
     console.log(this.value);
 }
 a.show();
```

这段代码运行的结果是输出一个字母”a”,但是我们看到a的对象中没有value的这个字段，但同样也能输出,因为在它的__proto__上面有一个value属性，js在寻找属性的时候会沿着__proto__的去寻找，这个就是我们要说的原型链。原理很简单：如果在当前对象中找不到当前属性或者方法（value),那么就会沿着原型链开始找，一直遍历完整个的原型链，一旦找到，就返回第一个找到的属性获得方法，如果没有，就返回undefined。

说了这么多，原型链到底有什么作用?也许很多人会说，可以用来继承，不过没错，但是我们还是要了解，为什么他可以继承，为什么 funcA.prototype=new funcB();这个就可以实现了所谓的继承。下面还是看代码：

```
function funcA() {
    this.show = function(str) {
        console.log(str);
    }
}
function funcB() {
    this.read = function() {
    }
}
var a = new funcA();
funcB.prototype = a;
a.show("a");
var b=new funcB();
b.show("b");
```

 运行的结果如下：
 
 ![image](https://images2015.cnblogs.com/blog/562505/201511/562505-20151114234437181-891485030.png)
 
 这个是很常用的写法，可以看到b是可以调用funcA的方法的，这个是为什么呢，下面我们画一个原型的示意图
 
 ![image](https://images2015.cnblogs.com/blog/562505/201511/562505-20151114234507556-903807444.png)
 
 看到上面的图应该就明白了，因为funcB的原型是a,所以导致b.__proto__=a,所以b就能够沿着原型链访问a中的方法，这样就实现了继承----原型链继承，又称为原型继承。

当前这种继承的方法可以不用这么写，原理清楚了，那就改下也没有什么，看一段简单粗暴的原型继承：

```
function funcA() {
    this.show = function(str) {
        console.log(str);
    }
}
function funcB() {
    this.read = function() {}
}
var a = new funcA();
var b = new funcB();
b.__proto__ = a;
a.show("a");
b.show("b");
```

这样和上面的输出的结果是一个的，但是这样的话只有对象b继承了a中的方法，但是再产生的funcB的新对象，却没有a中的方法：

如：
```
var c=new funcB();
c.show("c");//报错
```

当然实现的方法很多，下讲会讲说下js的三种继承方式，如果篇幅不大，会连着js的this的一起写出来。下面在写一个个实现方法，供参考，原因在上面：

```
function funcA() {
    this.show = function(str) {
        console.log(str);
    }
}
function funcB() {
    this.read = function() {}
}
var a = new funcA();
var b = new funcB();
 funcA.call(b);//use call
a.show("a");
b.show("b");
```
