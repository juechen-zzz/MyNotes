# HTML

* Hyper Text Markup Language，超文本标记语言 
* W3C标准
	* **结构**化标准语言：HTML、XML
	* **表现**标准语言：CSS
	* **行为**标准：DOM、ECMAScript

```html
<!-- DOCTYPE：告诉浏览器，需要使用什么规范 -->
<!DOCTYPE html>
<!-- 总标签 -->
<html lang="en">

<!-- head代表网页头部 -->
<head>
    <!-- meta描述性标签，描述网站的一些信息 -->
    <meta charset="UTF-8">      
    <meta name="keywords" content="komorebi">
    <title>My first web</title>
</head>

<!-- body代表网页主体 -->
<body>
Hello, world
</body>
</html>
```

## 一、基本标签

### 1.1 段落标签

```html
<h1>一级</h1>
<h2>二级</h2>
<h3>三级</h3>
<h4>四级</h4>
<h5>五级</h5>
<h6>六级</h6>
```

### 1.2 段落标签

```HTML
<p>陌上人如玉</p>
```

### 1.3 换行标签

```html
公子世无双<br/>
```

### 1.4 水平线标签

```html
<hr/>
```

### 1.5 粗体、斜体

```html
<strong>I</strong>
<em>am</em>
```

### 1.6 特殊符号

```HTML
空格			&nbsp;
大于			&gt;
小于 			&lt;
```

###  1.7 图像

<img src="../images/image-20201010204314003.png" alt="image-20201010204314003" style="zoom:50%;" />

```html
<img src="path" alt="text" title="text" width="x" height="y"/>
```

* src, alt必填

### 1.8 链接

```HTML
<a href="https://www.baidu.com" target="_blank">点击跳转（可以改成图片标签）</a>
```

* herf：必填，表示要跳转到哪个页面

* target：表示窗口**在哪里打开**

	* _blank：在新标签中打开
	* _self：在自己的网页中打开

* 锚链接

	```HTML
	<!-- 使用name作为标记 -->
	<a name="top">顶部</a>
	
	<!-- 锚链接 -->
	<a href="#top" target="_blank">回到顶部</a>
	```

* 邮件链接

	```HTML
	<!-- 功能性链接 -->
	<a href="mailto:nihaopeng1997@126.com">点击联系我</a>
	```

	