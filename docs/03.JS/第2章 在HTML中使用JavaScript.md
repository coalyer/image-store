## 第2章 在HTML中使用JavaScript

***1. 本章内容：***

- 使用\<script>元素
- 嵌入脚本与外部脚本
- 文档模式对JavaScript 的影响
- 考虑禁用JavaScript 的场景

***2. 在HTML 中使用JavaScript：***

把JavaScript 放到网页中，就不得不涉及Web 的核心语言——HTML。最终的决定就是为Web 增加**统一的脚本支持，并被正式纳入HTML 规范当中**。       

### 2.1 \<script>元素

***1. script元素6个属性：***

| 属性       | 说明                                                         |
| ---------- | ------------------------------------------------------------ |
| `async`    | 可选。表示应该**立即下载脚本，但不应妨碍页面中的其他操作**，比如下载其他资源或等待加载其他脚本。只对外部脚本文件有效。 |
| `charset`  | 可选。表示通过src 属性指定的代码的字符集。由于大多数浏览器会忽略它的值，因此这个属性很少有人用。 |
| `defer`    | 可选。表示脚本可以**延迟到文档完全被解析和显示之后再执行**。只对外部脚本文件有效。IE7 及更早版本对嵌入脚本也支持这个属性。 |
| `language` | 已废弃。原来用于表示编写代码使用的脚本语言（JavaScript、JavaScript1.2或VBScript）。大多数浏览器会忽略这个属性，因此也没有必要再用了。 |
| `src`      | 可选。表示包含要执行代码的外部文件。                         |
| `type`     | 可选。可以看成是language 的替代属性；**表示编写代码使用的脚本语言的内容类型（也称为MIME 类型）**。<br />人们一直以来使用的都还是**text/javascript(text/ecmascript)** ，服务器在传送JavaScript 文件时使用**application/x–javascript** ，非IE浏览器中还可以使用以下值：a**pplication/javascript** 和**application/ecmascript** |

***2. 使用\<script>元素的两种方式：***

1. 直接在页面中嵌入JavaScript 代码

   - 错误

     ```html
     <script type="text/javascript">
     function sayScript(){
     	alert("</script>");
     }
     </script>
     ```

   - 正确

     ```html
     <script type="text/javascript">
     function sayScript(){
     	alert("<\/script>");
     }
     </script>
     ```

     （通过转义字符“/”可以解决这个问题）

2. 包含外部JavaScript文件

   - 错误

     ```html
     <script type="text/javascript" src="example.js" />
     ```

     （如果是在XHTML 文档中，也可以省略前面示例代码中结束的</script>标签，但是，不能在HTML 文档使用这种语法，原因是这种语法不符合HTML 规范）

   - 正确

     ```html
     <script type="text/javascript" src="example.js"></script>
     ```

     如果不使用.js 扩展名，请确保服务器能返回正确的MIME 类型。

***3. 在解析JavaScript 时，页面的处理也会暂时停止***

#### 2.1.1 标签的位置

***1. 标签的位置：***

- 按照传统的做法，所有\<script>元素都应该放在页面的\<head>元素中

  ```html
  <!DOCTYPE html>
  <html>
  	<head>
  		<title>Example HTML Page</title>
  		<script type="text/javascript" src="example1.js"></script>
  		<script type="text/javascript" src="example2.js"></script>
  	</head>
  	<body>
  		<!-- 这里放内容 -->
  	</body>
  </html>
  ```

- 现代Web 应用程序一般都把全部JavaScript 引用放在\<body>元素中页面内容的后面。用户也会因为浏览器窗口显示空白页面的时间缩短而感到打开页面的速度加快了。

  ```html
  <!DOCTYPE html>
  <html>
  	<head>
  		<title>Example HTML Page</title>
  	</head>
  	<body>
  		<!-- 这里放内容 -->
  		<script type="text/javascript" src="example1.js"></script>
  		<script type="text/javascript" src="example2.js"></script>
  	</body>
  </html>
  ```

