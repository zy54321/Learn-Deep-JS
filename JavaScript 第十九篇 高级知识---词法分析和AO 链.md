[JavaScript 第十九篇 高级知识---词法分析和AO 链](https://www.cnblogs.com/OceanHeaven/p/4957704.html)
上面一篇文章说了js的作用域链，这一节算是对上面的延申，有一个典型的例子，首先看原来的一段代码：
```
var name = "test";
function t() {
    var b = 1;
    if (b === 1) {
        var name = "test1";
    } else {
        var name = "test2";
    }
    console.log(name);
}
t();
```
这段代码的结果是test1

![image](https://images2015.cnblogs.com/blog/562505/201511/562505-20151111230545009-1734156177.jpg)

这个肯定是没有什么问题了，但是如果把代码作如下修改：
```
修改1：
var name = "test";
function t() {
    console.log(name);
    var name = "test1";
    console.log(name);
}
t();

修改2：
var name = "test";
function t() {
    console.log(name);
       /***********
    // var b = 1;
    // if (b === 1) {
    //     var name = "test1";
    // } else {
    //     var name = "test2";
    // }
    // console.log(name);
    ******************/
}
t();
```
这两个都是基于原来的代码修改，运行的结果分别是undefine,test1和test,噫！

这个是修改1后的输出：

![image](https://images2015.cnblogs.com/blog/562505/201511/562505-20151111230734775-1689688471.png)

![image](https://images2015.cnblogs.com/blog/562505/201511/562505-20151111230930556-67341275.png)

这个是什么原因呢？原来在js运行前会有一个类似编译的过程------词法分析。首先看下记法分析的过程，词法分析主要分三个步骤：

1、先分析参数

2、再分析变量的声明

3、分析函数说明

**具体的步骤：**
0：函数的在运行的瞬间，生成一个活动对象（Active Object)就是所谓的AO

1：分析参数

      1-1:函数接收参数，添加到AO的属性上面，值全部都是undefine,如AO.age=undefine

      1-2:接收实参，形成AO对应的属性值

2：分析变量声明，如var age,

     2-1：如果AO上还没有age属性，则添加AO 属性，值是undefine

     2-2：如果AO 上面已经有了age属性,则不做任何操作。

3：分析函数的声明，如果funcion foo(){},

3-1： 则把函数赋给AO.fooo,如果数据属性已经存在，则要被现在的新的值覆盖

下面开始分析上面修改1的执行的过程（自上崦下）：

    1、在window上面形成一个AO链，AO.name=undefine;

    2、在函数的内部分析参数，函数没有参数，略过

    3、在到变量声明阶段，在t函数的内部形成AO1,AO1.name=undfine;

    4、分析函数声明，略过

执行过程：

   1、首先是 在外层的AO.name=”test”

   2、执行t函数，执行到console.log(name)时，函数内有name属性，就不会在到外面去寻找 执行输出undefine

   3、执行赋值语句AO1.name=”test1”

   4、输出当前的name值,”test1”;

这个问题解释清楚后，下面再分享几个代码：

```
function t(age) {
    console.log(age);//1
    var age = 99;
    console.log(age);//2
    function age() {
        
    }
    console.log(age);//3
}
t(5);
```

这个问题看起来有点晕，但是事事都逃不过一个理，下面开始对这个进行词法分析

   1-1、分析参数，有一个参数，形成一个AO.age=undefine;

   1-2、接收参数AO.age=5;

   2、分析变量声明，有一个var age,发现AO上面有一个AO.age，则不做任何处理

   3、分析函数声明，有一个function age(){}声明， 则把原有的age覆盖成AO.age=function(){};

执行过程

   1、执行第一个console.log(age)时，当前的AO.age是一个函数，所以输出的一个函数

   2、这句var age=99;是对不AO.age的属性赋值，AO.age=99，所以在第二个输出的是99

   3、同理第三个输出的是99,因为中间没有改变age值的语句了，

具体结果是，function age(){...}，99,99，你算对了么？

![image](https://images2015.cnblogs.com/blog/562505/201511/562505-20151111231218447-931602203.png)

如果把代码稍微再改下： 

```
function t(age) {
    var age;
    console.log(age);
    age = "99";
    console.log(age);
    function age() {
    }
    console.log(age);
}
t(5);
```

你发现这个和上面运行的结果一致，虽然js代码是从上到下到下执行的，但从这个例子可以看出，在执行前还有一定规则的“编译”。

好了，现在词法分析说的差不多了，万变不离其宗，下面开始再说几个容易混淆的问题：

对上面的代码再修改

1、
```
function t(age) {
    var age;
    console.log(age);
    age = "99";
    console.log(age);
    function age() {
        console.log(age);
    }
    age();//这里不一样
    console.log(age);
}
t(5);
```

说明:这里的与前面的一样，但是这个唯一不同的是调用了age();呵呵，这个输出什么，这里的age根本不是一个函数了，所以肯定要报错了。不要被这个表现的现象忽悠了。

![image](https://images2015.cnblogs.com/blog/562505/201511/562505-20151111231410869-481012349.png)

2、
```
function t(age) {
    var age;
     //age=99;这里被我注释掉了 
    console.log(age);
    function age() {
        console.log(age);
    }
    age();
    console.log(age);
}
t(5);
```

现在想想答案会是什么？这里没有对age的赋值，说明age是一个函数，当这个函数执行的时候，age函数的内部没有age变量，根据作用域链到外层的函数去找，找到了一个age,但它还是一个函数，又再次输出函数，后面一个也是如此，所以结果是下面结果：

![image](https://images2015.cnblogs.com/blog/562505/201511/562505-20151111231504869-1705362181.png)

3、把函数声明函数变成用函数表达式
```
function t(age) {
    // var age;
    console.log(age);
    var age=function () {
        console.log(age);
    }
    age();
    console.log(age);
}
t(5);
```
这个结果应该是？先说下函数的表达式，如果把表达式拆开也就明白了，拆成如下的结果：var age;age=function(){},也就是函数表达是其实是一个变量声明和赋值的过程，而不是一个真正的函数声明！所以有了前面的基础，这个输出就很简单了，答案是5,function(){},function(){};

![image](https://images2015.cnblogs.com/blog/562505/201511/562505-20151111231620212-1959064688.png)

4、把匿名函数转成“函数声明的形式”
```
function t(age) {
    // var age;
    console.log(age);
    var age=function age () {
        console.log(age);
    }
    age();
    console.log(age);
}
t(5);
```

这个答案又是什么呢？是不是有点晕？这个结果和上面的结果是一个的，由这两个例子可以看出js的函数表达式只是一个赋值的过程。

好了说了这么多，今天的内容算是说完了， 希望能够让你从另一个全新的角度去考虑js的这门语言。后面会把js的原型链，js的继承链写出来，这三个链写完后，会把js中的this详细的总结下，js的部分有些“坑”说下，希望能够给你带来收获。