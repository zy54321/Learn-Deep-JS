[js中Window 对象及其的方法](https://www.cnblogs.com/wqhwe/p/5647061.html)

https://www.cnblogs.com/wqhwe/p/5647061.html

### window.location 对象

window.location 对象用于获得当前页面的地址 (URL)，并把浏览器重定向到新的页面。

window.location 对象在编写时可不使用 window 这个前缀。

location.hostname 返回 web 主机的域名

location.pathname 返回当前页面的路径和文件名

location.port 返回 web 主机的端口 （80 或 443）

location.protocol 返回所使用的 web 协议（http:// 或 https://）

window.location.href= "http://www.xxxxxxxx.net" ; 跳转后有后退功能

window.location.replace("http://www.xxxxxxxx.net") ; 跳转后没有后退功能

window.open("http://www.xxxxxxxx.net");  在新的窗口打开链接，一般用于简单的弹出页面，现在基本上都被屏蔽掉　

window.location.reload( ); 刷新当前页面.

parent.location.reload( ); 刷新父亲对象（用于框架）

opener.location.reload( ); 刷新父窗口对象（用于单开窗口）

top.location.reload( ); 刷新最顶端对象（用于多开窗口）

### window.history 对象

window.history 对象包含浏览器的历史。window.history对象在编写时可不使用 window 这个前缀。

window.history.back() - 加载历史列表中的前一个 URL，与在浏览器点击后退按钮相同，

window.history.forward() -加载历史列表中的下一个 URL。 与在浏览器中点击按钮向前相同

### window.navigator 对象

window.navigator 对象包含有关访问者浏览器的信息，来自 navigator 对象的信息具有误导性，不应该被用于检测浏览器版本，这是因为：

```
navigator 数据可被浏览器使用者更改

一些浏览器对测试站点会识别错误

浏览器无法报告晚于浏览器发布的新操作系统
```

window.navigator 对象在编写时可不使用 window 这个前缀。

window.navigator.appCodeName返回浏览器的代码名。

window.navigator.appName返回代表浏览器名的字符串。

window.navigator.appMinorVersion返回浏览器的次版本号。该属性是一个只读的字符串。仅IE有效。

window.navigator.userAgent返回代表浏览器名和版本号的字符串。

window.navigator.platform返回浏览器平台的字符串（"Win32", "Win16", "WinCE", "Mac68k", "MacPPC", "HP-UX", "SunOS" 等）。

window.navigator.cpuClass 返回CPU的信息（"x86", "68K", "Alpha", "PPC" 等）。仅IE有效。

window.navigator.browserLanguage返回浏览器的语言种类。仅IE有效。

window.navigator.systemLanguage返回系统的语言种类。仅IE有效。

window.navigator.userLanguage userLanguage 返回用户环境的语言种类。仅IE有效。

window.navigator.cookieEnabled返回 cookie 是否可用的真伪值。

window.navigator.onLine返回是否能连上网络的真伪值。

window.navigator.javaEnabled()返回 Java 是否可用的真伪值。

window.navigator.userProfile保存着用户信息的对象。拥有 addReadRequest() doReadRequest() getAttribute() clearRequest() 等方法。

window.navigator.taintEnabled() 是否可以加密数据的真伪值。仅IE有效。

### window对象的一些其它方法

setInterval() 和 setTimeout() 是 HTML DOM Window对象的两个方法。

window.setInterval() - 间隔指定的毫秒数不停地执行指定的代码。

window.setTimeout() - 暂停指定的毫秒数后执行指定的代码

window.clearInterval() 方法用于停止 setInterval() 方法执行的函数代码。

window.clearTimeout() 方法用于停止执行setTimeout()方法的函数代码。

window.alert()- 警告框经常用于确保用户可以得到某些信息。当警告框出现后，用户需要点击确定按钮才能继续进行操作。

window.prompt()- 确认框用于使用户可以验证或者接受某些信息。当确认框出现后，用户需要点击确定或者取消按钮才能继续进行操作。如果用户点击确认，那么返回值为 true。如果用户点击取消，那么返回值为 false。

window.confirm()- 提示框经常用于提示用户在进入页面前输入某个值。当提示框出现后，用户需要输入某个值，然后点击确认或取消按钮才能继续操纵。如果用户点击确认，那么返回值为输入的值。如果用户点击取消，那么返回值为 null。

window.open() - 打开新窗口

window.close() - 关闭当前窗口

window.moveTo() - 移动当前窗口

window.resizeTo() - 调整当前窗口的尺寸

window.frameElement 获取当前文档的宿主节点iframe元素

window.execScript() execScript函数与eval的功能相同，不同的是eval函数执行后的脚本的作用域是当前执行上下文，而execScript则总是针对全局作用域。