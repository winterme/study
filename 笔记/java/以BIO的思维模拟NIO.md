# 以BIO的思维模拟NIO

此处使用NIO的ServerSocketChanle



1. 使用一个LIst来装所有的请求(clinet)
2. 所有的请求来了，先设置非阻塞，然后放入list中
3. 遍历list，拿出里面所有的 client，进行读取数据，如果没有读到就不做处理，如果报错了则证明client端已经关闭



代码如下：这就是个非阻塞的IO

```java
package bio;

import lombok.extern.slf4j.Slf4j;

import java.io.IOException;
import java.net.InetSocketAddress;
import java.net.SocketAddress;
import java.nio.ByteBuffer;
import java.nio.channels.ServerSocketChannel;
import java.nio.channels.SocketChannel;
import java.util.ArrayList;
import java.util.Date;
import java.util.Iterator;
import java.util.List;

@Slf4j
public class NIOServer3 {

    // 两个常量
    private static final String CRLF = "\r\n";
    private static final String BLANK = " ";

    // 装所有的请求
    private List<SocketChannel> selects;

    // 连接信息，ip，port
    private SocketAddress socketAddress;

    // 连接对象
    private ServerSocketChannel serverSocketChannel;

    public NIOServer3(SocketAddress socketAddress) {
        this.selects = new ArrayList<>();
        this.socketAddress = socketAddress;

        try {
            // 获取实例
            serverSocketChannel = ServerSocketChannel.open();
            // 绑定
            serverSocketChannel.bind(this.socketAddress);
            // 设置非阻塞
            serverSocketChannel.configureBlocking(false);

            log.info("server start...");
        } catch (IOException e) {
            e.printStackTrace();
        }
    }

    public void run() {
        while (true) {
            try {
                // 获取请求，阻塞
                SocketChannel accept = serverSocketChannel.accept();

                Iterator<SocketChannel> iterator = this.selects.iterator();
                // 遍历所有client
                while (iterator.hasNext()) {
                    SocketChannel client = iterator.next();
//                    iterator.remove();
                    ByteBuffer byteBuffer = ByteBuffer.allocate(2048);

                    int len = -1;
                    try {
                        len = client.read(byteBuffer);
                    }catch (IOException e){
                        iterator.remove();
                        continue;
                    }
                    // 复位
                    byteBuffer.flip();
                    if (len > 0) {
                        log.info("读取到消息：【"+ client.getRemoteAddress() +"】" + new String(byteBuffer.array(), "UTF-8"));
                        StringBuffer sb = new StringBuffer();
                        // http 协议版本，状态码，描述
                        sb.append("HTTP/1.1").append(BLANK).append(200).append(BLANK).append("love").append(CRLF);
                        // 响应头
                        sb.append("Server:Pkusoft Server/12.19.06.14").append(CRLF).append("Date:").append(new Date()).append(CRLF);
                        sb.append("Content-type:text/html").append(CRLF);
                        String html = "<meta charset='utf-8' /><h1>nio 牛批</h1>";
                        sb.append("Connection:keep-alive").append(CRLF);
                        // 正文长度：内容
                        sb.append("Content-Length:").append(html.getBytes().length).append(CRLF).append(CRLF);
                        sb.append(html);
                        client.write(ByteBuffer.wrap(sb.toString().getBytes("UTF-8")));
                    } else {
//                        log.info("数据无效！");
                    }
                }

                if (accept == null) {
                    // 请求不合法
                    //log.info("请求不合法");
                } else {
                    log.info("有连接了=>" + accept.getRemoteAddress());
                    // 设置非阻塞
                    accept.configureBlocking(false);
                    // 添加到clients 里面去
                    selects.add(accept);
                    log.info("设置为了非阻塞，放入selects里面了=>" + accept.getRemoteAddress());
                }
            } catch (IOException e) {
                e.printStackTrace();
            }
        }
    }

    public static void main(String[] args) {
        new NIOServer3(new InetSocketAddress(8080)).run();
    }

}
```