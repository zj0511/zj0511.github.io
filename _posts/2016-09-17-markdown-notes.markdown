---
date: 2016-09-17 23:42
status: public
title: Markdown语法的简要规则
layout: post
---

# 段落与换行

HTML的区段（行内）标签如`<span>` 、`<cite>` 、`<del>` 可以在 Markdown的段落、列表或是标题里随意使用。依照个人习惯，甚至可以不用 Markdown格式，而直接采用HTML标签来格式化。举例说明：如果比较喜欢 HTML 的`<a>`或`<img>`标签，可以直接使用这些标签，而不用 Markdown提供的链接或是图像标签语法。  

相邻两行文本，如果中间没有空行会显示在一行中（换行符被转换为空格）

多加空格来产生`<br/>` ，但是简单地每个换行都转换为`< br/>`的方法在Markdown中并不适合. 

***  

# 标题

Markdown支持两种标题的语法，类Setext和类atx形式。

（1）类 Setext 形式是用底线的形式，利用`=`（最高阶标题）和`-`（第二阶标题），任何数量的`=` 和`-` 都可以有效果，例如：

```md
This is an H1
=============
This is an H2
-------------
```
        
（2）类Atx形式则是在行首插入1到6个`#`，对应到标题1到6阶，例如：

```md
# 这是H1
### 这是H3
###### 这是H6
```

***

# 区块引用Blockquotes

（1）Markdown标记区块引用是使用类似email中用 `>` 的引用方式，例如：

```md
> This is a blockquote with two paragraphs. Lorem ipsum dolor sit amet,
> consectetuer adipiscing elit. Aliquam hendrerit mi posuere lectus.
> Vestibulum enim wisi, viverra nec, fringilla in, laoreet vitae, risus.
> 
> Donec sit amet nisl. Aliquam semper ipsum sit amet velit. Suspendisse
> id sem consectetuer libero luctus adipiscing.
```

> This is a blockquote with two paragraphs. Lorem ipsum dolor sit amet,
> consectetuer adipiscing elit. Aliquam hendrerit mi posuere lectus.
> Vestibulum enim wisi, viverra nec, fringilla in, laoreet vitae, risus.
> 
> Donec sit amet nisl. Aliquam semper ipsum sit amet velit. Suspendisse
> id sem consectetuer libero luctus adipiscing.

（2）Markdown也允许你偷懒只在整个段落的第一行最前面加上 `>` ，例如：

```md
> This is a blockquote with two paragraphs. Lorem ipsum dolor sit amet,
consectetuer adipiscing elit. Aliquam hendrerit mi posuere lectus.
Vestibulum enim wisi, viverra nec, fringilla in, laoreet vitae, risus.
> Donec sit amet nisl. Aliquam semper ipsum sit amet velit. Suspendisse
id sem consectetuer libero luctus adipiscing.
```

> This is a blockquote with two paragraphs. Lorem ipsum dolor sit amet,
consectetuer adipiscing elit. Aliquam hendrerit mi posuere lectus.
Vestibulum enim wisi, viverra nec, fringilla in, laoreet vitae, risus.
> Donec sit amet nisl. Aliquam semper ipsum sit amet velit. Suspendisse
id sem consectetuer libero luctus adipiscing.

（3）区块引用可以嵌套（例如：引用内的引用），只要根据层次加上不同数量的 `>` ：

```md
> This is the first level of quoting.
>
> > This is nested blockquote.
>
> Back to the first level.
```

> This is the first level of quoting.
>
> > This is nested blockquote.
>
> Back to the first level.

（4）引用的区块内也可以使用其他的Markdown语法，包括标题、列表、代码区块等：

```md
> ## 这是一个标题。
> 
> 1.   这是第一行列表项。
> 2.   这是第二行列表项。
> 
> 给出一些例子代码：
> 
>     return shell_exec("echo $input | $markdown_script");
```

> ## 这是一个标题。
> 
> 1.   这是第一行列表项。
> 2.   这是第二行列表项。
> 
> 给出一些例子代码：
> 
>     return shell_exec("echo $input | $markdown_script");

