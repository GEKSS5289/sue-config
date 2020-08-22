# 🥧Mycat1.6.7.3
> ## 💿安装环境:CentOS 7
> ## 🚬下载
> ## 📦解压
> ## 🛠配置(server.xml)
> ## 🛠配置(schema.xml)
> ## 🍟注意(autopartition-long.txt)
> ## ⚗测试
## 🥄下载Mycat1.6.7.3
 ###  🤝作者提供:[Mycat1.6.7.3](https://shushun.oss-cn-shenzhen.aliyuncs.com/software/Mycat-server-1.6.7.3-release-20190828135747-linux.tar.gz)
# 📦解压  
      tar -zxvf Mycat-server-1.6.7.3-release-20190828135747-linux.tar.gz -C /usr/local/
# 🛠配置(server.xml)

      vim /usr/local/mycat/conf/server.xml
      
      server.xml:
         <?xml version="1.0" encoding="UTF-8"?>
         
         <!DOCTYPE mycat:server SYSTEM "server.dtd">
         <mycat:server xmlns:mycat="http://io.mycat/">
         	<system>
         	<property name="nonePasswordLogin">0</property> <!-- 0为需要密码登陆、1为不需要密码登陆 ,默认为0，设置为1则需要指定默认账户-->
         	<property name="useHandshakeV10">1</property>
         	<property name="useSqlStat">0</property>  <!-- 1为开启实时统计、0为关闭 -->
         	<property name="useGlobleTableCheck">0</property>  <!-- 1为开启全加班一致性检测、0为关闭 -->
         		<property name="sqlExecuteTimeout">300</property>  <!-- SQL 执行超时 单位:秒-->
         		<property name="sequnceHandlerType">2</property>
         		<!--<property name="sequnceHandlerPattern">(?:(\s*next\s+value\s+for\s*MYCATSEQ_(\w+))(,|\)|\s)*)+</property>-->
         		<!--必须带有MYCATSEQ_或者 mycatseq_进入序列匹配流程 注意MYCATSEQ_有空格的情况-->
         		<property name="sequnceHandlerPattern">(?:(\s*next\s+value\s+for\s*MYCATSEQ_(\w+))(,|\)|\s)*)+</property>
         	<property name="subqueryRelationshipCheck">false</property> <!-- 子查询中存在关联查询的情况下,检查关联字段中是否有分片字段 .默认 false -->
               <!--  <property name="useCompression">1</property>--> <!--1为开启mysql压缩协议-->
                 <!--  <property name="fakeMySQLVersion">5.6.20</property>--> <!--设置模拟的MySQL版本号-->
         	<!-- <property name="processorBufferChunk">40960</property> -->
         	<!-- 
         	<property name="processors">1</property> 
         	<property name="processorExecutor">32</property> 
         	 -->
                 <!--默认为type 0: DirectByteBufferPool | type 1 ByteBufferArena | type 2 NettyBufferPool -->
         		<property name="processorBufferPoolType">0</property>
         		<!--默认是65535 64K 用于sql解析时最大文本长度 -->
         		<!--<property name="maxStringLiteralLength">65535</property>-->
         		<!--<property name="sequnceHandlerType">0</property>-->
         		<!--<property name="backSocketNoDelay">1</property>-->
         		<!--<property name="frontSocketNoDelay">1</property>-->
         		<!--<property name="processorExecutor">16</property>-->
         		<!--
         			<property name="serverPort">8066</property> <property name="managerPort">9066</property> 
         			<property name="idleTimeout">300000</property> <property name="bindIp">0.0.0.0</property>
         			<property name="dataNodeIdleCheckPeriod">300000</property> 5 * 60 * 1000L; //连接空闲检查
         			<property name="frontWriteQueueSize">4096</property> <property name="processors">32</property> -->
         		<!--分布式事务开关，0为不过滤分布式事务，1为过滤分布式事务（如果分布式事务内只涉及全局表，则不过滤），2为不过滤分布式事务,但是记录分布式事务日志-->
         		<property name="handleDistributedTransactions">0</property>
         		
         			<!--
         			off heap for merge/order/group/limit      1开启   0关闭
         		-->
         		<property name="useOffHeapForMerge">0</property>
         
         		<!--
         			单位为m
         		-->
                 <property name="memoryPageSize">64k</property>
         
         		<!--
         			单位为k
         		-->
         		<property name="spillsFileBufferSize">1k</property>
         
         		<property name="useStreamOutput">0</property>
         
         		<!--
         			单位为m
         		-->
         		<property name="systemReserveMemorySize">384m</property>
         
         
         		<!--是否采用zookeeper协调切换  -->
         		<property name="useZKSwitch">false</property>
         
         		<!-- XA Recovery Log日志路径 -->
         		<!--<property name="XARecoveryLogBaseDir">./</property>-->
         
         		<!-- XA Recovery Log日志名称 -->
         		<!--<property name="XARecoveryLogBaseName">tmlog</property>-->
         		<!--如果为 true的话 严格遵守隔离级别,不会在仅仅只有select语句的时候在事务中切换连接-->
         		<property name="strictTxIsolation">false</property>
         		
         		<property name="useZKSwitch">true</property>
         		
         	</system>
         	
         	<!-- 全局SQL防火墙设置 -->
         	<!--白名单可以使用通配符%或着*-->
         	<!--例如<host host="127.0.0.*" user="root"/>-->
         	<!--例如<host host="127.0.*" user="root"/>-->
         	<!--例如<host host="127.*" user="root"/>-->
         	<!--例如<host host="1*7.*" user="root"/>-->
         	<!--这些配置情况下对于127.0.0.1都能以root账户登录-->
         	<!--
         	<firewall>
         	   <whitehost>
         	      <host host="1*7.0.0.*" user="root"/>
         	   </whitehost>
                <blacklist check="false">
                </blacklist>
         	</firewall>
         	-->
         
         	<user name="root" defaultAccount="true">
         		<property name="password">ShuShun@1558</property>
         		<property name="schemas">user</property>
         		
         		<!-- 表级 DML 权限设置 -->
         		<!-- 		
         		<privileges check="false">
         			<schema name="TESTDB" dml="0110" >
         				<table name="tb01" dml="0000"></table>
         				<table name="tb02" dml="1111"></table>
         			</schema>
         		</privileges>		
         		 -->
         	</user>
         
         	<user name="user">
         		<property name="password">user</property>
         		<property name="schemas">user</property>
         		<property name="readOnly">true</property>
         	</user>
         
         </mycat:server>

