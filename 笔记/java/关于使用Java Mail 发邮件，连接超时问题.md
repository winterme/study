# 关于使用Java Mail 发邮件，连接超时问题

## 修改端口为465

    spring.mail.host=smtp.qq.com
    spring.mail.username=1006059906@qq.com
    spring.mail.password=这个是你的授权码
    spring.mail.properties.mail.smtp.auth=true
    spring.mail.properties.mail.smtp.starttls.enable=true
    spring.mail.properties.mail.smtp.starttls.required=true
    spring.mail.default-encoding=UTF-8
    spring.mail.port=465
    spring.mail.properties.mail.smtp.socketFactory.port = 465
    spring.mail.properties.mail.smtp.socketFactory.class = javax.net.ssl.SSLSocketFactory
    spring.mail.properties.mail.smtp.socketFactory.fallback = false

