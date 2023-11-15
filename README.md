# SSHtest

```java
import java.util.ArrayList;
import java.util.List;

public class IPAllocationTool {
    // 三个表，分别存储每个 IP 地址段的最新 IP
    private static String[] table1 = {"35.70.0.0", "35.79.254.254"};
    private static String[] table2 = {"35.60.0.0", "35.69.254.254"};
    private static String[] table3 = {"35.30.0.0", "35.39.254.254"};

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

        for (int i = 0; i < n; i++) {
            // 从每个表中分配 IP
            String ip1 = allocateIP(table1);
            String ip2 = allocateIP(table2);
            String ip3 = allocateIP(table3);

            // 将分配的 IP 添加到结果列表中
            result.add(ip1 + "." + ip2 + "." + ip3);
        }

        return result;
    }

    private static String allocateIP(String[] table) {
        // 从表中获取最新 IP
        String latestIP = table[0];

        // 将最新 IP 转换为整数
        long ipLong = ipToLong(latestIP);

        // 将最新 IP 更新为下一个未使用的 IP
        table[0] = longToIP(ipLong + 1);

        // 返回分配的 IP
        return latestIP;
    }

    private static long ipToLong(String ip) {
        String[] ipArray = ip.split("\\.");
        long result = 0;

        for (int i = 0; i < 4; i++) {
            result = result << 8 | Integer.parseInt(ipArray[i]);
        }

        return result;
    }

    private static String longToIP(long ip) {
        return ((ip >> 24) & 0xFF) + "." +
               ((ip >> 16) & 0xFF) + "." +
               ((ip >> 8) & 0xFF) + "." +
               (ip & 0xFF);
    }
}
```