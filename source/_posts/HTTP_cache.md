---
title: HTTP缓存机制大局观
date: 2018-03-02 21:30:00
categories: http
tags: [http] #文章标签，可空，多标签请用格式，注意:后面有个空格
toc: true
comments: true
---
网上介绍HTTP缓存机制的博客思路通常是挨个介绍HTTP缓存涉及的多个字段，这样通读下来感觉印象不深刻，并且缺少大局观，本文将尝试从更总览的角度进行总结和概括，以帮助建立知识体系。

<!-- more -->

> by MaryTien from  [http://supermaryy.com](http://luckymona.github.com)

## 前言及缓存字段分类

前端涉及到缓存这一概念的地方包括HTTP缓存、以`localStorage`为代表的本地存储缓存（`sessionStorage`、`indexDB`、`web SQL`等），本文主要介绍HTTP缓存。HTTP请求中只有GET请求可以被缓存。

网上介绍HTTP缓存机制的博客思路通常是挨个介绍HTTP缓存涉及的多个字段，这样通读下来感觉印象不深刻，并且缺少大局观，本文将尝试从更总览的角度进行总结，阅读这篇文章可以帮助你产生一点大菊观，明白轻重缓急，如果看了却产生不了，那你当我是浮夸吧，夸张只因我孩怕XDD。。

缓存是一种保存资源副本并在下次请求时直接使用该副本的技术。设置缓存的好处：减少资源显示时间提升性能；减少服务器压力。HTTP缓存机制使用多个字段来实现缓存控制，把这些字段进行分类有利于记忆和加深理解，经过梳理总结我发现大家会对这些字段有以下几种分类方法：

###1. 按照缓存判断的执行过程，分为：###

参考自[【腾讯Bugly干货分享】彻底弄懂 Http 缓存机制 - 基于缓存策略三要素分解法](https://zhuanlan.zhihu.com/p/24467558)

- 缓存存储策略：`Cache-Control: public、private、no-cache、max-age、no-store`

  前4项都可以缓存数据到本地，后者 `no-store` 则不会在客户端缓存任何响应数据。

- 缓存过期策略：`Expires`、`Cache-Control`设置`max-age`、`no-cache`的情况

- 缓存对比策略：`Etag/If-None-Match`、`Last-Modified/If-Modified-Since`

其中`max-age`和`no-cache`是个混合体，可以同时设置两个类别的内容，既包含缓存存储策略，也包含缓存过期策略，`Cache-Control:max-age=3600`相当于

```
Cache-Control:private/public
Expires:<当前时间> + 3600
```

`Cache-Control:no-cache`相当于`Cache-Control:max-age=0`，相当于

```
Cache-Control:private/publice
Expires:<当前时间>
```

在这种分类下，浏览器会依次根据**是否存储、是否过期、是否对比一致**来进行缓存判断，如果设置时缺失某一项，浏览器会根据约定好的规则为这一项设置默认值。例如没有设定具体过期时间时，浏览器会根据以下**启发式缓存过期策略**设定过期时间：

**把响应头中的Date字段和Last-Modified的差值的10%作为max-age的值，也就是作为缓存有效期**

### 2.按照HTTP不同版本的字段，分为：

- HTTP1.0中的字段：Pragma、Expires

- HTTP1.1中的字段：Cache-Control、Etag / If-None-Match、Last-Modified / If-Modified-Since等

  这种分类主要强调的是两点：

  1. 关于Pragma：设置了Pragma就相当于设置了Cache-Control：no-cache，但是HTTP响应头不支持这个属性，所以它不能拿来完全替代HTTP/1.1中定义的Cache-control头。通常定义Pragma以向后兼容基于HTTP/1.0的客户端，如低版本IE浏览器。
  2. 同时设置了max-age和Expires的情况下，Expires被覆盖，以max-age为准

### 3.按照使用频率，分为：###

- 主要字段：Expires、Cache-Control、Etag / If-None-Match、Last-Modified / If-Modified-Since
- 辅助字段：Vary、Date、Age

###4.按照能否被请求头、响应头支持，分为：###

参考自[rfc2616 14.Header Field Definitions](https://www.w3.org/Protocols/rfc2616/rfc2616-sec14.html)

​           [浅谈浏览器http的缓存机制-Vajoy](http://www.cnblogs.com/vajoy/p/5341664.html)

- 请求头字段(request-header)：If-Match、If-None-Match、If-Modified-Since、If-Unmodified-Since

- 响应头字段(response-header)：Age、Etag

- 通用头部字段(general-header)：Cache-Control、Date、Pragma

- 实体首部字段(entity-header )：Expires、Last-Modified、

实体首部字段定义：For entity-header fields, both sender and recipient refer to either the client or the server, depending on who sends and who receives the entity。在我理解，这样也就是说Client或Server可以作为接收端或发送端，具体取决于它们在一次首发过程中扮演的角色，也就是说既可以出现在请求头中也可以出现在响应头中吗？这点还不太理解。（TODO）

以下是表格：

   **1. 通用首部字段**（就是请求报文和响应报文都能用上的字段）
   ![img](https://images2015.cnblogs.com/blog/561179/201604/561179-20160401161150504-1030837643.png)

   **2. 请求首部字段**
   ![img](https://images2015.cnblogs.com/blog/561179/201604/561179-20160401161240301-2050921595.png)

   **3. 响应首部字段**
   ![img](https://images2015.cnblogs.com/blog/561179/201604/561179-20160401161311394-1246877214.png)

   **4. 实体首部字段**
   ![img](https://images2015.cnblogs.com/blog/561179/201604/561179-20160401171410441-767100632.png)

但是，MDN上说HTTP响应头不支持Pragma（TODO），并且通常Last-Modified会用在响应头里面。

## 一、常用缓存字段

### 1. 主要字段：

#### 1.Expires

- HTTP1.0时期的缓存过期控制字段，是相对于服务器时间的绝对时间，不同服务器之间的时间设置可能不同，这种不一致存在隐患
- 值为GMT格式的日期，设定在当前时间之前相当于立即过期，相当于Cache-Control：no-cache
- 与Cache-Control：max-age=<毫秒数>共存时，被其覆盖并失效
- Pragma与Expires共存时，Pragma胜出，Expires失效

#### 2.Cache-control

- **public/private**

  `public`表明响应可以被任何对象（包括：发送请求的客户端，代理服务器，等等）缓存。

  `private`表明响应只能被单个用户缓存，不能作为共享缓存（即代理服务器不能缓存它）。

- **max-age**

  值为一个毫秒数，表示缓存过期的相对时间，优先级高于Expires

- **s-maxage=<seconds>**

  覆盖max-age 或者 Expires 头，但是仅适用于共享缓存(比如各个代理)，并且私有缓存中它被忽略。

- **no-cache**

  允许保存缓存副本 ，但是不能直接使用缓存副本，必须先去服务器提交验证请求，验证是否过期、已修改，如果验证没有过期、没有修改，即返回304状态，就可以使用缓存副本

- **no-store**：不允许保存缓存副本

- **must-revalidate**

  缓存必须在使用之前验证旧资源的状态，并且不可使用过期资源。和no-cache比较相似

- **min-fresh=<seconds>**

  表示客户端希望在指定的时间内获取最新的响应。

若报文中同时出现了 Pragma、Expires 和 Cache-Control，会以 Cache-Control 为准，其它Cache-Control值略

####3.Etag / If-None-Match

- 服务器端为资源打的一个标记，标记资源有无修改，服务端资源修改后就修改Etag值
- “W\”开头的Etag表示开启弱校验，当有了较多修改之后才会修改Etag值，默认使用强校验，即按字节进行对比

####4.Last-Modified / If-Modified-Since

- Last-Modified标记资源最后被修改的时间，是一个GMT格式的时间值
- 如果在服务器上，一个资源被修改了，但其实际内容根本没发生改变，会因为Last-Modified时间匹配不上而返回了整个实体给客户端*（即使客户端缓存里有个一模一样的资源）*，所以通常配合Etag一起使用，可以达到较好的控制缓存的效果
- 如果 Last-Modified 和 ETag 同时被使用，则要求它们的验证都必须通过才会返回304，若其中某个验证没通过，则服务器会按常规返回资源实体及200状态码。

###2. 辅助字段：

####1.Vary

参考[MDN|HTTP缓存](https://developer.mozilla.org/zh-CN/docs/Web/HTTP/Caching_FAQ)

控制代理服务器直接使用缓存资源返回还是向源服务器发起请求。当缓存服务器收到一个请求，只有当前的请求和原始（缓存）的请求头跟缓存的响应头里的Vary都匹配，才能使用缓存的响应。

![The Vary header leads cache to use more HTTP headers as key for the cache.](https://mdn.mozillademos.org/files/13769/HTTPVary.png)

使用vary头有利于内容服务的动态多样性。例如，使用Vary: User-Agent头，缓存服务器需要通过UA判断是否使用缓存的页面。如果需要区分移动端和桌面端的展示内容，利用这种方式就能避免在不同的终端展示错误的布局。另外，它可以帮助google或者其他搜索引擎更好地发现页面的移动版本，并且告诉搜索引擎没有引入[Cloaking](https://en.wikipedia.org/wiki/Cloaking)。

####2.Date和Age

Date 是原服务器发送该资源响应报文的时间（GMT格式），如果你发现 Date 的时间与“当前时间”差别较大，或者连续F5刷新发现 Date 的值都没变化，则说明你当前请求是命中了代理服务器的缓存。

Age表示资源在代理服务器中存在的时间*（秒）*，如文件被修改或替换，Age会重新由0开始累计。

```
静态资源Age + 静态资源Date = 原服务端Date
```

不过这条规则也不一定准确，特别是当原服务器经常修改系统时间的情况下。

## 二、缓存判断流程

参考[Google Developer HTTP缓存](https://developers.google.com/web/fundamentals/performance/optimizing-content-efficiency/http-caching?hl=zh-cn)

![缓存决策树](http://7xpsmd.com1.z0.glb.clouddn.com/16-2-23/28130284.jpg)



这个图可以说是设置缓存的参考策略，也可以据此判断浏览器判断是否使用缓存的大概流程：

1. 由Expires、Pragma、max-age等判断缓存是否过期，未过期直接使用缓存，已过期进行下一步

2. 发起请求，请求头附带If-None-Match和If-Modified-Since，服务器由If-Modified-Since与其Last-Modified对比，如果不一致直接反回新版本的资源，否则需要再判断If-None-Match和Etag是否一致，如果不一致直接返回新版本资源，否则返回304状态码，浏览器就可以直接使用本地的缓存副本

![浏览器的缓存判断流程](http://7xpsmd.com1.z0.glb.clouddn.com/16-2-23/8668139.jpg)

另外需要注意的是，Chrome浏览器中，当缓存未过期直接使用缓存时，在Dev Tools的network选项卡中显示的是200 OK (from cache)。

## 三、前端设置

### 1. HTTP header字段设置###

涉及到前端人员需要设置的HTTP header字段有哪些呢？通过前面按照能否被请求头、响应头支持这一标准进行的分类中，我们知道请求首部字段有：If-Match / If-None-Match、If-Modified-Since / If-Unmodified-Since，这几个值通常是首先服务器发回Etag、Last-Modified，然后浏览器自动加在请求头里面的，所以一般不用前端人员进行设置。**通用首部字段有：Cache-Control、Pragma，也就是说作为前端人员，可以通过对这两个字段进行设置以控制缓存。**（TODO）

### 2. Meta http-equiv设置缓存###

HTML4中支持在meta标签的http-equiv属性中使用http头部缓存控制字段，包括Expires、Pragma、Cache-Control等。

```
<meta http-equiv="Cache-Control" content="no-cache, no-store, must-revalidate" />
<meta http-equiv="Pragma" content="no-cache" />
<meta http-equiv="Expires" content="0" />
```

> The [http-equiv](https://www.w3.org/TR/html4/struct/global.html#adef-http-equiv) attribute can be used in place of the name attribute and has a special significance when documents are retrieved via the Hypertext Transfer Protocol (HTTP). HTTP servers may use the property name specified by the [http-equiv](https://www.w3.org/TR/html4/struct/global.html#adef-http-equiv) attribute to create an [[RFC822\]](https://www.w3.org/TR/html4/references.html#ref-RFC822)-style header in the HTTP response. Please see the HTTP specification ([[RFC2616\]](https://www.w3.org/TR/html4/references.html#ref-RFC2616)) for details on valid HTTP headers.

参考自[w3.org | HTML4 | META and HTTP headers](https://www.w3.org/TR/html4/struct/global.html#h-7.4.4)

HTML5中已经不再包含这样的用法。参考自[w3.org | HTML5 | META Pragma directives](https://dev.w3.org/html5/spec-preview/the-meta-element.html#pragma-directives)

当同时在meta标签中设置了缓存控制字段并在HTTP header中设置了这些字段时，会优先使用HTTP header字段。另外有的代理服务器可能会不认这个设置，为了避免造成冲突和混乱，建议只使用HTTP header设置缓存。另外META http-equiv适用于在file:// 本地文件中使用。

> **[HTML meta tags vs HTTP response headers](https://stackoverflow.com/questions/49547/how-to-control-web-page-caching-across-all-browsers)**
>
> Important to know is that when a HTML page is served over a HTTP connection, and a header is present in **both** the HTTP response headers and the HTML `<meta http-equiv>` tags, then the one specified in the HTTP response header will get precedence over the HTML meta tag. The HTML meta tag will only be used when the page is viewed from local disk file system via a `file://` URL. See also [W3 HTML spec chapter 5.2.2](http://www.w3.org/TR/html4/charset.html#h-5.2.2). Take care with this when you don't specify them programmatically, because the webserver can namely include some default values.
>
> Generally, you'd better just **not** specify the HTML meta tags to avoid confusion by starters, and rely on hard HTTP response headers. Moreover, specifically those `<meta http-equiv>` tags are [**invalid**](http://validator.w3.org/) in HTML5. Only the `http-equiv` values listed in [HTML5 specification](http://dev.w3.org/html5/spec-preview/the-meta-element.html#attr-meta-http-equiv) are allowed.

### 3.给静态资源加版本号Revved Resource###

更多地利用缓存资源，可以提高网站的性能和相应速度。为了优化缓存，过期时间设置得尽量长是一种很好的策略。对于定期或者频繁更新的资源，这么做是比较稳妥的，但是对于那些长期不更新的资源会有点问题。这些固定的资源在一定时间内受益于这种长期保持的缓存策略，但一旦要更新就会很困难。特指网页上引入的一些js/css文件，当它们变动时需要尽快更新线上资源。

web开发者发明了一种 Steve Sounders 称作加速（译者注：revving）的技术[[1\]](https://www.stevesouders.com/blog/2008/08/23/revving-filenames-dont-use-querystring/) 。不频繁更新的文件会使用特定的命名方式：在URL后面（通常是文件名后面）会加上版本号。加上版本号后的资源就被视作一个完全新的独立的资源，同时拥有一年甚至更长的缓存过期时长。但是这么做也存在一个弊端，所有引用这个资源的地方都需要更新链接。web开发者们通常会采用自动化构建工具在实际工作中完成这些琐碎的工作。当低频更新的资源（js/css）变动了，只用在高频变动的资源文件（html）里做入口的改动。

## 四、设置规则

1. 为基本不会变动的资源设置非常长的过期时间，例如Image、Fonts、jQuery库文件等，以年为单位，例如淘宝网的logo图片设置了一年的max-age值

2. 为定时变动的资源设置中等过期时间，例如CSS文件、JS文件，以版本更替的时间间隔为参考进行设置，如果在过期时间之前需要发新版本，为避免用户仍访问本地副本（因为本地副本还未过期），可使用加版本号的做法

3. HTML文档不使用缓存或谨慎设定过期时间，因为其他资源（图片、CSS、JavaScript 等）的请求都来自一个单一的HTML 文档，如果被缓存了，那么页面中包含的资源的文件名等信息都会一并被缓存，导致对它的更新难以确保立即对用户生效。（TODO）

一个网站，说白了就是HTML+JS+CSS+fonts+img 这几类文件，我们可以针对这几类文件做一些缓存层级

| 文件      | 缓存层级                                |
| --------- | --------------------------------------- |
| HTML      | Cache-Control: no-cache,must-revalidate |
| JS        | Cache-Control:private,max-age=86400     |
| CSS       | Cache-Control:max-age=2629000           |
| img\fonts | Cache-Control:max-age=2629000           |

上面只是一个简单的设置，要知道HTML是一定不能缓存的(大部分网页)。 缓存设置时间应该在你版本稳定之后设置，否则会得不偿失。 另外设置Cache-Control还可以配合ETag或者Last-Modified进行补偿验证，如果后面文件变化也可以及时反映出来。

## 五、用户刷新/访问行为

我们可以把刷新/访问界面的手段分成以下几类：

- **在URI输入栏中输入然后回车 / 通过书签访问 / 通过浏览器前进后退按钮 / 通过页面中的a标签链接进行访问**

  返回`200 OK (from cache)`，浏览器发现该资源已经缓存了而且没有过期（通过Expires头部或者Cache-Control头部），没有跟服务器确认，而是直接使用了浏览器缓存的内容。

- **F5 / 点击工具栏中的刷新按钮 / 右键菜单重新加载**

  F5会让浏览器**无论如何都发一个HTTP Request给Server**，即使先前的响应中有Expires头部。并且浏览器会在Request header中添加这样的字段:

  ```
  Cache-Control: max-age=0
  If-Modified-Since: Fri, 15 Jul 2016 04:11:51 GMT
  ```

  其中Cache-Control是Chrome强制加上的，而If-Modified-Since是因为获取该资源的时候包含了Last-Modified头部，浏览器会使用If-Modified-Since头部信息重新发送该时间以确认资源是否需要重新发送。 实际上Server没有修改这个文件，所以返回了一个`304(Not Modified)`

- **Ctl+F5**

  Ctrl+F5要的是**彻底的从Server拿一份新的资源过来**，所以不光要发送HTTP request给Server，而且这个请求里面连If-Modified-Since/If-None-Match都没有，这样就逼着Server不能返回304，而是把整个资源原原本本地返回一份

  实际上，为了保证拿到的是从Server上最新的，Ctrl+F5不只是去掉了If-Modified-Since/If-None-Match，还需要添加一些HTTP Headers。按照HTTP/1.1协议，Cache不光只是存在Browser终端，从Browser到Server之间的中间节点(比如Proxy)也可能扮演Cache的作用，为了防止获得的只是这些中间节点的Cache，需要告诉他们，别用自己的Cache敷衍我，往Upstream的节点要一个最新的copy吧。
  在Chrome 51 中会包含两个头部信息， 作用就是让中间的Cache对这个请求失效，这样返回的绝对是新鲜的资源。

  ```
  Cache-Control: no-cache
  Pragma: no-cache
  ```

  参考自[HTTP缓存控制小结](http://imweb.io/topic/5795dcb6fb312541492eda8c)




## 参考

1. [【腾讯Bugly干货分享】彻底弄懂 Http 缓存机制 - 基于缓存策略三要素分解法](https://zhuanlan.zhihu.com/p/24467558)
2. [rfc2616 14.Header Field Definitions](https://www.w3.org/Protocols/rfc2616/rfc2616-sec14.html)
3. [浅谈浏览器http的缓存机制-Vajoy](http://www.cnblogs.com/vajoy/p/5341664.html)
4. [MDN | HTTP缓存](https://developer.mozilla.org/zh-CN/docs/Web/HTTP/Caching_FAQ)
5. [MDN | HTTP headers](https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers)
6. [Google Developer HTTP缓存](https://developers.google.com/web/fundamentals/performance/optimizing-content-efficiency/http-caching?hl=zh-cn)
7. [网站优化：浏览器缓存控制简介及配置策略](https://www.renfei.org/blog/http-caching.html)
8. [HTTP缓存控制小结--总结了刷新页面的不同方式的影响](http://imweb.io/topic/5795dcb6fb312541492eda8c)
9. [浏览器的缓存](https://segmentfault.com/a/1190000004486640)
10. [w3.org | HTML4 | META and HTTP headers](https://www.w3.org/TR/html4/struct/global.html#h-7.4.4)
11. [w3.org | HTML5 | META Pragma directives](https://dev.w3.org/html5/spec-preview/the-meta-element.html#pragma-directives)
12. [HTML meta tags vs HTTP response headers](https://stackoverflow.com/questions/49547/how-to-control-web-page-caching-across-all-browsers)
13. [HTTP缓存控制小结](http://imweb.io/topic/5795dcb6fb312541492eda8c)




**技术博客为了总结整理知识和帮助别人**




