# 🍃SpringBootWarFileConfig
## 配置简介
- ⚒依赖配置
- 🍖War包类添加
# ⚒依赖配置
    📁添加:
        <packaging>war</packageing>
        <dependency>
                    <groupId>javax.servlet</groupId>
                    <artifactId>javax.servlet-api</artifactId>
                    <scope>provided</scope>
        </dependency>
    🗑排除:
        <dependency>
                   <groupId>org.springframework.boot</groupId>
                   <artifactId>spring-boot-starter-web</artifactId>
                   <exclusions>
                         <exclusion>
                               <groupId>org.sprinframework.boot</groupId>
                               <artifactId>org-springframework-starter-tomcat</artifactId>
                         </exclusion>
                   </exclusions>
        </dependency>
# 🍖War包类添加
    public class WarStarterApplication extends SpringBootServletInitializer {
                @Override
                protected SpringApplicationBuilder configure(SpringApplicationBuilder builder) {
                    //指向Application启动类
                    return builder.sources(Application.class);
                }
    }