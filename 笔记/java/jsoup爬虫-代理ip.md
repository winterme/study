# JSOUP 代理ip 爬虫

代理ip地址： https://www.xicidaili.com/wt/



精简代码：

```
public static void main(String[] args) throws IOException {
    String url = "http://103.118.42.35:8091/getRequestInfo";
    String ip = "183.166.253.96";
    int port = 4216;


    Connection connect = Jsoup.connect(url);
    connect.timeout(10*1000);
    connect.userAgent("xxxxxxxxxxxxxxxxxxxxxxxxx");
    connect.ignoreContentType(true);
    connect.proxy(ip, port);

    System.out.println(connect.execute().body());

}
```



```
package com;

import org.jsoup.Jsoup;
import org.jsoup.nodes.Document;
import org.slf4j.Logger;
import org.slf4j.LoggerFactory;

import java.io.IOException;
import java.util.HashMap;
import java.util.Map;
import java.util.Random;

public class ProxyIP {

    private static Logger logger = LoggerFactory.getLogger(ProxyIP.class);

    private static String url;

    private static String reference;

    private static String ip;

    private static int port;

    private static String[] ua = {"Mozilla/5.0 (Windows NT 6.1; WOW64; rv:46.0) Gecko/20100101 Firefox/46.0",
            "Mozilla/5.0 (Windows NT 6.1; WOW64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/50.0.2661.87 Safari/537.36 OPR/37.0.2178.32",
            "Mozilla/5.0 (Windows NT 6.1; WOW64) AppleWebKit/534.57.2 (KHTML, like Gecko) Version/5.1.7 Safari/534.57.2",
            "Mozilla/5.0 (Windows NT 6.1; WOW64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/45.0.2454.101 Safari/537.36",
            "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/46.0.2486.0 Safari/537.36 Edge/13.10586",
            "Mozilla/5.0 (Windows NT 10.0; WOW64; Trident/7.0; rv:11.0) like Gecko",
            "Mozilla/5.0 (compatible; MSIE 10.0; Windows NT 6.1; WOW64; Trident/6.0)",
            "Mozilla/5.0 (compatible; MSIE 9.0; Windows NT 6.1; WOW64; Trident/5.0)",
            "Mozilla/4.0 (compatible; MSIE 8.0; Windows NT 6.1; WOW64; Trident/4.0)",
            "Mozilla/5.0 (Windows NT 6.1; WOW64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/47.0.2526.106 BIDUBrowser/8.3 Safari/537.36",
            "Mozilla/5.0 (Windows NT 6.1; WOW64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/47.0.2526.80 Safari/537.36 Core/1.47.277.400 QQBrowser/9.4.7658.400",
            "Mozilla/5.0 (Windows NT 6.1; WOW64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/48.0.2564.116 UBrowser/5.6.12150.8 Safari/537.36",
            "Mozilla/5.0 (Windows NT 6.1; WOW64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/38.0.2125.122 Safari/537.36 SE 2.X MetaSr 1.0",
            "Mozilla/5.0 (Windows NT 6.1; WOW64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/48.0.2564.116 Safari/537.36 TheWorld 7",
            "Mozilla/5.0 (Windows NT 6.1; W…) Gecko/20100101 Firefox/60.0"};

    public static void main(String[] args) {
        ip = "183.166.253.96";
        port = 4216;

        url = "https://www.mzitu.com/54625/2";
        reference = "https://www.mzitu.com/54625/2";

        new ProxyIP().run();

        url = "http://103.118.42.35:8080/lcm";
        reference = "http://103.118.42.35:8080/lcm/xx";

        new ProxyIP().run();
    }

    public void run() {
        Random r = new Random();
        int i = r.nextInt(14);
        logger.info("检测中------ {}:{}", ip, port);

        long t = System.currentTimeMillis();
        Map<String, String> param = new HashMap<>();
        //爬取的目标网站，url记得换下。。。！！！
        Document doc = null;
        try {
            doc = Jsoup.connect(url)
                    .timeout(20 * 1000)
                    .proxy(ip, port)
                    .data(param)
//                    .ignoreContentType(true)
                    .header("Content-Type","text/html;charset=utf-8")
                    .userAgent(ua[i])
                    .header("referer", reference)//这个来源记得换..
                    .get();
        } catch (IOException e) {
            logger.error(e.getMessage(), e);
        }
        if (doc != null) {
            System.out.println(ip + ":" + port + "访问时间:" + (System.currentTimeMillis() - t) + "   访问结果: " + doc.text());
        }

    }

}
```