# java_crud环境配置教程

本文会从安装开始，到最后写出一个带命令行交互窗口的CRUD的小项目.

# 安装

## 安装JDK

[安装地址](https://www.oracle.com/java/technologies/downloads/#jdk17-windows)

![](https://picxyxsw.oss-cn-hangzhou.aliyuncs.com/20220929221643.png)

 建议下载java17以防止有依赖问题

![](https://picxyxsw.oss-cn-hangzhou.aliyuncs.com/20220929221712.png)

点击这个下载`.msi`安装包来安装

![](https://picxyxsw.oss-cn-hangzhou.aliyuncs.com/20220929221734.png)

![](https://picxyxsw.oss-cn-hangzhou.aliyuncs.com/20220929221748.png)

这里建议不要更改**默认位置**

![](https://picxyxsw.oss-cn-hangzhou.aliyuncs.com/20220929221802.png)

安装完成🥳

## 设置环境变量

在`Windows开始菜单`搜索`环境变量`

![](https://picxyxsw.oss-cn-hangzhou.aliyuncs.com/20220929221840.png)

![](https://picxyxsw.oss-cn-hangzhou.aliyuncs.com/20220929221858.png)

在系统环境变量下面点击新建

![](https://picxyxsw.oss-cn-hangzhou.aliyuncs.com/20220929221915.png)

变量名设置为 `JAVA_HOME`

变量值设置为 `C:\Program Files\Java\jdk-17.0.4.1` 如果你有安装到其他目录请填其他目录

![](https://picxyxsw.oss-cn-hangzhou.aliyuncs.com/20220929221927.png)

新建环境变量 `CLASS_PATH` 

变量值为 `.;%JAVA_HOME%\lib\dt.jar;%JAVA_HOME%\lib\tools.jar;`

如下图所示

![](https://picxyxsw.oss-cn-hangzhou.aliyuncs.com/20220929221948.png)

注意 变量值比较奇怪 可能理解不能  但是要照上填写

在系统变量里找到`path` 双击

![](https://picxyxsw.oss-cn-hangzhou.aliyuncs.com/20220929222004.png)

点击新建  值填写

`%JAVA_HOME%\bin`

![](https://picxyxsw.oss-cn-hangzhou.aliyuncs.com/20220929222021.png)

点击确定

接下来按 Windows键加R键  输入cmd点确定

在命令行里输入`java --version`

如有正常不报错则配置成功🥳

![](https://picxyxsw.oss-cn-hangzhou.aliyuncs.com/20220929222038.png)

## 安装MySQL

[MySQL :: Download MySQL Installer (Archived Versions)](https://downloads.mysql.com/archives/installer/)

![](https://picxyxsw.oss-cn-hangzhou.aliyuncs.com/20220929222056.png)

双击打开

![](https://picxyxsw.oss-cn-hangzhou.aliyuncs.com/20220929222109.png)

选default

![](https://picxyxsw.oss-cn-hangzhou.aliyuncs.com/20220929222120.png)

直接next

![](https://picxyxsw.oss-cn-hangzhou.aliyuncs.com/20220929222134.png)

yes

![](https://picxyxsw.oss-cn-hangzhou.aliyuncs.com/20220929222149.png)

Execute

![](https://picxyxsw.oss-cn-hangzhou.aliyuncs.com/20220929222201.png)

一路next

![](https://picxyxsw.oss-cn-hangzhou.aliyuncs.com/20220929222212.png)

设一个密码

然后一路next

![](https://picxyxsw.oss-cn-hangzhou.aliyuncs.com/20220929222223.png)

![](https://picxyxsw.oss-cn-hangzhou.aliyuncs.com/20220929222238.png)

点check

点next

![](https://picxyxsw.oss-cn-hangzhou.aliyuncs.com/20220929222255.png)

最后会跳出这么一个奇奇怪怪的 MySQL JS 的命令行 （说实话我没懂为什么有这玩意

（不过你可以在上面玩玩 JS 的特性   ：）

![](https://picxyxsw.oss-cn-hangzhou.aliyuncs.com/20220929230157.png)

和命令行同时弹出来的是workbench 

![](https://picxyxsw.oss-cn-hangzhou.aliyuncs.com/20220929230229.png)

MySQL已安装完成🥳

## 安装 JetBrain IntelliJ IDEA

[下载地址](https://www.jetbrains.com/zh-cn/idea/)

直接下载咯 0配置的

# 创建项目

![](https://picxyxsw.oss-cn-hangzhou.aliyuncs.com/20220929230750.png)

新建项目

![](https://picxyxsw.oss-cn-hangzhou.aliyuncs.com/20220929230914.png)

创建配置如图所示

![](https://picxyxsw.oss-cn-hangzhou.aliyuncs.com/20220929231039.png)

点进 `src/main/java/org/example/main`运行一下试试

## 配置Maven

![](https://picxyxsw.oss-cn-hangzhou.aliyuncs.com/20220929231348.png)

点击编辑配置

![](https://picxyxsw.oss-cn-hangzhou.aliyuncs.com/20220929231437.png)

新建一个Maven配置

![](https://picxyxsw.oss-cn-hangzhou.aliyuncs.com/20220929231601.png)

配置如图 保存应用

![](https://picxyxsw.oss-cn-hangzhou.aliyuncs.com/20220929231748.png)

点击运行

![](https://picxyxsw.oss-cn-hangzhou.aliyuncs.com/20220929231717.png)

build成功后会生成一个snapshot的jar

接下来是安装maven依赖项

打开pom.xml 右键点生成

![](https://picxyxsw.oss-cn-hangzhou.aliyuncs.com/20220929231903.png)

![](https://picxyxsw.oss-cn-hangzhou.aliyuncs.com/20220929231942.png)

搜索 mysql-connector 添加这个8版本的依赖

![](https://picxyxsw.oss-cn-hangzhou.aliyuncs.com/20220929232035.png)

![](https://picxyxsw.oss-cn-hangzhou.aliyuncs.com/20220930000633.png)

生成了如图的代码则成功

在与 dependencies 同级的标签下面 添加如下代码 配置maven依赖自动安装

```xml
 <build>
        <plugins>
            <plugin>
                <artifactId>maven-assembly-plugin</artifactId>
                <executions>
                    <execution>
                        <phase>package</phase>
                        <goals>
                            <goal>single</goal>
                        </goals>
                    </execution>
                </executions>
                <configuration>
                    <descriptorRefs>
                        <descriptorRef>jar-with-dependencies</descriptorRef>
                    </descriptorRefs>
                    <archive>
                        <manifest>
                            <addClasspath>true</addClasspath>
                            <classpathPrefix>lib/</classpathPrefix>
                            <mainClass>org.example.Main</mainClass>
                        </manifest>
                    </archive>
                </configuration>
            </plugin>
        </plugins>
    </build>
```

点击右上角 ![](https://picxyxsw.oss-cn-hangzhou.aliyuncs.com/20220929232533.png) 重新加载maven变更

## 加载数据库&&创建数据表

![](https://picxyxsw.oss-cn-hangzhou.aliyuncs.com/20220929232829.png)

在右边的侧边栏中找到数据库  添加一个MySQL

![](https://picxyxsw.oss-cn-hangzhou.aliyuncs.com/20220929233002.png)

输入你刚设置的账号密码连接

弹出一个console

在里面输入

```sql
CREATE DATABASE `java_crud_demo` CHARACTER SET 'utf8mb4' COLLATE 'utf8mb4_general_ci';
```

![](https://picxyxsw.oss-cn-hangzhou.aliyuncs.com/20220929233123.png)

绿色框内是idea识别出来的sql语句  点击左上绿色箭头执行

![](https://picxyxsw.oss-cn-hangzhou.aliyuncs.com/20220929233231.png)

![](https://picxyxsw.oss-cn-hangzhou.aliyuncs.com/20220929233308.png)

右侧数据库栏里会出现你刚创建的数据库 但是里面没有表

在console里输入

```sql
create table if not exists `java_crud_demo`.`article_table`
(
    id     int unsigned auto_increment
        primary key,
    title  varchar(100) not null,
    author varchar(40)  not null,
    time   datetime     null
);
```

ctrl + a 全选中 然后点执行

![](https://picxyxsw.oss-cn-hangzhou.aliyuncs.com/20220929233643.png)

生成了表article_table 里面没有数据

简单的填充数据 可以 console输入

```sql
INSERT INTO `java_crud_demo`.`article_table` (`title`, `author`, `time`)
VALUES ('标题', '作者', NOW());
```

![](https://picxyxsw.oss-cn-hangzhou.aliyuncs.com/20220929234014.png)

![](https://picxyxsw.oss-cn-hangzhou.aliyuncs.com/20220929234049.png)

插入完了记得点这个刷新

也可以直接在table预览界面

![](https://picxyxsw.oss-cn-hangzhou.aliyuncs.com/20220930000521.png)

添加行 输入完毕后 点提交

## 数据库crud模板

新建一个crud.java

```java
package org.example;

import java.sql.*;

public class Crud {

    // MySQL 8.0 以下版本 - JDBC 驱动名及数据库 URL
    //static final String JDBC_DRIVER = "com.mysql.jdbc.Driver";
    //static final String DB_URL = "jdbc:mysql://localhost:3306/java_crud_demo";

    // MySQL 8.0 以上版本 - JDBC 驱动名及数据库 URL
    static final String JDBC_DRIVER = "com.mysql.cj.jdbc.Driver";
    static final String DB_URL = "jdbc:mysql://localhost:3306/java_crud_demo?useSSL=false&allowPublicKeyRetrieval=true&serverTimezone=UTC";


    // 数据库的用户名与密码，需要根据自己的设置
    static final String USER = "root";
    static final String PASS = "123456";

    public static void main(String[] args) {
        Connection conn = null;
        Statement stmt = null;
        try{
            // 注册 JDBC 驱动
            Class.forName(JDBC_DRIVER);

            // 打开链接
            System.out.println("连接数据库...");
            conn = DriverManager.getConnection(DB_URL,USER,PASS);

            // 执行查询
            System.out.println(" 实例化Statement对象...");
            stmt = conn.createStatement();
            String sql;
            sql = "SELECT id, title, author, time FROM java_crud_demo";
            ResultSet rs = stmt.executeQuery(sql);

            // 展开结果集数据库
            while(rs.next()){
                // 通过字段检索
                int id  = rs.getInt("id");
                String title = rs.getString("title");
                String author = rs.getString("author");
                String time = rs.getString("time");

                // 输出数据
                System.out.println("ID: " + id);
                System.out.println("标题：" + title);
                System.out.println("作者：" + author);
                System.out.println("时间：" + time);
            }
            // 完成后关闭
            rs.close();
            stmt.close();
            conn.close();
        }catch(SQLException se){
            // 处理 JDBC 错误
            se.printStackTrace();
        }catch(Exception e){
            // 处理 Class.forName 错误
            e.printStackTrace();
        }finally{
            // 关闭资源
            try{
                if(stmt!=null) stmt.close();
            }catch(SQLException se2){
            }// 什么都不做
            try{
                if(conn!=null) conn.close();
            }catch(SQLException se){
                se.printStackTrace();
            }
        }
        System.out.println("Goodbye!");
    }
}
```

接下来愉快写你的代码主逻辑咯🥳

## 双击打开jar

```java
package org.example;

import java.io.File;

public class Main {
    public static void main(String[] args) {
        if (args.length == 0) {
            try {
                String path = System.getProperty("java.class.path");
                int lastIndex = path.lastIndexOf(File.separator) + 1;
                String file_name = path.substring(lastIndex);
                ProcessBuilder pb = new ProcessBuilder();
                pb.directory(new File("."));
                System.out.printf("%s %s %s %s %s %s %s", "cmd", "/c", "start", "java", "-jar", file_name, "eject");
                        pb.command("cmd", "/c", "start", "java", "-jar", file_name, "eject");
                pb.start();
            } catch (Exception e) {
                e.printStackTrace();
            } finally {
                System.exit(0);
            }
        }
        else {
            //主函数
            System.out.println("Hello World!");
        }
    }
    
}
```

在main开头加上这样一段 他会帮你输入命令来打开一个弹出的新窗口啦🥳

## 结束

Maven 构建之后就得到了需要的jar包了🥳
