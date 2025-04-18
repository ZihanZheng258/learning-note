1. Host: www.A.com
有了 Host 字段，就可以将请求发往「同一台」服务器上的不同网站。

2. Content-Length: 1000
服务器在返回数据时，会有 Content-Length 字段，表明本次回应的数据长度。
HTTP 协议通过设置回车符、换行符作为 HTTP header 的边界，通过 Content-Length 字段作为 HTTP body 的边界，这两个方式都是为了解决“粘包”的问题

3. Connection 字段最常用于客户端要求服务器使用「HTTP 长连接」机制，以便其他请求复用。
HTTP 长连接的特点是，只要任意一端没有明确提出断开连接，则保持 TCP 连接状态。

4. Content-Type
Content-Type 字段用于服务器回应时，告诉客户端，本次数据是什么格式

5. Content-Encoding 字段
Content-Encoding 字段说明数据的压缩方法。表示服务器返回的数据使用了什么压缩格式
Content-Encoding: gzip