# 🥂sue-project
> ## 🧊`依赖管理`
> #### 🧊`SpringBoot主要依赖`
> #### 🧊`持久层依赖`
> #### 🧊`日志工具依赖`
> #### 🧊`通用工具依赖`
> #### 🧊`消息队列依赖`
> #### 🧊`缓存中间依赖`
> #### 🧊`搜索引擎依赖`
> #### 🧊`文件存储依赖`
> #### 🧊`数据库中间件依赖`
> #### 🧊`分布式锁依赖`
> ## 🧈`微服务依赖`
> #### 🧈`注册中心依赖`
> #### 🧈`配置中心依赖`
> #### 🧈`服务依赖`
> #### 🧈`熔断降级依赖`
> #### 🧈`服务调用依赖`
> #### 🧈`总线依赖`
> #### 🧈`消息驱动依赖`
> #### 🧈`流量限流依赖`
> #### 🧈`服务网关依赖`
> #### 🧈`负载均衡依赖`
> #### 🧈`链路追踪依赖`
# 🧊`依赖管理`
### 🧊SpringBoot主要依赖

    依赖版本管理:
    
        <dependencyManagement>
                <dependencies>
                    <dependency>
                        <groupId>org.springframework.boot</groupId>
                        <artifactId>spring-boot-starter-parent</artifactId>
                        <version>2.1.5.RELEASE</version>
                        <scope>import</scope>
                        <type>pom</type>
                    </dependency>
                </dependencies>
        </dependencyManagement>
        
    具体依赖:
   
        <dependency>
               <groupId>org.springframework.boot</groupId>
               <artifactId>spring-boot-starter-web</artifactId>
        </dependency>
        
        <dependency>
               <groupId>org.springframework.boot</groupId>
               <artifactId>spring-boot-configuration-processor</artifactId>
        </dependency>
    
        <dependency>
               <groupId>org.springframework.boot</groupId>
               <artifactId>spring-boot-starter-aop</artifactId>
        </dependency>
       
        <dependency>
               <groupId>org.springframework.boot</groupId>
               <artifactId>spring-boot-starter-test</artifactId>
               <scope>test</scope>
        </dependency>
        
### 🧊持久层依赖

       mysql数据驱动:
        <dependency>
               <groupId>mysql</groupId>
               <artifactId>mysql-connector-java</artifactId>
               <version>8.0.18</version>
        </dependency>
        
        Mybatis持久层框架:
            <dependency>
                    <groupId>org.mybatis.spring.boot</groupId>
                    <artifactId>mybatis-spring-boot-starter</artifactId>
                    <version>2.1.0</version>
            </dependency>
            
            
            辅助持久层(mybatis):
                <dependency>
                        <groupId>tk.mybatis</groupId>
                        <artifactId>mapper-spring-boot-starter</artifactId>
                        <version>2.1.5</version>
                </dependency>
                
            逆向生成(mybatis代码执行):
                 <dependency>
                        <groupId>org.mybatis.generator</groupId>
                        <artifactId>mybatis-generator-core</artifactId>
                        <version>1.3.5</version>
                 </dependency>
                 
            逆向生成(mybatis插件执行):
                 <build>
                         <plugins>
                             <plugin>
                                 <groupId>org.mybatis.generator</groupId>
                                 <artifactId>mybatis-generator-maven-plugin</artifactId>
                                 <version>1.3.5</version>
                                 <configuration>
                                     <!--generatorConfig.xml位置-->
                                     <configurationFile>src/main/resources/generatorConfig.xml</configurationFile>
                                     <verbose>true</verbose>
                                     <overwrite>true</overwrite>
                                 </configuration>
                                 <executions>
                                     <execution>
                                         <id>Generate MyBatis Artifacts</id>
                                         <goals>
                                             <goal>generate</goal>
                                         </goals>
                                         <phase>generate-sources</phase>
                                     </execution>
                                 </executions>
                                 <!--此处必须添加mysql驱动包-->
                                 <dependencies>
                                     <dependency>
                                         <groupId>mysql</groupId>
                                         <artifactId>mysql-connector-java</artifactId>
                                         <scope>runtime</scope>
                                         <version>8.0.18</version>
                                     </dependency>
                                 </dependencies>
                             </plugin>
                         </plugins>
                </build>
                
        JPA持久层框架:  
                <dependency>
                       <groupId>org.springframework.boot</groupId>
                       <artifactId>spring-boot-starter-data-jpa</artifactId>
                </dependency> 
 ### 🧊日志工具依赖
 
       方案A:
        <dependency>
                 <groupId>org.springframework.boot</groupId>
                 <artifactId>spring-boot-starter-web</artifactId>
                 <exclusions>
                          <exclusion>
                                <groupId>org.springframework.boot</groupId>
                                <artifactId>spring-boot-starter-logging</artifactId>
                          </exclusion>
                 </exclusions>
        </dependency>
      
               
        <dependency>
                    <groupId>org.slf4j</groupId>
                    <artifactId>slf4j-api</artifactId>
                    <version>1.7.21</version>
        </dependency>
        
        
        <dependency>
                    <groupId>org.slf4j</groupId>
                    <artifactId>slf4j-log4j12</artifactId>
                    <version>1.7.21</version>
        </dependency>
        
        
      方案B:
      
        <dependency>
                    <groupId>org.springframework.boot</groupId>
                    <artifactId>spring-boot-starter-web</artifactId>
                    <exclusions>
                        <exclusion>
                            <groupId>org.springframework.boot</groupId>
                            <artifactId>spring-boot-starter-logging</artifactId>
                        </exclusion>
                    </exclusions>
        </dependency>
                
         <dependency>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-starter-log4j2</artifactId>
         </dependency>
      
