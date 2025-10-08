## 前言

在 [之前的文章](https://openmcp.kirigaya.cn/zh/plugin-tutorial/what-is-mcp.html) 中，我们简单介绍了 MCP 的定义和它的基本组织结构。==作为开发者，我们最需要关注的其实是如何根据我们自己的业务和场景定制化地开发我们需要的 MCP 服务器==，这样直接接入任何一个 MCP 客户端后，我们都可以给大模型以我们定制出的交互能力。

在正式开始教大家如何开发自己的 MCP 服务器之前，我想，或许有必要讲清楚几个基本概念。

## Resources, Prompts 和 Tools

在 [MCP 客户端协议](https://modelcontextprotocol.io/clients) 中，讲到了 MCP 协议中三个非常重要的能力类别：

- Resouces ：定制化地请求和访问本地的资源，可以是文件系统、数据库、当前代码编辑器中的文件等等原本网页端的app 无法访问到的 **静态资源**。额外的 resources 会丰富发送给大模型的上下文，使得 AI 给我们更加精准的回答。

- Prompts ：定制化一些场景下可供 AI 进行采纳的 prompt，比如如果需要 AI 定制化地返回某些格式化内容时，可以提供自定义的 prompts

- Tools ：可供 AI 使用的工具，它必须是一个函数，==比如预定酒店、打开网页、关闭台灯这些封装好的函数就可以是一个 tool，大模型会通过 function calling 的方式来使用这些 tools==。 Tools 将会允许 AI 直接操作我们的电脑，甚至和现实世界发生交互。

各位拥有前后端开发经验的朋友们，可以将 Resouces 看成是「额外给予大模型的只读权限」，把 Tools 看成是「额外给予大模型的读写权限」。

MCP 客户端（比如 Claude Desktop，5ire 等）已经实现好了上述的前端部分逻辑。而具体提供什么资源，具体提供什么工具，则需要各位玩家充分想象了，==也就是我们需要开发丰富多彩的 MCP Server 来允许大模型做出更多有意思的工作。==

不过需要说明的一点是，目前几乎所有大模型采用了 openai 协议作为我们访问大模型的接入点。什么叫 openai 协议呢？

## openai 协议

当我们使用 python 或者 typescript 开发 app 时，往往会安装一个名为 openai 的库，里面填入你需要使用的模型厂商、模型的基础 url、使用的模型类别来直接访问大模型。而各个大模型提供商也必须支持这个库，这套协议。

比如我们在 python 中访问 deepseek 的服务就可以这么做：

```python
from openai import OpenAI

client = OpenAI(api_key="<DeepSeek API Key>", base_url="https://api.deepseek.com")

response = client.chat.completions.create(
    model="deepseek-chat",
    messages=[
        {"role": "system", "content": "You are a helpful assistant"},
        {"role": "user", "content": "Hello"},
    ],
    stream=False
)

print(response.choices[0].message.content)
```

如果你点进这个 create 函数去看，你会发现 openai 协议需要大模型厂家支持的 feature 是非常非常多的：

```python
    @overload
    def create(
        self,
        *,
        messages: Iterable[ChatCompletionMessageParam],
        model: Union[str, ChatModel],
        audio: Optional[ChatCompletionAudioParam] | NotGiven = NOT_GIVEN,
        frequency_penalty: Optional[float] | NotGiven = NOT_GIVEN,
        function_call: completion_create_params.FunctionCall | NotGiven = NOT_GIVEN,
        functions: Iterable[completion_create_params.Function] | NotGiven = NOT_GIVEN,
        logit_bias: Optional[Dict[str, int]] | NotGiven = NOT_GIVEN,
        logprobs: Optional[bool] | NotGiven = NOT_GIVEN,
        max_completion_tokens: Optional[int] | NotGiven = NOT_GIVEN,
        max_tokens: Optional[int] | NotGiven = NOT_GIVEN,
        metadata: Optional[Metadata] | NotGiven = NOT_GIVEN,
        modalities: Optional[List[Literal["text", "audio"]]] | NotGiven = NOT_GIVEN,
        n: Optional[int] | NotGiven = NOT_GIVEN,
        parallel_tool_calls: bool | NotGiven = NOT_GIVEN,
        prediction: Optional[ChatCompletionPredictionContentParam] | NotGiven = NOT_GIVEN,
        presence_penalty: Optional[float] | NotGiven = NOT_GIVEN,
        reasoning_effort: Optional[ReasoningEffort] | NotGiven = NOT_GIVEN,
        response_format: completion_create_params.ResponseFormat | NotGiven = NOT_GIVEN,
        seed: Optional[int] | NotGiven = NOT_GIVEN,
        service_tier: Optional[Literal["auto", "default"]] | NotGiven = NOT_GIVEN,
        stop: Union[Optional[str], List[str], None] | NotGiven = NOT_GIVEN,
        store: Optional[bool] | NotGiven = NOT_GIVEN,
        stream: Optional[Literal[False]] | NotGiven = NOT_GIVEN,
        stream_options: Optional[ChatCompletionStreamOptionsParam] | NotGiven = NOT_GIVEN,
        temperature: Optional[float] | NotGiven = NOT_GIVEN,
        tool_choice: ChatCompletionToolChoiceOptionParam | NotGiven = NOT_GIVEN,
        tools: Iterable[ChatCompletionToolParam] | NotGiven = NOT_GIVEN,
        top_logprobs: Optional[int] | NotGiven = NOT_GIVEN,
        top_p: Optional[float] | NotGiven = NOT_GIVEN,
        user: str | NotGiven = NOT_GIVEN,
        web_search_options: completion_create_params.WebSearchOptions | NotGiven = NOT_GIVEN,
        # Use the following arguments if you need to pass additional parameters to the API that aren't available via kwargs.
        # The extra values given here take precedence over values defined on the client or passed to this method.
        extra_headers: Headers | None = None,
        extra_query: Query | None = None,
        extra_body: Body | None = None,
        timeout: float | httpx.Timeout | None | NotGiven = NOT_GIVEN,
    ) -> ChatCompletion:
```

从上面的签名中，你应该可以看到几个很熟悉的参数，==比如 `temperature`, `top_p`，很多的大模型使用软件中，有的会给你暴露这个参数进行调节。==比如在 5ire 中，内容随机度就是 `temperature` 这个参数的图形化显示。

![[Pasted image 20250912170551.png]]

其实如你所见，一次普普通通调用涉及到的可调控参数是非常之多的。而在所有参数中，你可以注意到一个参数叫做 `tools`:

```python
    @overload
    def create(
        self,
        *,
        messages: Iterable[ChatCompletionMessageParam],
        model: Union[str, ChatModel],
        audio: Optional[ChatCompletionAudioParam] | NotGiven = NOT_GIVEN,

		# 看这里
        tools: Iterable[ChatCompletionToolParam] | NotGiven = NOT_GIVEN,
    ) -> ChatCompletion:
```

## tool_calls 字段

在上面的 openai 协议中，==有一个名为 tools 的参数。 tools 就是要求大模型厂商必须支持 function calling 这个特性==，也就是我们提供一部分工具的描述（和 MCP 协议完全兼容的），在 tools 不为空的情况下，chat 函数返回的值中会包含一个特殊的字段 `tool_calls`，我们可以运行下面的我写好的让大模型调用可以查询天气的代码：

```
from openai import OpenAI

client = OpenAI(
	api_key="Deepseek API",
	base_url="https://api.deepseek.com"
)

# 定义 tools（函数/工具列表）
tools = [
    {
        "type": "function",
        "function": {
            "name": "get_current_weather",
            "description": "获取给定地点的天气",
            "parameters": {
                "type": "object",
                "properties": {
                    "location": {
                        "type": "string",
                        "description": "城市，比如杭州，北京，上海",
                    }
                },
                "required": ["location"],
            },
        },
    }
]

response = client.chat.completions.create(
    model="deepseek-chat",
    messages=[
        {"role": "system", "content": "你是一个很有用的 AI"},
        {"role": "user", "content": "今天杭州的天气是什么？"},
    ],
    tools=tools,  # 传入 tools 参数
    tool_choice="auto",  # 可选：控制是否强制调用某个工具
    stream=False,
)

print(response.choices[0].message)
```

运行上述代码，它的返回如下：

```python
ChatCompletionMessage(
	content='',
	refusal=None,
	role='assistant',
	annotations=None,
	audio=None,
	function_call=None,
	tool_calls=[
		ChatCompletionMessageToolCall(
			id='call_0_baeaba2b-739d-40c2-aa6c-1e61c6d7e855',
			function=Function(
				arguments='{"location":"杭州"}',
				name='get_current_weather'
			),
			type='function',
			index=0
		)
	]
)
```

可以看到上面的 `tool_calls` 给出了大模型想要如何去使用我们给出的工具。需要说明的一点是，收到上下文的限制，目前一个问题能够让大模型调取的工具上限一般不会超过 100 个，这个和大模型厂商的上下文大小有关系。奥，对了，友情提示，当你使用 MCP 客户端在使用大模型解决问题时，同一时间激活的 MCP Server 越多，消耗的 token 越多哦 😄

而目前 openai 的协议中，tools 是只支持函数类的调用。而函数类的调用往往是可以模拟出 Resources 的效果的。比如取资源，你可以描述为一个 tool。因此在正常情况下，如果大家要开发 MCP Server，最好只开发 Tools，另外两个 feature 还暂时没有得到广泛支持。