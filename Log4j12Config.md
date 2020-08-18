# 🗼Log4j12
> ## ❤SpringBoot整合Log4j12
> ### 😱排除依赖
> ### 😀引入依赖
> ### ⚙配置文件
# ❤SpringBoot整合Log4j12
## 😱排除依赖
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
## 😀引入依赖
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
       <dependency>
                  <groupId>org.springframework.boot</groupId>
                  <artifactId>spring-boot-starter-log4j2</artifactId> 
       </dependency>
## ⚙配置文件
        在resource目录下创建log4j2.xml(名字必须一样)    
             <?xml version="1.0" encoding="UTF-8"?>
             <Configuration status="INFO" schema="Log4J-V2.0.xsd" monitorInterval="600" >
                 <Properties>
                     <Property name="LOG_HOME">logs</Property>
                     <property name="FILE_NAME">collector</property>
                     <property name="patternLayout">[%d{yyyy-MM-dd'T'HH:mm:ss.SSSZZ}] [%level{length=5}] [%thread-%tid] [%logger] [%X{hostName}] [%X{ip}] [%X{applicationName}] [%F,%L,%C,%M] [%m] ## '%ex'%n</property>
                 </Properties>
                 <Appenders>
                     <Console name="CONSOLE" target="SYSTEM_OUT">
                         <PatternLayout pattern="${patternLayout}"/>
                     </Console>  
                     <RollingRandomAccessFile name="appAppender" fileName="${LOG_HOME}/app-${FILE_NAME}.log" filePattern="${LOG_HOME}/app-${FILE_NAME}-%d{yyyy-MM-dd}-%i.log" >
                       <PatternLayout pattern="${patternLayout}" />
                       <Policies>
                           <TimeBasedTriggeringPolicy interval="1"/>
                           <SizeBasedTriggeringPolicy size="500MB"/>
                       </Policies>
                       <DefaultRolloverStrategy max="20"/>         
                     </RollingRandomAccessFile>
                     <RollingRandomAccessFile name="errorAppender" fileName="${LOG_HOME}/error-${FILE_NAME}.log" filePattern="${LOG_HOME}/error-${FILE_NAME}-%d{yyyy-MM-dd}-%i.log" >
                       <PatternLayout pattern="${patternLayout}" />
                       <Filters>
                           <ThresholdFilter level="warn" onMatch="ACCEPT" onMismatch="DENY"/>
                       </Filters>              
                       <Policies>
                           <TimeBasedTriggeringPolicy interval="1"/>
                           <SizeBasedTriggeringPolicy size="500MB"/>
                       </Policies>
                       <DefaultRolloverStrategy max="20"/>         
                     </RollingRandomAccessFile>            
                 </Appenders>
                 <Loggers>
                     <!-- 业务相关 异步logger -->
                     <AsyncLogger name="com.sue.*" level="info" includeLocation="true">
                       <AppenderRef ref="appAppender"/>
                     </AsyncLogger>
                     <AsyncLogger name="com.sue.*" level="info" includeLocation="true">
                       <AppenderRef ref="errorAppender"/>
                     </AsyncLogger>       
                     <Root level="info">
                         <Appender-Ref ref="CONSOLE"/>
                         <Appender-Ref ref="appAppender"/>
                         <AppenderRef ref="errorAppender"/>
                     </Root>         
                 </Loggers>
             </Configuration>