#### 2.1.2 延迟脚本

***1. defer 属性：***

```html
<!DOCTYPE html>
<html>
 
<head>
  <title>Example HTML Page</title>
  <script type="text/javascript" defer="defer" src="example1.js"></script>
  <script type="text/javascript" defer="defer" src="example2.js"></script>
</head>
 
<body>
  <!-- 这里放内容 -->
</body>
 
</html>
```

***2. 延迟脚本特点：***

- 执行顺序：
  - 脚本会被延迟到整个页面都解析完毕后再运行
  - 第一个延迟脚本会先于第二个延迟脚本执行
    - 这两个脚本会先于DOMContentLoaded 事件（[详见第13 章]())）       
- defer 属性只适用于外部脚本文件。这一点在HTML5 中已经明确规定。
- 浏览器对defer支持性不一，把延迟脚本放在页面底部仍然是最佳选择。 

#### 2.1.3 异步脚本

***1. async属性：***

```html
<html>
 
<head>
  <title>Example HTML Page</title>
  <script type="text/javascript" async src="example1.js"></script>
  <script type="text/javascript" async src="example2.js"></script>
</head>
 
<body>
  <!-- 这里放内容 -->
</body>
 
</html>
```

***2. 异步脚本特点：***

- 执行顺序：
  - 第二个脚本文件可能会在第一个脚本文件之前执行，确保两者之间互不依赖非常重要
  - 异步脚本**不要在加载期间修改DOM**
  - 异步脚本一定会在页面的load 事件前执行，但**可能会在DOMContentLoaded 事件触发之前或之后执行**       
- async属性只适用于外部脚本文件。

***3. async属性与defer属性异同：***

| 异                                                           | 同                                                           |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| **标记为async 的脚本并不保证按照指定它们的先后顺序执行（延迟和异步的区别）<br />在XHTML 文档中为defer="defer"。为async="async"。** | **用于改变处理脚本的行为<br />只适用于外部脚本文件<br />告诉浏览器立即下载文件** |

#### 2.1.4 在XHTML中的用法

***1. 本节内容可以跳过***

HTML5 正快速地被前端开发人员采用，建议读者在学习和开发中遵循HTML5 标准，本节内容可以跳过。

***2. XHTML定义：***

可扩展超文本标记语言，即XHTML（Extensible HyperText Markup Language），是将HTML 作为XML 的应用而重新定义的一个标准。

***3. 比编写HTML 严格得多：***

- 比较语句a < b 中的小于号（<）在XHTML 中将被当作开始一个新标签来解析。用相应的HTML 实体（&lt;）替换代码中所有的小于号（<）

  ```html
  <script type="text/javascript">
    function compare(a, b) {
      if (a &lt; b) {
        alert("A is less than B");
      } else if (a > b) {
        alert("A is greater than B");
      } else {
        alert("A is equal to B");
      }
    }
   
  </script>
  ```

- 用一个CData 片段来包含JavaScript 代码

  ```html
  <script type="text/javascript">
    <![CDATA[
    function compare(a, b) {
      if (a < b) {
        alert("A is less than B");
      } else if (a > b) {
        alert("A is greater than B");
      } else {
        alert("A is equal to B");
      }
    }
    ]]>
  </script>
  ```

- 有不少浏览器不兼容XHTML

  ```html
  <script type="text/javascript">
    // <![CDATA[
    function compare (a, b) {
      if (a < b) {
        alert('A is less than B')
        } else if (a > b) {
        alert('A is greater than B')
        } else {
        alert('A is equal to B')
        }
    }
    // ]]>
  </script>
  ```

***4. 如何触发：***

在将页面的MIME 类型指定为"application/xhtml+xml"的情况下会触发XHTML 模式。

#### 2.1.5 不推荐使用的语法

***1. 让不支持\<script>元素的浏览器能够隐藏嵌入的JavaScript 代码：***

```html
<script><!--
  function sayHi() {
    alert("Hi!");
  }
  //
--></script>
```

