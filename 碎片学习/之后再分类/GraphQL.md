[**GraphQL**](http://apifox.com/apiskills/graphql-tutorials/) 是一种用于 API 的查询语言，也是一个基于服务端的运行引擎。

==GraphQL 提供了一套完整的规范和描述用于查询 API，服务端能够准确地返回给客户端需要的数据且没有任何冗余的数据==。GraphQL 本质是 API 查询语言，用于前后端数据的查询；开发者可以自定义数据模型、查询规范和查询参数，==并在一个请求中就可以获取所有想要的数据==。这不像 RESTful 请求，可能需要请求多次才可以获取需要的数据。所以在 GraphQL 请求中，开发者对于返回的结果是有确定性的。

## GraphQL 规范

### 数据模型

数据模型用于定义==对象实体的数据结构以及对象之间的关系==，比如对象有哪些属性，下图表示一个完整的数据模型，定义了一个查询 Query 和两个返回对象 Header 和 Query，这两个对象分别包含的属性字段和数据类型也分别定义了，这些就会用于实际的 GraphQL 的请求中。

![[Pasted image 20250529180058.png]]


### GraphQL 的优点

- GraphQL 模式会在 ==GraphQL 应用中设置单一事实来源。它为企业提供了一种整合其整个 API 的方法。==
- 一次往返通讯可以处理多个 GraphQL 调用。客户端可得到自己所请求的内容，不会超量。
- ==严格定义的数据类型可减少客户端与服务器之间的通信错误。==
- GraphQL 具有自检功能。客户端可以请求一个可用数据类型的列表。这非常适合文档的自动生成。
- GraphQL 允许应用 API 进行更新优化，而无需破坏现有查询。
- 许多开源 GraphQL 扩展可提供 REST API 所不具备的功能。
- GraphQL 不指定特定的[应用架构](https://www.redhat.com/zh/topics/application-development-and-delivery/shenmeshiyingyongjiagou)。它能够以现有的 REST API 为基础，并与现有的 [API 管理](https://www.redhat.com/zh/topics/integration/shenmeshi-api-guanli)工具配合使用。

### GraphQL 的缺点

- 即便是熟悉 REST API 的开发人员，也需要一定时间才能掌握 GraphQL。
- GraphQL 将数据查询的大部分工作都转移到服务器端，由此增加了服务器开发人员工作的复杂度。
- 根据不同的实施方式，GraphQL 可能需要不同于 REST API 的 API 管理策略，尤其是在考虑速率限制和定价的情况下。
- 缓存机制比 REST 更加复杂。
- API 维护人员还会面临编写可维护 GraphQL 模式的额外任务。