`meta`标签用来提供有关html文档的元数据，元数据不会显示给用户看，但浏览器可以识别，同时对`SEO`（搜索引擎优化）起着重要作用。

通过`name-content`来介绍网页信息。

- author

网页作者

```html
<meta name="author" content="xxx@xx.com"></meta>
```

- description

网页的描述

```html
<meta name="description" content="xxxxx"></meta>
```

- keywords

网页关键字，提取网页的关键字贡搜索引擎查询

```html
<meta name="keywords" content="游戏、动作"></meta>
```

- viewport

为`viewport`（视口）的初始大小提供指示。目前仅用于移动设备。

`width`用来设置viewport的宽度为设备宽度

`initial-scale`为设备宽度与viewport大小之间的缩放比例

```html
<meta name="viewport" content="width=device-width, initial-scale=1.0"></meta>
```

- robots

表示爬虫对此页面的处理行为，或者说应当遵守的规则，用来给搜索引擎抓取的。

`all`: 搜索引擎可以抓取本页面，也可以通过本页面中的链接抓取其他页面

`none`: 搜索引擎不能抓取本页面，也不能通过本页面中的链接抓取其他页面

`index`：搜索引擎可以抓取本页面

`noindex`： 搜索引擎不能抓取本页面，但可以通过本页面中的链接抓取其他页面

`follow`：搜索引擎可以通过本页面的链接抓取其他页面

`nofollow`： 禁止搜索引擎通过本页面中的链接抓取其他页面

```html
<meta name="robots" content="all"></meta>
```

- renderer（渲染器）

用来指定双核浏览器的渲染方式，比如360浏览器

```html
<meta name="renderer" content="webkit"></meta> // 默认webkit内核
<meta name="renderer" content="ie-comp"></meta> // 默认IE兼容模式
<meta name="renderer" content="ie-stand"></meta> // 默认IE标准模式
```

- X-UA-Compatible

它是用来做IE浏览器适配的。

`IE=edge`告诉浏览器，以当前浏览器支持的最新版本来做渲染，`IE9`就以`IE9`版本来渲染

`chrome=1`告诉浏览器，如果当前IE浏览器安全了`Google Chrome Frame`插件，就以`chrome`内核来渲染页面。

```html
<meta name="X-UA-Compatible" content="IE=edge,chrome=1">
```

- content-type

用来声明文档类型和字符集

```html
<meta name="content-type" content="text/html;charset=utf-8">
```

`charset`还可以写成

```html
<meta charset="UTF-8">
```

- x-dns-prefetch-control（dns预解析）

一般来说，`HTML`页面中的a标签会自动启用`DNS`提前解析来提升网站性能，但是在使用`https`协议的网站中失效，我们可以用这个开打开`dns`对`a`标签的提前解析

```html
<meta name="x-dns-prefetch-control" content="on">
```

- referer (请求来源)

`meta`标签中的`name=referer`属性主要用于控制客户端发送给服务端的`referer`信息，告诉服务端一些客户端的信息，如：来源网页的地址。

`nerver`：删除`http head`中的`referer`

`origin`:  只发送`origin`部分

`always`：不改变`http header`中的`referer`的值

```html
<meta name="referer" content="no-referer">
```

- 缓存

`cache-control`、`pragma`、`expires`和缓存设置相关，但是这些设置往往不生效，一般通过`http headers`来设置缓存策略。