由于所有浏览器都已经支持JavaScript，因此也就没有必要再使用这种格式了

### 2.2 嵌入代码与外部文件

***1. 嵌入代码与外部文件***

一般认为最好的做法还是尽可能使用外部文件来包含JavaScript 代码

### 2.3 文档模式

***1. 文档类型（doctype）：***

- **混杂模式**（quirks mode）：让IE 的行为与（包含非标准特性的）IE5 相同

  如果在文档开始处没有发现文档类型声明，则所有浏览器都会默认开启混杂模式。  

- **标准模式**（standards mode）:让IE 的行为更接近标准行为

  ```html
  <!-- HTML 4.01 严格型 -->
  <!DOCTYPE HTML PUBLIC "-//W3C//DTD HTML 4.01//EN" "http://www.w3.org/TR/html4/strict.dtd">
   
  <!-- XHTML 1.0 严格型 -->
  <!DOCTYPE html PUBLIC "-//W3C//DTD XHTML 1.0 Strict//EN" "http://www.w3.org/TR/xhtml1/DTD/xhtml1-strict.dtd">
   
  <!-- HTML 5 -->
  <!DOCTYPE html>
  ```

- IE 又提出一种所谓的**准标准模式**（almost standards mode）:通过使用过渡型（transitional）或框架集型（frameset）文档类型来触发

  准标准模式与标准模式非常接近，它们的差异几乎可以忽略不计。

  不标准的地方主要体现在处理图片间隙的时候（在表格中使用图片时问题最明显）。

  ```html
  <!-- HTML 4.01 过渡型 -->
  <!DOCTYPE HTML PUBLIC "-//W3C//DTD HTML 4.01 Transitional//EN" "http://www.w3.org/TR/html4/loose.dtd">
   
  <!-- HTML 4.01 框架集型 -->
  <!DOCTYPE HTML PUBLIC "-//W3C//DTD HTML 4.01 Frameset//EN" "http://www.w3.org/TR/html4/frameset.dtd">
   
  <!-- XHTML 1.0 过渡型 -->
  <!DOCTYPE html
    PUBLIC "-//W3C//DTD XHTML 1.0 Transitional//EN" "http://www.w3.org/TR/xhtml1/DTD/xhtml1-transitional.dtd">
   
  <!-- XHTML 1.0 框架集型 -->
  <!DOCTYPE html PUBLIC "-//W3C//DTD XHTML 1.0 Frameset//EN" "http://www.w3.org/TR/xhtml1/DTD/xhtml1-frameset.dtd">
  ```

本书后面提到标准模式时，指的是除混杂模式之外的其他模式。

### 2.4 \<noscript>元素

**1. \<noscript>元素**

当浏览器不支持JavaScript 时如何让页面平稳地退化：

```html
<html>
 
<head>
  <title>Example HTML Page</title>
  <script type="text/javascript" defer="defer" src="example1.js"></script>
  <script type="text/javascript" defer="defer" src="example2.js"></script>
</head>
 
<body>
  <noscript>
    <p>本页面需要浏览器支持（启用）JavaScript。
  </noscript>
</body>
 
</html>
```

### 2.5 小结

***1. 把JavaScript 插入到HTML 页面中要使用\<script>元素。***

注意的地方：

- 包含外部JavaScript 文件时，必须将src 属性设置为指向相应文件的URL。
- 在不使用defer 和async 属性的情况下，所有\<script>元素都会按照它们在页面中出现的先后顺序依次被解析。
- 一般应该把\<script>元素放在页面最后。
- 延迟脚本(defer 属性)：在文档完全呈现之后再执行。总是按照指定它们在页面中的顺序执行。
- 异步脚本(async 属性)：表示当前脚本不必等待其他脚本，也不必阻塞文档呈现。不能保证按照它们在页面中的顺序执行。       

另外，使用\<noscript>元素可以指定在不支持脚本的浏览器中显示的替代内容。但在启用了脚本的情况下，浏览器不会显示\<noscript>元素中的任何内容