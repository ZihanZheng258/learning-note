==[SIP](https://zhida.zhihu.com/search?content_id=185148070&content_type=Article&match_order=1&q=SIP&zhida_source=entity)（Session Initiation Protocol），会话发起协议，顾名思义，就是用于发起会话的协议。==

SIP会话（Session）是两个用户终端之间（也可能是多个用户之间）基于IP网络的会话，即VoIP会话；会话的媒体一般是语音、视频、也可能有其他形式。

SIP协议本身只负责会话的建立，具体来说，==就是在两个用户终端之间通过SIP消息交换传递消息，完成会话的建立；==

会话建立过程中的一个关键点是交换发送/接受会话媒体的地址及相关参数，即媒体描述；媒体描述是通过[SDP](https://zhida.zhihu.com/search?content_id=185148070&content_type=Article&match_order=1&q=SDP&zhida_source=entity)来完成的；会话建立以后，两方得到了彼此的媒体地址之后，就可以向对方发送媒体，开始通话。媒体传输是通过[RTP](https://zhida.zhihu.com/search?content_id=185148070&content_type=Article&match_order=1&q=RTP&zhida_source=entity)（实时传输协议）完成的。

SDP、RTP，与SIP协议是相对独立的。

## 会话建立与管理

### 发起会话

一个创建会话的简单的例子如下图所示：

![[Pasted image 20250712170857.png]]

1. 主叫方向被叫方发送SIP INVITE 请求消息，请求建立会话，INVITE消息其中包含提议的会话参数的描述
2. 被叫方返回SIP 200 OK响应消息，其中包含接受的会话参数的描述，接受会话建立请求
3. 主叫方发送SIP ACK 请求消息确认会话的建立。

![[Pasted image 20250712170930.png]]

1. 会话中的任意一方可以发送SIP reINVITE 请求消息，其中包含提议的新的会话参数，请求修改二者之间的会话；
2. 另一方返回一个SIP 200 OK 响应消息，其中包含接受的新的会话参数，接受会话修改请求

一个结束会话的简单的例子如下图所示：
![[Pasted image 20250712170959.png]]