# 🛠配置(schema.xml)
       vim /usr/local/mycat/conf/schema.xml
        schema.xml:
            <?xml version="1.0"?>
            <!DOCTYPE mycat:schema SYSTEM "schema.dtd">
            <mycat:schema xmlns:mycat="http://io.mycat/">
            
            	<schema name="user" checkSQLschema="true" sqlMaxLimit="100">
            		<!-- auto sharding by id (long) -->
            		<table name="user" dataNode="dn154,dn155" rule="auto-sharding-long" />
            		</table>
            		<!-- <table name="oc_call" primaryKey="ID" dataNode="dn1$0-743" rule="latest-month-calldate"
            			/> -->
            	</schema>
            	<!-- <dataNode name="dn1$0-743" dataHost="localhost1" database="db$0-743"
            		/> -->
            	<dataNode name="dn154" dataHost="db154" database="user_154" />
            	<dataNode name="dn155" dataHost="db155" database="user_155" />
            	<!--<dataNode name="dn4" dataHost="sequoiadb1" database="SAMPLE" />
            	 <dataNode name="jdbc_dn1" dataHost="jdbchost" database="db1" />
            	<dataNode	name="jdbc_dn2" dataHost="jdbchost" database="db2" />
            	<dataNode name="jdbc_dn3" 	dataHost="jdbchost" database="db3" /> -->
            	<dataHost name="db154" maxCon="1000" minCon="10" balance="0"
            			  writeType="0" dbType="mysql" dbDriver="native" switchType="1"  slaveThreshold="100">
            		<heartbeat>select user()</heartbeat>
            		<!-- can have multi write hosts -->
            		<writeHost host="M1" url="192.168.182.154:3306" user="root"
            				   password="ShuShun@1558">
            			<!-- can have multi read hosts -->
            			<!--<readHost host="hostS2" url="192.168.1.200:3306" user="root" password="xxx" />-->
            		</writeHost>
            		<!-- <writeHost host="hostM2" url="localhost:3316" user="root" password="123456"/> -->
            	</dataHost>
            	<dataHost name="db155" maxCon="1000" minCon="10" balance="0"
                                      writeType="0" dbType="mysql" dbDriver="native" switchType="1"  slaveThreshold="100">
                            <heartbeat>select user()</heartbeat>
                            <!-- can have multi write hosts -->
                            <writeHost host="M1" url="192.168.182.155:3306" user="root"
                                               password="ShuShun@1558">
                                    <!-- can have multi read hosts -->
                                    <!--<readHost host="hostS2" url="192.168.1.200:3306" user="root" password="xxx" />-->
                            </writeHost>
                            <!-- <writeHost host="hostM2" url="localhost:3316" user="root" password="123456"/> -->
                    </dataHost>
            	<!--
            		<dataHost name="sequoiadb1" maxCon="1000" minCon="1" balance="0" dbType="sequoiadb" dbDriver="jdbc">
            		<heartbeat> 		</heartbeat>
            		 <writeHost host="hostM1" url="sequoiadb://1426587161.dbaas.sequoialab.net:11920/SAMPLE" user="jifeng" 	password="jifeng"></writeHost>
            		 </dataHost>
            
            	  <dataHost name="oracle1" maxCon="1000" minCon="1" balance="0" writeType="0" 	dbType="oracle" dbDriver="jdbc"> <heartbeat>select 1 from dual</heartbeat>
            		<connectionInitSql>alter session set nls_date_format='yyyy-mm-dd hh24:mi:ss'</connectionInitSql>
            		<writeHost host="hostM1" url="jdbc:oracle:thin:@127.0.0.1:1521:nange" user="base" 	password="123456" > </writeHost> </dataHost>
            
            		<dataHost name="jdbchost" maxCon="1000" 	minCon="1" balance="0" writeType="0" dbType="mongodb" dbDriver="jdbc">
            		<heartbeat>select 	user()</heartbeat>
            		<writeHost host="hostM" url="mongodb://192.168.0.99/test" user="admin" password="123456" ></writeHost> </dataHost>
            
            		<dataHost name="sparksql" maxCon="1000" minCon="1" balance="0" dbType="spark" dbDriver="jdbc">
            		<heartbeat> </heartbeat>
            		 <writeHost host="hostM1" url="jdbc:hive2://feng01:10000" user="jifeng" 	password="jifeng"></writeHost> </dataHost> -->
            
            	<!-- <dataHost name="jdbchost" maxCon="1000" minCon="10" balance="0" dbType="mysql"
            		dbDriver="jdbc"> <heartbeat>select user()</heartbeat> <writeHost host="hostM1"
            		url="jdbc:mysql://localhost:3306" user="root" password="123456"> </writeHost>
            		</dataHost> -->
            </mycat:schema>
# 🍟注意(autopartition-long.txt)
    将1000M-1500M注释掉
        # range start-end ,data node index
        # K=1000,M=10000.
        0-500M=0
        500M-1000M=1
        #1000M-1500M=2
# 👍特别注意
    关于密码策略mysql设置密码的时候选择旧版本的加密方式:
        ALTER USER 'root'@'%' IDENTIFIED WITH mysql_native_password BY 'ShuShun@1558';
    mysql8cli连接mycat的时候选择旧版的加密策略:
        mysql -uroot -p -P8066 --default-auth=mysql_native_password