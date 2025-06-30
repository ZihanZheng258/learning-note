前面我们花费大量篇幅讨论了 REST 的思想、概念和指导原则等理论方面的内容，在这个小节里，我们将把重心放在实践上，把目光从整个软件架构设计进一步聚焦到 REST 接口设计上，以切合本节的题目“REST 设计风格”，也顺带填了前面埋下的“如何评价服务是否 RESTful”的坑。

《[RESTful Web APIs](https://book.douban.com/subject/22139962/)》和《[RESTful Web Services](https://book.douban.com/subject/2054201/)》的作者 Leonard Richardson 曾提出过一个衡量“服务有多么 REST”的 Richardson 成熟度模型（[Richardson Maturity Model](https://martinfowler.com/articles/richardsonMaturityModel.html)），便于那些原本不使用 REST 的系统，能够逐步地导入 REST。Richardson 将服务接口“REST 的程度”从低到高，分为 0 至 3 级：

0. The Swamp of [Plain Old XML](https://en.wikipedia.org/wiki/Plain_Old_XML)：完全不 REST。另外，关于 Plain Old XML 这说法，SOAP 表示[感觉有被冒犯到](https://baike.baidu.com/item/%E6%84%9F%E8%A7%89%E6%9C%89%E8%A2%AB%E5%86%92%E7%8A%AF%E5%88%B0)。
1. Resources：开始引入资源的概念。
2. ==HTTP Verbs：引入统一接口，映射到 HTTP 协议的方法上。==
3. Hypermedia Controls：超媒体控制在本文里面的说法是“超文本驱动”，在 Fielding 论文里的说法是==“Hypertext As The Engine Of Application State，HATEOAS”，其实都是指同一件事情==。

笔者借用 Martin Fowler 撰写的关于 RMM 成熟度模型的[文章](https://martinfowler.com/articles/richardsonMaturityModel.html)中的实际例子（==原文是 XML 写的，这里简化为 JSON 表示）==，来具体展示一下四种不同程度的 REST 反应到实际接口中会是怎样的。假设你是一名软件工程师，接到需求（原文中的需求复杂一些，这里简化了）的 UserStory 描述是这样的：

医生预约系统

作为一名病人，我想要从系统中得知指定日期内我熟悉的医生是否具有空闲时间，以便于我向该医生预约就诊。

### 第 0 级
医院开放了一个`/appointmentService`的 Web API，传入日期、医生姓名作为参数，可以得到该时间段该名医生的空闲时间，该 API 的一次 HTTP 调用如下所示：

```
POST /appointmentService?action=query HTTP/1.1

{date: "2020-03-04", doctor: "mjones"}
```

```
HTTP/1.1 200 OK

[
	{start:"14:00", end: "14:50", doctor: "mjones"},
	{start:"16:00", end: "16:50", doctor: "mjones"}
]
```

得到了医生空闲的结果后，我觉得 14:00 的时间比较合适，于是进行预约确认，并提交了我的基本信息：

```
POST /appointmentService?action=confirm HTTP/1.1

{
	appointment: {date: "2020-03-04", start:"14:00", doctor: "mjones"},
	patient: {name: icyfenix, age: 30, ……}
}
```

如果预约成功，那我能够收到一个预约成功的响应：

```
HTTP/1.1 200 OK

{
	code: 0,
	message: "Successful confirmation of appointment"
}
```

如果发生了问题，譬如有人在我前面抢先预约了，那么我会在响应中收到某种错误信息：

```
HTTP/1.1 200 OK

{
	code: 1
	message: "doctor not available"
}
```

到此，整个预约服务宣告完成，直接明了，我们采用的是非常直观的基于 RPC 风格的服务设计似乎很容易就解决了所有问题……了吗？

### 第 1 级

第 0 级是 RPC 的风格，==如果需求永远不会变化，也不会增加，那它完全可以良好地工作下去==。但是，如果你不想为预约医生之外的其他操作、为获取空闲时间之外的其他信息去编写额外的方法，或者改动现有方法的接口，那还是应该考虑一下如何使用 REST 来抽象资源。

==通往 REST 的第一步是引入资源的概念，在 API 中基本的体现是围绕着资源而不是过程来设计服务==，说的直白一点，可以理解为服务的 Endpoint 应该是一个名词而不是动词。==此外，每次请求中都应包含资源的 ID，所有操作均通过资源 ID 来进行，譬如，获取医生指定时间的空闲档期：==

```
POST /doctors/mjones HTTP/1.1

{date: "2020-03-04"}
```

然后服务器传回一组包含了 ID 信息的档期清单==，注意，ID 是资源的唯一编号，有 ID 即代表“医生的档期”被视为一种资源：==

```
HTTP/1.1 200 OK

[
	{id: 1234, start:"14:00", end: "14:50", doctor: "mjones"},
	{id: 5678, start:"16:00", end: "16:50", doctor: "mjones"}
]
```

我还是觉得 14:00 的时间比较合适，于是又进行预约确认，并提交了我的基本信息：

```
POST /schedules/1234 HTTP/1.1

{name: icyfenix, age: 30, ……}
```

==后面预约成功或者失败的响应消息在这个级别里面与之前一致，就不重复了==。比起第 0 级，第 1 级的特征是引入了资源，通过资源 ID 作为主要线索与服务交互，但第 1 级至少还有三个问题并没有解决：==一是只处理了查询和预约，如果我临时想换个时间，要调整预约，或者我的病忽然好了，想删除预约，这都需要提供新的服务接口。==二是处理结果响应时，==只能靠着结果中的`code`、`message`这些字段做分支判断==，每一套服务都要设计可能发生错误的 code，这很难考虑全面，而且也不利于对某些通用的错误做统一处理；==三是并没有考虑认证授权等安全方面的内容，譬如要求只有登陆用户才允许查询医生档期时间，某些医生可能只对 VIP 开放，需要特定级别的病人才能预约==，等等。


### 第 2 级

第 1 级遗留三个问题都可以靠引入统一接口来解决。HTTP 协议的七个标准方法是经过精心设计的，==只要架构师的抽象能力够用，它们几乎能涵盖资源可能遇到的所有操作场景。==REST 的做法是把不同业务需求抽象为对资源的增加、修改、删除等操作来解决第一个问题；使用 HTTP 协议的 Status Code，可以涵盖大多数资源操作可能出现的异常，==而且 Status Code 可以自定义扩展，以此解决第二个问题==；依靠 HTTP Header 中携带的额外认证、授权信息来解决第三个问题，这个在实战中并没有体现，请参考安全架构中的“[凭证](https://icyfenix.cn/architect-perspective/general-architecture/system-security/credentials)”相关内容。

按这个思路，获取医生档期，应采用具有查询语义的 GET 操作进行：

```
GET /doctors/mjones/schedule?date=2020-03-04&status=open HTTP/1.1
```

然后服务器会传回一个包含了所需信息的回应：

```
HTTP/1.1 200 OK

[
	{id: 1234, start:"14:00", end: "14:50", doctor: "mjones"},
	{id: 5678, start:"16:00", end: "16:50", doctor: "mjones"}
]
```

我仍然觉得 14:00 的时间比较合适，==于是又进行预约确认，并提交了我的基本信息，用以创建预约，这是符合 POST 的语义的：==

```
POST /schedules/1234 HTTP/1.1

{name: icyfenix, age: 30, ……}
```
如果预约成功，那我能够收到一个预约成功的响应：
```
HTTP/1.1 201 Created

Successful confirmation of appointment
```
如果发生了问题，譬如有人在我前面抢先预约了，那么我会在响应中收到某种错误信息：
```
HTTP/1.1 409 Conflict

doctor not available
```

### 第 3 级

第 2 级是目前绝大多数系统所到达的 REST 级别，但仍不是完美的，至少还存在一个问题：==你是如何知道预约 mjones 医生的档期是需要访问`/schedules/1234`这个服务 Endpoint 的？==也许你甚至第一时间无法理解为何我会有这样的疑问，这当然是程序代码写的呀！但 REST 并不认同这种已烙在程序员脑海中许久的想法。RMM 中的 Hypermedia Controls、Fielding 论文中的 HATEOAS 和现在提的比较多的“超文本驱动”，==所希望的是除了第一个请求是由你在浏览器地址栏输入所驱动之外，其他的请求都应该能够自己描述清楚后续可能发生的状态转移==，由超文本自身来驱动。所以，当你输入了查询的指令之后：

```
GET /doctors/mjones/schedule?date=2020-03-04&status=open HTTP/1.1
```

```
HTTP/1.1 200 OK

{
	schedules：[
		{
			id: 1234, start:"14:00", end: "14:50", doctor: "mjones",
			links: [
				{rel: "comfirm schedule", href: "/schedules/1234"}
			]
		},
		{
			id: 5678, start:"16:00", end: "16:50", doctor: "mjones",
			links: [
				{rel: "comfirm schedule", href: "/schedules/5678"}
			]
		}
	],
	links: [
		{rel: "doctor info", href: "/doctors/mjones/info"}
	]
}
```

如果做到了第 3 级 REST，那服务端的 API 和客户端也是完全解耦的，你要调整服务数量，或者同一个服务做 API 升级将会变得非常简单。