***

# 列表

Markdown支持有序列表和无序列表。

（1）有序列表则使用数字接着一个英文句点：

```md
1.  Bird
2.  McHale
3.  Parish
```

1.  Bird
2.  McHale
3.  Parish

（2）无序列表使用`星号`、`加号`或是`减号`作为列表标记：

```md
*   Red
*   Green
*   Blue
```

*   Red
*   Green
*   Blue

等同于：

```md
+   Red
+   Green
+   Blue
```

+   Red
+   Green
+   Blue

也等同于：

```md
-   Red
-   Green
-   Blue
```

-   Red
-   Green
-   Blue

（3）嵌套的列表：

```md
1. 第一层
  + 1-1
  + 1-2
2. 无序列表和有序列表可以随意相互嵌套
  1. 2-1
  2. 2-2
```

1. 第一层
  + 1-1
  + 1-2
2. 无序列表和有序列表可以随意相互嵌套
  1. 2-1
  2. 2-2

（4）列表项目标记通常是放在最左边，但是其实也可以缩进，最多3个空格，项目标记后面则一定要接着至少一个空格或制表符。

（5）要让列表看起来更漂亮，你可以把内容用固定的缩进整理好：

```md
*   Lorem ipsum dolor sit amet, consectetuer adipiscing elit.
    Aliquam hendrerit mi posuere lectus. Vestibulum enim wisi,
    viverra nec, fringilla in, laoreet vitae, risus.
*   Donec sit amet nisl. Aliquam semper ipsum sit amet velit.
    Suspendisse id sem consectetuer libero luctus adipiscing.
```
*   Lorem ipsum dolor sit amet, consectetuer adipiscing elit.
    Aliquam hendrerit mi posuere lectus. Vestibulum enim wisi,
    viverra nec, fringilla in, laoreet vitae, risus.
*   Donec sit amet nisl. Aliquam semper ipsum sit amet velit.
    Suspendisse id sem consectetuer libero luctus adipiscing.
    
（6）列表项目可以包含多个段落，每个项目下的段落都必须缩进4个空格或是1个制表符：

```md
*  This is a list item with two paragraphs. Lorem ipsum dolor
   sit amet, consectetuer adipiscing elit. Aliquam hendrerit
   mi posuere lectus.
   Vestibulum enim wisi, viverra nec, fringilla in, laoreet
   vitae, risus. Donec sit amet nisl. Aliquam semper ipsum
   sit amet velit.
*  Suspendisse id sem consectetuer libero luctus adipiscin
```
*  This is a list item with two paragraphs. Lorem ipsum dolor
   sit amet, consectetuer adipiscing elit. Aliquam hendrerit
   mi posuere lectus.
   Vestibulum enim wisi, viverra nec, fringilla in, laoreet
   vitae, risus. Donec sit amet nisl. Aliquam semper ipsum
   sit amet velit.
*  Suspendisse id sem consectetuer libero luctus adipiscing.

（7）如果要在列表项目内放进引用，那`>`就需要缩进：

```md
*   A list item with a blockquote:
   
    > This is a blockquote
    > inside a list item.       

```

*   A list item with a blockquote:
   
    > This is a blockquote
    > inside a list item.       

（8）如果要放代码区块的话，该区块就需要缩进两次，也就是8个空格或是2个制表符：

```md
*   一列表项包含一个列表区块：

        <代码写在这>
```

*   一列表项包含一个列表区块：

        <代码写在这>
        
（9）在行首出现`数字-句点-空白`时（项目表），可以在句点前面加上`反斜杠`，例如：

```md
1986\. What a great season.
```

1986\. What a great season.

***

# 代码区块

（1）要在Markdown中建立代码区块很简单，只要简单地缩进4个空格或是1个制表符就可以，例如，下面的输入：

这是一个普通段落：

    这是一个代码区块。

一个代码区块会一直持续到没有缩进的那一行（或是文件结尾）。

