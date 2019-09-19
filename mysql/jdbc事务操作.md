#### jdbc事务操作

```java

package com.wzw;

import com.mysql.cj.jdbc.Driver;

import java.sql.Connection;
import java.sql.DriverManager;
import java.sql.PreparedStatement;
import java.sql.ResultSet;


/**
 * @Description TODO
 * @Date 2019/9/6 23:33
 * @Created by wzw
 */

public class JdbcTest {
    public static void main(String[] args) throws Exception{
        PreparedStatement pasta1;
        PreparedStatement pstat2;
        //注册驱动
       // Class.forName("com.mysql.jdbc.Driver");
        DriverManager.registerDriver(new Driver());
        String url = "jdbc:mysql://localhost:3306/manager?serverTimezone=GMT%2B8&useUnicode=true&characterEncoding=UTF-8";
        Connection connection = DriverManager.getConnection(url, "root", "root");
        String sql2 = "update t_test set age = age + ? where id = ?";
        String sql1 = "update t_test set age = age - ? where id = ?";
        pstat2 = connection.prepareStatement(sql2);
        pasta1 = connection.prepareStatement(sql1);
        //给占位符注入值
        pasta1.setInt(1,500);
        pasta1.setInt(2,2);
        pstat2.setInt(1,500);
        pstat2.setInt(2,1);
        //开始事务
        connection.setAutoCommit(false);
        try {
            pstat2.executeUpdate();
            pasta1.executeUpdate();
            connection.commit();
        } catch (Exception e) {
            //连接没有关闭才可以回滚
            if (connection != null){
                connection.rollback();
            }
        } finally {
            pasta1.close();
            pstat2.close();
            connection.close();
        }




    }
}


```

