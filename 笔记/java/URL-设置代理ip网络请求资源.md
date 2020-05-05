# URL-设置代理ip网络请求资源







```
package com;


import java.io.IOException;
import java.io.InputStream;
import java.net.URL;
import java.net.URLConnection;
import java.nio.ByteBuffer;
import java.nio.channels.Channels;
import java.nio.channels.ReadableByteChannel;

public class Proxy3 {
    public static void main(String[] args) throws IOException {

        String link = "http://103.118.42.35:8091/getRequestInfo";
        String ip = "183.166.253.96";
        int port = 4216;

        System.getProperties().setProperty("http.proxyHost", ip);
        System.getProperties().setProperty("http.proxyPort", String.valueOf(port));

        URL url = new URL(link);

        // 获取连接
        URLConnection urlConnection = url.openConnection();
        urlConnection.setConnectTimeout(10 * 1000);

        // 设置请求头
        urlConnection.addRequestProperty("Referer", "https://www.mzitu.com/192059/2");
        urlConnection.addRequestProperty("user-agent", "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/76.0.3809.132 Safari/537.36");

        // 获取输入流，网络文件都是以流的形式进行传输的
        InputStream inputStream = urlConnection.getInputStream();

        ReadableByteChannel inChannel = Channels.newChannel(inputStream);

        // 使用 增强读取 ， 可以设置 解码字符集
        // 使用 增强写出
        int capacity = 1024;
        ByteBuffer buffer = ByteBuffer.allocate(capacity);
        int len = -1;
        while ((len = inChannel.read(buffer)) != -1) {
            buffer.flip();

            int length = 0;
            System.out.println(new String(buffer.array()));
            buffer.clear();
        }


    }
}
```