# SSHtest

```java
import java.sql.Connection;
import java.sql.DriverManager;
import java.sql.PreparedStatement;
import java.sql.ResultSet;
import java.sql.SQLException;
import java.util.ArrayList;
import java.util.List;

public class IPAllocationTool {
    private static final String DB_URL = "jdbc:mysql://your_database_url";
    private static final String USER = "your_username";
    private static final String PASSWORD = "your_password";

    public static void main(String[] args) {
        int n = Integer.parseInt(args[0]); // 从命令行参数获取需求数量
        List<String> allocatedIPs = allocateIPs(n);

        System.out.println("Allocated IP addresses:");
        for (String ip : allocatedIPs) {
            System.out.println(ip);
        }
    }

    private static List<String> allocateIPs(int n) {
        List<String> result = new ArrayList<>();

        try (Connection connection = DriverManager.getConnection(DB_URL, USER, PASSWORD)) {
            // 从每个表中分配 IP
            for (int i = 0; i < n; i++) {
                String ip1 = allocateIP(connection, "table1");
                String ip2 = allocateIP(connection, "table2");
                String ip3 = allocateIP(connection, "table3");

                // 将分配的 IP 添加到结果列表中
                result.add(ip1 + "." + ip2 + "." + ip3);
            }
        } catch (SQLException e) {
            e.printStackTrace();
        }

        return result;
    }

    private static String allocateIP(Connection connection, String tableName) throws SQLException {
        String query = "SELECT latest_ip FROM " + tableName + " FOR UPDATE";
        String update = "UPDATE " + tableName + " SET latest_ip = ? WHERE latest_ip = ?";

        try (PreparedStatement selectStatement = connection.prepareStatement(query);
             PreparedStatement updateStatement = connection.prepareStatement(update)) {

            // 从表中获取最新 IP
            ResultSet resultSet = selectStatement.executeQuery();
            resultSet.next();
            String latestIP = resultSet.getString("latest_ip");

            // 将最新 IP 更新为下一个未使用的 IP
            updateStatement.setString(1, getNextIP(latestIP));
            updateStatement.setString(2, latestIP);
            updateStatement.executeUpdate();

            // 返回分配的 IP
            return latestIP;
        }
    }

    private static String getNextIP(String ip) {
        // 实现获取下一个 IP 的逻辑
        // 省略具体实现，可以参考前面非数据库版本的 getNextIP 方法
        return ip;
    }
}
```