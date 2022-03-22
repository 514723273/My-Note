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