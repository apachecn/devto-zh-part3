# 关闭 Spring Boot 命令行应用程序

> 原文：<https://dev.to/mengjiann/shutdown-spring-boot-command-line-application-9fl>

如果您有一个 SpringBootApplication，它实现了 CommandLineRunner 接口，并希望在运行 override run 方法后终止它，下面是它:

```
@SpringBootApplication
public class DemoApplication implements CommandLineRunner {

  SpringApplication app = new SpringApplicationBuilder()
                .sources(DemoApplication.class)
                .web(false).build();

  springApplication.run(args).close();

  @Overrride
  public void run(String... strings) throws Exception {
    // Code
  }

} 
```