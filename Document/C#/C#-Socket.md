

# Shutdown
传入**SocketShutdown**参数。用于禁用，在任何关闭Socket链接之前都应该先调用Shutdown方法。
Send:禁用上传，但是任然会向底层传入一个零字节的数据包，当服务器接受到的时候，将会知道客户端不会在发送任何数据了
Receive：禁用接受数据。

# Disconnect
关闭Socket链接，造成阻塞，但是会等待两件事情完成：  
1. 所有已经到底层的数据将会被发送完
2. 另一端确认零字节数据包（如果底层协议适用）

# Close
关闭Socket链接，释放系统资源，可能会停止发送到达底层的数据

# blocking 属性
阻塞，还未测试具体使用方式

# NoDelay  属性
该值指定流Socket是否正在使用Nagle算法。此属性在UDP模式下不起作用。主要作用就是通过合并数据从而减少TCP发送小数据包的数量，从而减少TCP标头所产生的额外的网络流量开销

# Available 属性
非阻塞模式下，当数据缓冲区没有等待的数据的时候返回0，当数据缓冲区有等待的数据的时候会返回等待的数据长度。当链接已经断开的时候会触发 SocketException


# IOCP  没有了解过-------很牛逼 