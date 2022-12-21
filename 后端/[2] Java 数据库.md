## MySQL 基础

略

## JDBC 入门

使用 JDBC 实现员工的增删改查：

- 使用 Connection 建立数据库连接；
- 使用 PreparedStatement 组合成 SQL 语句，使用 executeQuery、executeUpdate 执行；
  - `PreparedStatement pstmt = null;`
  - `String sql = "insert into employee(eno, ename, salary, dname) values(?, ?, ?, ?)";`
  - `pstmt = conn.prepareStatement(sql);`
  - `pstmt.setInt(1, 1);pstmt.setString(2, "员工一号XX");pstmt.setFloat(3, 4000f);pstmt.setString(4, "研发部");`
  - `pstmt.executeUpdate();`
- 事务：适用于场景——要么都做，要么都不做，例如一次性插入多条数据（利用事务），保证了要么都插入，要么都不插入（执行过程中抛错的情况，全部回滚）；
  - `conn.setAutoCommit(false)`关闭自动提交，即使用事务（相当于加入一个缓冲区，多条 sql 执行之后，最后写进数据库）；
  - 使用`conn.commit()`提交事务，使用`conn.rollback()`回滚事务（通常放在 catch 处理）；
- 批处理：
  - `pstmt.addBatch()`将一条条 sql 加入批处理任务；
  - `pstmt.executeBatch()` 执行刚刚 for 循环里累计的插入任务；
  - 比一条一条插入（`executeUpdate`）快；
- 配置 Druid 框架，开启数据库连接池；
## 实战 OA 系统
RBAC（Role-Based Access Control）基于角色权限控制，面向企业安全策略的访问控制方式，其核心思想是将控制访问的资源与角色进行绑定。
使用到的工具有：
- MySQL 8
- Mybatis 3.5
- Alibaba Druid
- Servlet 3.1
- Freemarker 2.3
- LayUI 2.5
## 环境搭建：将 General Maven 项目转化为 Web 项目
1. File -> Project Structure -> Modules 增加模块，Framework 选择 Web；
2. 配置 Web：
	1. 将 Web Module Deployment Descriptor 的 Path 设置为 `/Users/yuqingbo/IdeaProjects/oa/src/main/webapp/WEB-INF/web.xml`；
	2. 将 Web Resource Directory 设置为 `/Users/yuqingbo/IdeaProjects/oa/src/main/webapp`；
	3. Create Artifact❓：直接默认保存即可（应该就是确认下编译结果的输出结构）；
3. 配置 Tomcat：
	1. 右上角 Add Configuration，Add New，选择 Tomcat Server Local；
	2. 设置使用哪个 Tomcat：Tomcat Home 和 Tomcat base directory：`/opt/homebrew/opt/tomcat@8/libexec`；
	3. 设置部署哪一个 Artifact：切换 Tab 到 Deployment 中，新增刚刚设置的 Artifact，同时将 Application context 设置为 `/`（就是 URL 的起始，比如设置为 /yqb，那所有的访问都为 /yqb/xxxx）；
	4. 以后 Maven 新增的依赖，都需要手动在这里面选择发布；
