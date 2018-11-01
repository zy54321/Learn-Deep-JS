[JS中常见原生DOM操作API【总结整理)](https://blog.csdn.net/hj7jay/article/details/53389522)

https://blog.csdn.net/hj7jay/article/details/53389522

### 几种对象
#### Node
 Node是一个接口，中文叫节点，很多类型的DOM元素都是继承于它，都共享着相同的基本属性和方法。常见的Node有 element，text，attribute，comment，document 等（所以要注意 节点 和 元素 的区别，元素属于节点的一种）。

Node有一个属性 nodeType 表示Node的类型，它是一个整数，其数值分别表示相应的Node类型，具体如下：

```
{
    ELEMENT_NODE: 1, // 元素节点
    ATTRIBUTE_NODE: 2, // 属性节点
    TEXT_NODE: 3, // 文本节点
    DATA_SECTION_NODE: 4,
    ENTITY_REFERENCE_NODE: 5,
    ENTITY_NODE: 6,
    PROCESSING_INSTRUCTION_NODE: 7,
    COMMENT_NODE: 8, // 注释节点
    DOCUMENT_NODE: 9, // 文档
    DOCUMENT_TYPE_NODE: 10,
    DOCUMENT_FRAGMENT_NODE: 11, // 文档碎片
    NOTATION_NODE: 12,
    DOCUMENT_POSITION_DISCONNECTED: 1,
    DOCUMENT_POSITION_PRECEDING: 2,
    DOCUMENT_POSITION_FOLLOWING: 4,
    DOCUMENT_POSITION_CONTAINS: 8,
    DOCUMENT_POSITION_CONTAINED_BY: 16,
    DOCUMENT_POSITION_IMPLEMENTATION_SPECIFIC: 32
}
```

####  NodeList
 NodeList 对象是一个节点的集合，一般由 Node.childNodes 、 document.getElementsByName 和 document.querySelectorAll 返回的。

不过需要注意， Node.childNodes 、 document.getElementsByName 返回的 NodeList 的结果是实时的（此时跟HTMLCollection比较类似），而 document.querySelectorAll 返回的结果是固定的，这一点比较特殊。

举例如下：
```
var childNodes = document.body.childNodes;
console.log(childNodes.length); // 如果假设结果是“2”
document.body.appendChild(document.createElement('div'));
console.log(childNodes.length); // 此时的输出是“3”
```

####  HTMLCollection
 HTMLCollection是一个特殊的NodeList，表示包含了若干元素（元素顺序为文档流中的顺序）的通用集合，它是实时更新的，当其所包含的元素发生改变时，它会自动更新。另外，它是一个伪数组，如果想像数组一样操作它们需要像 Array.prototype.slice.call(nodeList, 2) 这样调用。
 
### 节点查找API


    document.getElementById ：根据ID查找元素，大小写敏感，如果有多个结果，只返回第一个；

    document.getElementsByClassName ：根据类名查找元素，多个类名用空格分隔，返回一个 HTMLCollection 。注意兼容性为IE9+（含）。另外，不仅仅是document，其它元素也支持 getElementsByClassName 方法；

    document.getElementsByTagName ：根据标签查找元素， * 表示查询所有标签，返回一个 HTMLCollection 。

    document.getElementsByName ：根据元素的name属性查找，返回一个 NodeList 。

    document.querySelector ：返回单个Node，IE8+(含），如果匹配到多个结果，只返回第一个。

    document.querySelectorAll ：返回一个 NodeList ，IE8+(含）。

    document.forms ：获取当前页面所有form，返回一个 HTMLCollection ；

###  节点创建API
 节点创建API主要包括 createElement 、 createTextNode 、 cloneNode 和 createDocumentFragment 四个方法。
 
#### createElement
创建元素：
```
var elem = document.createElement("div");
elem.id = 'test';
elem.style = 'color: red';
elem.innerHTML = '我是新创建的节点';
document.body.appendChild(elem);
```
 通过 createElement 创建的元素并不属于 document 对象，它只是创建出来，并未添加到html文档中，要调用 appendChild 或 insertBefore 等方法将其添加到HTML文档中。
 
 #### createTextNode
  创建文本节点：
  ```
  var node = document.createTextNode("我是文本节点");
document.body.appendChild(node);
  ```
  
#### cloneNode
克隆一个节点： node.cloneNode(true/false) ，它接收一个bool参数，用来表示是否复制子元素。
```
   var from = document.getElementById("test");
var clone = from.cloneNode(true);
clone.id = "test2";
document.body.appendChild(clone);
   ```
   
克隆节点并不会克隆事件，除非事件是用 <div onclick="test()"></div> 这种方式绑定的，用 addEventListener 和 node.onclick=xxx; 方式绑定的都不会复制。
    
#### createDocumentFragment
本方法用来创建一个 DocumentFragment ，也就是文档碎片，它表示一种轻量级的文档，主要是用来存储临时节点，大量操作DOM时用它可以大大提升性能。

假设现有一题目，要求给ul添加10000个li，我们先用最简单的拼接字符串的方式来实现：

```
<ul id="ul"></ul>
<script>
(function()
{
    var start = Date.now();
    var str = '';
    for(var i=0; i<10000; i++) 
    {
        str += '<li>第'+i+'个子节点</li>';
    }
    document.getElementById('ul').innerHTML = str;
    console.log('耗时：'+(Date.now()-start)+'毫秒'); // 44毫秒
})();
</script>
```

再换逐个append的方式，不用说，这种方式效率肯定低：
 
```
<ul id="ul"></ul>
<script>
(function()
{
    var start = Date.now();
    var str = '', li;
    var ul = document.getElementById('ul');
    for(var i=0; i<10000; i++)
    {
        li = document.createElement('li');
        li.textContent = '第'+i+'个子节点';
        ul.appendChild(li);
    }
    console.log('耗时：'+(Date.now()-start)+'毫秒'); // 82毫秒
})();
</script>
```

最后再试试文档碎片的方法，可以预见的是，这种方式肯定比第二种好很多，但是应该没有第一种快：
```
<ul id="ul"></ul>
<script>
(function()
{
    var start = Date.now();
    var str = '', li;
    var ul = document.getElementById('ul');
    var fragment = document.createDocumentFragment();
    for(var i=0; i<10000; i++)
    {
        li = document.createElement('li');
        li.textContent = '第'+i+'个子节点';
        fragment.appendChild(li);
    }
    ul.appendChild(fragment);
    console.log('耗时：'+(Date.now()-start)+'毫秒'); // 63毫秒
})();
</script>
```
### 节点修改API
节点修改API都具有下面这几个特点：

    不管是新增还是替换节点，如果其原本就在页面上，那么原来位置的节点将被移除；
    
    修改之后节点本身绑定的事件不会消失；
    
#### appendChild
这个其实前面已经多次用到了，语法就是：
```
parent.appendChild(child);
```

它会将child追加到parent的子节点的最后面。另外，如果被添加的节点是一个页面中存在的节点，则执行后这个节点将会添加到新的位置，其原本所在的位置将移除该节点，也就是说不会同时存在两个该节点在页面上，且其事件会保留。

#### insertBefore
将某个节点插入到另外一个节点的前面，语法：
```
parentNode.insertBefore(newNode, refNode);
```
这个API个人觉得设置的非常不合理，因为插入节点只需要知道newNode和refNode就可以了，parentNode是多余的，所以jQuery封装的API就比较好：
```
newNode.insertBefore(refNode); // 如 $("p").insertBefore("#foo");
```

所以切记不要把这个原生API和jQuery的API使用方法搞混了！为了加深理解，这里写一个简单的例子：

```
<div id="parent">
    我是父节点
    <div id="child">
        我是旧的子节点
    </div>
</div>
<input type="button" id="insertNode" value="插入节点" />
<script>
var parent = document.getElementById("parent");
var child = document.getElementById("child");
document.getElementById("insertNode").addEventListener('click', function()
{
    var newNode = document.createElement("div");
    newNode.textContent = "我是新节点";
    parent.insertBefore(newNode, child);
}, false);
</script>
```


关于第二个参数：

    refNode是必传的，如果不传该参数会报错；
    
    如果refNode是undefined或null，则insertBefore会将节点添加到末尾；
    
#### removeChild
removeChild用于删除指定的子节点并返回子节点，语法：
```
var deletedChild = parent.removeChild(node);
```

deletedChild指向被删除节点的引用，它仍然存在于内存中，可以对其进行下一步操作。另外，如果被删除的节点不是其子节点，则将会报错。一般删除节点都是这么删的：

```
function removeNode(node)
{
    if(!node) return;
    if(node.parentNode) node.parentNode.removeChild(node);
}
```
#### replaceChild
replaceChild用于将一个节点替换另一个节点，语法：
```
parent.replaceChild(newChild, oldChild);
```

### 节点关系API
DOM中的节点相互之间存在着各种各样的关系，如父子关系，兄弟关系等。
#### 父关系API
    parentNode ：每个节点都有一个parentNode属性，它表示元素的父节点。Element的父节点可能是Element，Document或DocumentFragment；
    
    parentElement ：返回元素的父元素节点，与parentNode的区别在于，其父节点必须是一个Element元素，如果不是，则返回null；
    
#### 子关系API
    children ：返回一个实时的 HTMLCollection ，子节点都是Element，IE9以下浏览器不支持；
    
    childNodes ：返回一个实时的 NodeList ，表示元素的子节点列表，注意子节点可能包含文本节点、注释节点等；
    
    firstChild ：返回第一个子节点，不存在返回null，与之相对应的还有一个 firstElementChild ；
    
    lastChild ：返回最后一个子节点，不存在返回null，与之相对应的还有一个 lastElementChild ；

#### 兄弟关系型API
    previousSibling ：节点的前一个节点，如果不存在则返回null。注意有可能拿到的节点是文本节点或注释节点，与预期的不符，要进行处理一下。
    
    nextSibling ：节点的后一个节点，如果不存在则返回null。注意有可能拿到的节点是文本节点，与预期的不符，要进行处理一下。
    
    previousElementSibling ：返回前一个元素节点，前一个节点必须是Element，注意IE9以下浏览器不支持。
    
    nextElementSibling ：返回后一个元素节点，后一个节点必须是Element，注意IE9以下浏览器不支持。
    
### 元素属性型API
#### setAttribute
给元素设置属性：
```
element.setAttribute(name, value);
```
其中name是特性名，value是特性值。如果元素不包含该特性，则会创建该特性并赋值。

#### getAttribute
getAttribute返回指定的特性名相应的特性值，如果不存在，则返回null：
```
var value = element.getAttribute("id");
```

### 样式相关API
#### 直接修改元素的样式
```
elem.style.color = 'red';
elem.style.setProperty('font-size', '16px');
elem.style.removeProperty('color');
```
#### 动态添加样式规则
```
var style = document.createElement('style');
style.innerHTML = 'body{color:red} #top:hover{background-color: red;color: white;}';
document.head.appendChild(style);
```
#### window.getComputedStyle
通过 element.sytle.xxx 只能获取到内联样式，借助 window.getComputedStyle 可以获取应用到元素上的所有样式，IE8或更低版本不支持此方法。
```
var style = window.getComputedStyle(element[, pseudoElt]);
```

#### getBoundingClientRect
getBoundingClientRect 用来返回元素的大小以及相对于浏览器可视窗口的位置，用法如下：
```
var clientRect = element.getBoundingClientRect();
```
clientRect是一个 DOMRect 对象，包含width、height、left、top、right、bottom，它是相对于窗口顶部而不是文档顶部，滚动页面时它们的值是会发生变化的。

![image](https://img-blog.csdn.net/20161129101404582?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center)