代码区块中，一般的Markdown语法不会被转换，像是星号便只是星号，这表示你可以很容易地以Markdown语法撰写Markdown语法相关的文件。代码块前后需要有至少一个空行，且每行代码前需要有至少一个选项卡或四个空格。

（2）也可以通过\`\`，插入行内代码（\`的英文Tab键上边，数字1键左侧的那个按键），例如： 
`<title>Markdown</title>`

***

# 分割线

你可以在一行中用三个以上的`星号`、`减号`、`底线`来建立一个分隔线，行内不能有其他东西。你也可以在`星号`或是`减号`中间插入空格。下面每种写法都可以建立分隔线：

```md
***
_ _ _
---------------------------------------
```

***
_ _ _
---------------------------------------

***

# 链接

Markdown支持两种形式的链接语法：行内式和参考式两种形式。

不管是哪一种，链接文字都是用`[方括号]`来标记。

（1）要建立一个行内式的链接，只要在方块括号后面紧接着圆括号并插入网址链接即可，如果你还想要加上链接的title文字，只要在网址后面，用双引号把title文字包起来即可，例如：

```md
This is [an example](http://example.com/ "Title") inline link.
[This link](http://example.net/) has no title attribute.
```

（2）如果是要链接到同样主机的资源，可以使用相对路径：

```md
See my [About](/about/) page for details.
```    

（3）参考式的链接是在链接文字的括号后面再接上另一个方括号，而在第二个方括号里面要填入用以辨识链接的标记：

```md
This is [an example] [id] reference-style link.
```

（4）接着，在文件的任意处，你可以把这个标记的链接内容定义出来：

```md
[id]:http://example.com/  "Optional Title Here"
```

链接内容定义的形式为：
    
 *  方括号（前面可以选择性地加上至多三个空格来缩进），里面输入链接文字
 *  接着一个冒号
 *  接着一个以上的空格或制表符
 *  接着链接的网址
 *  选择性地接着title内容，可以用单引号、双引号或是括弧包着

下面这三种链接的定义都是相同：

```md
[foo]:http://example.com/  "Optional Title Here"
[foo]:http://example.com/  'Optional Title Here'
[foo]:http://example.com/  (Optional Title Here)
```
    
***

# 图片

Markdown 使用一种和链接很相似的语法来标记图片，同样也允许两种样式： 行内式和参考式。

（1）行内式的图片语法看起来像是：

```md
![Alt text](/path/to/img.jpg)
![Alt text](/path/to/img.jpg "Optional title")
```

详细叙述如下：

* 一个惊叹号 !
* 接着一个方括号，里面放上图片的替代文字
* 接着一个普通括号，里面放上图片的网址，最后还可以用引号包住并加上 * 选择性的'title'文字。

（2）参考式的图片语法则长得像这样：

```md
![Alt text][id]
```    
    
`id`是图片参考的名称，图片参考的定义方式则和连结参考一样：

```md
[id]: url/to/image  "Optional title attribute"
```

***

# 自动链接

Markdown支持以比较简短的自动链接形式来处理网址和电子邮件信箱，只要是用尖括号包起来，Markdown就会自动把它转成链接。一般网址的链接文字就和链接地址一样，例如：

```md
<http://example.com/>
```

邮址的自动链接也很类似，只是 Markdown 会先做一个编码转换的过程，把文字字符转成 16 进位码的 HTML 实体，这样的格式可以糊弄一些不好的邮址收集机器人，例如：

```md
<address@example.com>
```

Markdown 会转成：

```md
<a href="&#x6D;&#x61;i&#x6C;&#x74;&#x6F;:&#x61;&#x64;&#x64;&#x72;&#x65;
&#115;&#115;&#64;&#101;&#120;&#x61;&#109;&#x70;&#x6C;e&#x2E;&#99;&#111;
&#109;">&#x61;&#x64;&#x64;&#x72;&#x65;&#115;&#115;&#64;&#101;&#120;&#x61;
&#109;&#x70;&#x6C;e&#x2E;&#99;&#111;&#109;</a>
```

***

# 强调

Markdown使用`星号（*）`和`底线（_）`作为标记强调字词的符号，被`* `或`_`包围的字词会被转成用 `<em>` 标签包围，用两个`*`或`_`包起来的话，则会被转成`<strong>`，例如：

```md
*single asterisks*
_single underscores_
**double asterisks**
__double underscores__
```

如果要在文字前后直接插入普通的星号或底线，你可以用反斜线：

```md
\*this text is surrounded by literal asterisks\*
```

***

# 字符转义

Markdown可以利用反斜杠来插入一些在语法中有其它意义的符号，例如：如果你想要用星号加在文字旁边的方式来做出强调效果（但不用`<em>`标签），你可以在星号的前面加上反斜杠：

```md
\*literal asterisks\*
```    

\*literal asterisks\*
    
Markdown 支持以下这些符号前面加上反斜杠来帮助插入普通的符号：

```md
\   反斜线
`   反引号
*   星号
_   底线
{}  花括号
[]  方括号
()  括弧
#   井字号
+   加号
-   减号
.   英文句点
!   惊叹号
```

***

# 代码快和语法高亮

（1）如果要标记一小段行内代码，你可以用`反引号`把它包起来（`），例如：

```md
Use the `printf()` function.
```

Use the `printf()` function.

（2）如果要在代码区段内插入反引号，你可以用多个反引号来开启和结束代码区段：

```md
``There is a literal backtick (`) here.``
```

``There is a literal backtick (`) here.``

（3）代码区段的起始和结束端都可以放入一个空白，起始端后面一个，结束端前面一个，这样你就可以在区段的一开始就插入反引号：

```md
A single backtick in a code span: `` ` ``
A backtick-delimited string in a code span: `` `foo` ``
```

A single backtick in a code span: `` ` ``
A backtick-delimited string in a code span: `` `foo` ``

（4）使用``````来包含多行代码，例如：

    ```
    <p>code here</p>
    ```

（5）代码高亮：

在上面的代码块语法基础上，添加代码的语言，如“JavaScript的'或'JS'，即可将代码标记为`JavaScript`：

    ```js
    window.addEventListener('load', function() {
      console.log('window loaded');
    });
    ```

```js
window.addEventListener('load', function() {
  console.log('window loaded');
});
```

***

# 表格

（1）单元格和表头
使用`|`来分隔不同的单元格，使用`-`来分隔表头状语从句：其他行：

```md
name | age
---- | ---
LearnShare | 12
Mike |  32
```

name | age
---- | ---
LearnShare | 12
Mike |  32

为了美观，可以使用空格对齐不同行的单元格，并左右在都两侧使用`|`来标记单元格边界：

```md
|    name    | age |
| ---------- | --- |
| LearnShare |  12 |
| Mike       |  32 |
```

（2）对齐：

在表头下方的分隔线标记中加入`:`，即可标记下方单元格内容的对齐方式：

:--- 代表左对齐
:--: 代表居中对齐
---: 代表右对齐

```md
| left | center | right |
| :--- | :----: | ----: |
| aaaa | bbbbbb | ccccc |
| a    | b      | c     |
```

| left | center | right |
| :--- | :----: | ----: |
| aaaa | bbbbbb | ccccc |
| a    | b      | c     |

（3）插入其他内容：

表格中可以插入其他的行内标记：

```md
|     name     | age |             blog                |
| ------------ | --- | ------------------------------- |
| _LearnShare_ |  12 | [LearnShare](http://xianbai.me) |
| __Mike__     |  32 | [Mike](http://mike.me)          |
```

|     name     | age |             blog                |
| ------------ | --- | ------------------------------- |
| _LearnShare_ |  12 | [LearnShare](http://xianbai.me) |
| __Mike__     |  32 | [Mike](http://mike.me)          |

***

# 任务列表

```md
- [ ] Eat
- [x] Code
  - [x] HTML
  - [x] CSS
  - [x] JavaScript
- [ ] Sleep
```

- [ ] Eat
- [x] Code
  - [x] HTML
  - [x] CSS
  - [x] JavaScript
- [ ] Sleep

***