# spring-boot-web-jsp-tomcat
spring-boot 整合jsp 由tomcat接管跑起来



springboot 部署为Unix Linux Service

该方式主要借助官方的spring-boot-maven-plugin创建"Fully executable" jar
这中jar包内置一个shell脚本，可以方便的将该应用设置为Unix/Linux的系统服务(init.d service) 官方对该功能在CentOS和Ubuntu进行了测试
对于OS X和FreeBSD,可能需要自定义。 具体步骤如下:

1.在pom.xml中引入插件

<!--maven的插件-->
<build>
    <plugins>
        <plugin>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-maven-plugin</artifactId>
            <configuration>
                <executable>true</executable>
            </configuration>
        </plugin>
    </plugins>
</build>
2.设置为系统服务

将你的应用打成jar包，部署到服务器
假设部署路径为/var/app，包名为app.jar
通过如下方式将应该设置为一个系统服务：

sudo ln -s /var/app/app.jar /etc/init.d/app
3.赋予可执行权限

chmod u+x app.jar
4.以系统服务的方式管理

接下来，就可以使用我们熟悉的service foo start|stop|restart来对应用进行启停等管理了

sudo service app start|stop
命令将得到形如:

Started|Stopped [PID]
5.自定义参数

在这种方式下，我们还可以使用自定义的.conf文件来变更默认配置，方法如下：

1.在jar包相同路径下创建一个.conf文件，名称应该与.jar的名称相同，如appname.conf
2.在其中配置相关变量，如：
JAVA_HOME=/usr/local/jdk
JAVA_OPTS="$JAVA_OPTS -Dspring.profiles.active=dev -Dxxx.eee=aaa"
LOG_FOLDER=/custom/log
6.日志中文乱码解决方法

使用service启动Java程序时，输出的日志是中文乱码，而直接启动程序，输出的日志却是正常的，这种问题解决方案如下： 编辑service文件:

vim /sbin/service
在env -i 后面加上:

LANG="$LANG"
