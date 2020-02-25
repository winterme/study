# swagger-ui只显示@ApiOperation注解的方法接口

    .apis(RequestHandlerSelectors.withMethodAnnotation(ApiOperation.class))


    package com.zzq.config.swagger;

    import io.swagger.annotations.ApiOperation;
    import org.springframework.context.annotation.Bean;
    import org.springframework.context.annotation.Configuration;
    import springfox.documentation.builders.ApiInfoBuilder;
    import springfox.documentation.builders.PathSelectors;
    import springfox.documentation.builders.RequestHandlerSelectors;
    import springfox.documentation.service.ApiInfo;
    import springfox.documentation.spi.DocumentationType;
    import springfox.documentation.spring.web.plugins.Docket;
    import springfox.documentation.swagger2.annotations.EnableSwagger2;

    /**
    * @author maxwell
    * @Title: zhangzq
    * @ProjectName quartz-control
    * @Description: swagger2-ui 配置
    * @date 2019/7/23 11:06
    * @email: bestzijia@gmail.com
    * @github: https://github.com/winterme/
    * @csdn: https://blog.csdn.net/yali_aini
    */
    @Configuration
    @EnableSwagger2
    public class SwaggerConfig {

        @Bean
        public Docket createRestApi() {
            return new Docket(DocumentationType.SWAGGER_2)
                    .apiInfo(apiInfo())
                    .select()
                    .apis(RequestHandlerSelectors.withMethodAnnotation(ApiOperation.class))
                    .apis(RequestHandlerSelectors.basePackage("com.zzq"))
                    .paths(PathSelectors.any())
                    .build();
        }

        private ApiInfo apiInfo() {
            return new ApiInfoBuilder()
                    .title("quartz 定时任务控制接口")
                    .description("包含查询所有任务，启动，关闭，动态改变执行时间")
                    .termsOfServiceUrl("http://blog.csdn.net/yali_aini")
                    .version("1.0")
                    .build();
        }

    }
