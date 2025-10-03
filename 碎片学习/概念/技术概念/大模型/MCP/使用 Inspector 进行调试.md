Claude 原生提供的 MCP 协议可以通过官方提供的 Inspector 进行调试，对于 [你的第一个 MCP](https://openmcp.kirigaya.cn/zh/plugin-tutorial/quick-start/first-mcp.html) 中的例子，==可以如下进行调试，在命令行输入如下命令启动 Inspector:==

```
mcp dev main.py
```

这会启动一个前端服务器，并打开 `http://localhost:5173/` 后我们可以看到 inspector 的调试界面，先点击左侧的 `Connect` 来运行我们的 server.py 并通过 stdio 为通信管道和 web 建立通信。

Fine，可以开始愉快地进行调试了，Inspector 主要给了我们三个板块，分别对应 Resources，Prompts 和 Tools。

先来看 Resources，点击「Resource Templates」可以罗列所有注册的 Resource，比如我们上文定义的 `get_greeting`，你可以通过输入参数运行来查看这个函数是否正常工作。（因为一般情况下的这个资源协议是会访问远程数据库或者微服务的）

![[Pasted image 20250912171043.png]]Prompts 端就比较简单了，直接输入预定义参数就能获取正常的返回结果。

![[Pasted image 20250912171055.png]]

Tools 端将会是我们后面调试的核心。在之前的章节我们讲过了，MCP 协议中的 Prompts 和 Resources 目前还没有被 openai 协议和各大 MCP 客户端广泛支持，因此，我们主要的服务端业务都应该是在写 tools。

我们此处提供的 tool 是实现一个简单的加法，它非常简单，我们输入 1 和 2 就可以直接看到结果是 3。我们后续会开发一个可以访问天气预报的 tool，那么到时候就非常需要一个这样的窗口来调试我们的天气信息获取是否正常了。

![[Pasted image 20250912171227.png]]

这篇文章，我们简单了解了 MCP 内部的一些基本概念，我认为这些概念对于诸位开发一个 MCP 服务器是大有裨益的，所以我认为有必要先讲一讲。

下面的文章中，我将带领大家探索 MCP 的奇境，一个属于 AI Agent 的时代快要到来了。