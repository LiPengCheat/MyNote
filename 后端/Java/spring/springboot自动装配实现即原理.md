# springboot自动装配实现即原理

## 实现

xml文件参考

ps:去掉spring-boot-maven-plugin依赖，防止maven打包失败。

```xml
<properties>
        <java.version>1.8</java.version>
    </properties>

    <dependencies>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
            <exclusions><!-- 去掉springboot默认配置 -->
                <exclusion>
                    <groupId>org.springframework.boot</groupId>
                    <artifactId>spring-boot-starter-logging</artifactId>
                </exclusion>
            </exclusions>
        </dependency>

        <dependency> <!-- 引入log4j2依赖 -->
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-log4j2</artifactId>
        </dependency>

        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-aop</artifactId>
        </dependency>

        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-configuration-processor</artifactId>
            <version>2.2.7.RELEASE</version>
            <optional>true</optional>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-test</artifactId>
            <scope>test</scope>
            <exclusions>
                <exclusion>
                    <groupId>org.junit.vintage</groupId>
                    <artifactId>junit-vintage-engine</artifactId>
                </exclusion>
            </exclusions>
        </dependency>
    </dependencies>

    <build>
        <plugins>

            <!-- 跳过代码安全检查-->
            <plugin>
                <groupId>org.apache.maven.plugins</groupId>
                <artifactId>maven-surefire-plugin</artifactId>
                <configuration>
                    <skipTests>true</skipTests>
                </configuration>
            </plugin>
```

需要实现自动装配的类加上<font color=red>@Configuration</font>注解,示例：

```java
@Configuration
public class CustomAutoConfiguration {
    private static final Logger logger = LoggerFactory.getLogger(CustomAutoConfiguration.class);

    @Bean
    @ConditionalOnMissingBean	// 此注解表示容器中没有这个注解就注入进去
    public WebLogAspect getWebLogAspect() {
        return new WebLogAspect();
    }

    @Bean
    @ConditionalOnMissingBean
    public AutoUtil getAutoUtil() {
        return new AutoUtil();
    }

    @Bean
    @ConditionalOnProperty(prefix = "global.exception.resolver", name = "enable", havingValue = "true") // 根据条件注入到容器中  prefix前缀名，name属性名，havingValue将配置文件中的属性值跟这个相比较如果一样则返回true;否则返回false。
    public GlobalExceptionResolver getGlobalExceptionResolver() {
        logger.info("GlobalExceptionResolver注入");
        return new GlobalExceptionResolver();
    }
}
```

最后在resources文件夹下建立文件夹 META-INF 并添加文件 spring.factories

文件内容如下：

```properties
org.springframework.boot.autoconfigure.EnableAutoConfiguration = \
  io.gitee.zhuanyanchenghuiyi.spirng_auto_configuration.config.CustomAutoConfiguration,\
  io.gitee.zhuanyanchenghuiyi.spirng_auto_configuration.config.GlobalExceptionConfig
```

文件中的key默认使用这个，当值为多个的时候使用,好分割，\表示换行符