### 🧊通用工具依赖

                <dependency>
                    <groupId>commons-codec</groupId>
                    <artifactId>commons-codec</artifactId>
                    <version>1.11</version>
                </dependency>
               
                <dependency>
                    <groupId>org.apache.commons</groupId>
                    <artifactId>commons-lang3</artifactId>
                    <version>3.4</version>
                </dependency>
                
                <dependency>
                    <groupId>org.apache.commons</groupId>
                    <artifactId>commons-io</artifactId>
                    <version>1.3.2</version>
                </dependency>
                
                <dependency>
                    <groupId>org.projectlombok</groupId>
                    <artifactId>lombok</artifactId>
                    <version>1.18.12</version>
                </dependency>
                
                <dependency>
                    <groupId>io.springfox</groupId>
                    <artifactId>springfox-swagger2</artifactId>
                    <version>2.4.0</version>
                </dependency>
                
                <dependency>
                    <groupId>io.springfox</groupId>
                    <artifactId>springfox-swagger-ui</artifactId>
                    <version>2.4.0</version>
                </dependency>
                
                <dependency>
                    <groupId>com.github.xiaoymin</groupId>
                    <artifactId>swagger-bootstrap-ui</artifactId>
                    <version>1.6</version>
                </dependency>
                
                 <dependency>
                    <groupId>com.github.pagehelper</groupId>
                    <artifactId>pagehelper-spring-boot-starter</artifactId>
                    <version>1.2.13</version>
                 </dependency>
### 🧊消息队列依赖

         不整合SpringBoot:
            kafka:
                 <dependency>
                         <groupId>org.apache.kafka</groupId>
                         <artifactId>kafka_2.12</artifactId>
                 </dependency>
          整合SpringBoot:
            kafka:
                 <dependency>
                         <groupId>org.springframework.kafka</groupId>
                         <artifactId>spring-kafka</artifactId>
                 </dependency>
            rabbitmq:
                 <dependency>
                         <groupId>org.springframework.boot</groupId>
                         <artifactId>spring-boot-starter-amqp</artifactId>
                 </dependency>
            rocketmq:
                 <dependency>
                         <groupId>org.apache.rocketmq</groupId>
                         <artifactId>rocketmq-client</artifactId>
                         <version>4.7.1</version>
                 </dependency>
### 🧊缓存中间依赖
        
        redis:
            <dependency>
                    <groupId>org.springframework.boot</groupId>
                    <artifactId>spring-boot-starter-data-redis</artifactId>
            </dependency>
        
### 🧊搜索引擎依赖
        
        Elasticsearch:
            <dependency>
                    <groupId>org.springframework.boot</groupId>
                    <artifactId>spring-boot-starter-data-elasticsearch</artifactId>
                    <!--<version>2.1.5.RELEASE</version>-->
                    <version>2.2.2.RELEASE</version>
            </dependency>
          
### 🧊文件存储依赖
        
        FastDFS文件存储:
            <dependency>
                  <groupId>com.github.tobato</groupId>
                  <artifactId>fastdfs-client</artifactId>
                  <version>1.26.7</version>
            </dependency>
            
            
        OSS文件存储:
            <dependency>
                  <groupId>com.aliyun.oss</groupId>
                  <artifactId>aliyun-sdk-oss</artifactId>
                  <version>3.7.0</version>
            </dependency>
        