## 环境搭建：下载依赖
### 添加”阿里云“仓库地址
```xml
<repositories>  
    <!--https://developer.aliyun.com/mvn/guide-->  
    <repository>  
        <id>aliyun</id>  
        <name>aliyun</name>  
        <url>https://maven.aliyun.com/repository/public</url>  
    </repository>
</repositories>
```
### 添加 Maven 依赖
```xml
    <dependencies>  
        <!-- https://mvnrepository.com/artifact/org.mybatis/mybatis -->  
        <dependency>  
            <groupId>org.mybatis</groupId>  
            <artifactId>mybatis</artifactId>  
            <version>3.5.3</version>  
        </dependency>        <!-- https://mvnrepository.com/artifact/mysql/mysql-connector-java -->  
<!--       MySQL 8 JDBC 数据库驱动-->  
        <dependency>  
            <groupId>mysql</groupId>  
            <artifactId>mysql-connector-java</artifactId>  
            <version>8.0.19</version>  
        </dependency>  
        <!-- https://mvnrepository.com/artifact/com.alibaba/druid -->  
<!--       Druid 数据库连接池-->  
        <dependency>  
            <groupId>com.alibaba</groupId>  
            <artifactId>druid</artifactId>  
            <version>1.1.14</version>  
        </dependency>  
        <!-- https://mvnrepository.com/artifact/junit/junit -->  
<!--        测试-->  
        <dependency>  
            <groupId>junit</groupId>  
            <artifactId>junit</artifactId>  
            <version>4.12</version>  
            <scope>test</scope>  
        </dependency>  
        <!-- https://mvnrepository.com/artifact/ch.qos.logback/logback-classic -->  
<!--        日志-->  
        <dependency>  
            <groupId>ch.qos.logback</groupId>  
            <artifactId>logback-classic</artifactId>  
            <version>1.2.3</version>  
            <scope>test</scope>  
        </dependency>    </dependencies>
```
### 创建 mybatis-config.xml
在 resources 文件夹下，创建文件，用于被 MyBatisUtils 解析：
```xml
<?xml version="1.0" encoding="UTF-8" ?>  
<!-- https://mybatis.org/mybatis-3/getting-started.html-->  
<!DOCTYPE configuration  
        PUBLIC "-//mybatis.org//DTD Config 3.0//EN"  
        "http://mybatis.org/dtd/mybatis-3-config.dtd">  
<configuration>  
    <settings><!--        开启驼峰命名转换，例如 form_id => formId-->        <setting name="mapUnderscoreToCamelCase" value="true"/>  
    </settings><!--    开发环境配置-->  
    <environments default="development">  
        <environment id="development">  
            <transactionManager type="JDBC"/>  
<!--            利用 Mybatis 自带连接池管理连接-->  
            <dataSource type="POOLED">  
<!--                JDBC 连接属性-->  
                <property name="driver" value="com.mysql.cj.jdbc.Driver"/>  
                <property name="url" value="jdbc://localhost:3306/demo-oa?useSSL=false&amp;useUnicode=true&amp;characterEncoding=UTF-8&amp;serverTimezone=Asia/Shanghai&amp;allowPublicKeyRetrieval=true"/>  
                <property name="username" value="root"/>  
                <property name="password" value="12345678"/>  
            </dataSource>        </environment>    </environments>    <mappers>        <mapper resource="org/mybatis/example/BlogMapper.xml"/>  
    </mappers></configuration>
```
## 环境搭建：封装 MybatisUtils、整合 Druid
- 新建 `MybatisUtils` 封装数据库执行（sqlSession 的打开、关闭、执行）；
- 新建`DruidDataSourceFactory`，修改`mybatis-config.xml`，来整合 Druid 数据库连接池库；
## 环境搭建：整合 freemarker
- 添加 freemarker 依赖，在`web.xml`中添加 freemarker servlet 配置（初始化参数、地址映射 \*.ftl），编写 TestServlet，通过 `request.getRequestDispatcher("/test.ftl").forward(request, response);`将请求转发给 freemarker servlet 进行渲染；
- 下载 layui 的资源， `src/webapp/resources/layui`底下；
- html 中 通过 `<link rel="stylesheet" href="/resources/layui/css/layui.css">`引用资源；
## 正式编码
1. 编写 usermapper：在`src/main/resources/mappers/user.xml`，编写 sql 语句；
2. 编写 userDao：在`src/main/java/.../dao/UserDao.java`，使用 MyBatisUtils 调用 mapper；
3. 编写 userService：在`src/main/java/.../service/UserService.java`，调用 userDao，处理 dao 返回的结果，例如用户不存在、密码错误、成功返回；
4. 编写 LoginServlet：在`src/main/java/.../controller/LoginServlet.java`，调用 userService 校验前端传递过来的账号密码，设置 session，返回请求结果；
5. 编写 LogoutServlet：调用`request.getSesstion().invalidate();`清空 session，并重定向`response.sendRedirect("/login.html");`，完成退出；
## 开发小点
### mapper
- 编写 mapper 之后，又要编写 dao，代码部分有重复，所以可以简化：先定义 XXDao 接口，然后在 mapper 中的 namespace 和 id 分别写上 XXDao 和 方法名，相当于进行绑定；
- 可以通过 if 语句，来动态修改 sql 语句；
- 可以通过 @Param("emp") 注解，用于 mapper 的 sql 中，例如 emp.level == 8；
```java
public Employee selectLeader(@Param("emp") Employee employee); // 这个 Param 用于 sql 中书写
```
```xml
<!--namespace与包名类名一致-->  
<mapper namespace="cn.kiyonamiyu.demo.oa.dao.EmployeeDao">  
    <!--id与方法名对应  
    parameterType与方法参数类型对应  
    resultType与方法返回类型对应-->  
    <select id="selectLeader" parameterType="cn.kiyonamiyu.demo.oa.entity.Employee" resultType="cn.kiyonamiyu.demo.oa.entity.Employee">  
        select * from adm_employee  
        where        <if test="emp.level &lt; 7">  
            level = 7 and department_id = #{emp.departmentId}  
        </if>  
        <if test="emp.level == 7">  
            level = 8  
        </if>  
        <if test="emp.level == 8"> <!-- 自己是自己的领导 -->  
            employee_id = #{emp.employeeId}  
        </if>  
    </select>  
</mapper>
```
### 新建流程
新建一个 leaveForm 之后，可以创建与之对应的处理流程。
```java
//1.持久化form表单数据,8级以下员工表单状态为processing,8级(总经理)状态为approved  
EmployeeDao employeeDao = sqlSession.getMapper(EmployeeDao.class);  
Employee employee = employeeDao.selectById(form.getEmployeeId());  
if (employee.getLevel() == 8) {  
    form.setState("approved"); // 8 级自动完成  
} else {  
    form.setState("processing");  
}  
LeaveFormDao leaveFormDao = sqlSession.getMapper(LeaveFormDao.class);  
leaveFormDao.insert(form);  
//2.增加第一条流程数据,说明表单已提交,状态为complete  
ProcessFlowDao processFlowDao = sqlSession.getMapper(ProcessFlowDao.class);  
ProcessFlow flow1 = new ProcessFlow();  
flow1.setFormId(form.getFormId());  
flow1.setOperatorId(employee.getEmployeeId());  
flow1.setAction("apply");  
flow1.setCreateTime(new Date());  
flow1.setOrderNo(1);  
flow1.setState("complete");  
flow1.setIsLast(0);  
processFlowDao.insert(flow1);
//3.分情况创建其余流程数据
//3.1 7级以下员工,生成部门经理审批任务,请假时间大于72小时,还需生成总经理审批任务
//3.2 7级员工,生成总经理审批任务
//3.3 8级员工,生成总经理审批任务,系统自动通过
```