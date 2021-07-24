[TOC]

## 常用算法

### 1.搜索算法

#### 1).BFS(breadth first search)

以当前节点兄弟节点优先路径的探索方法
应用：最短路径查找
案例：查找某个目录下所有的文件

```java
//广度搜索遍历文件夹
public static void bfsListFile(String dirPath, List<String> list) {
    File file = new File(dirPath);
    File[] fs = file.listFiles();
    Queue<File> queue = new LinkedList<>();

    // 遍历第一层
    for (File f : fs) {
        // 把第一层文件夹加入队列
        if (f.isDirectory())
            queue.offer(f);
        else
            list.add(f.getAbsolutePath());
    }
    // 逐层搜索下去
    while (!queue.isEmpty()) {
        // 从队列头取一个元素
        File fileTemp = queue.poll();
        File[] fileListTemp = fileTemp.listFiles();
        for (File f : fileListTemp) {
            if (f.isDirectory())
                queue.offer(f);
            else
                list.add(f.getAbsolutePath());
        }
    }

}
```

#### 2).DFS(Depth  first search)

以深度路径为优先路径的探索方法
案例：查找某个目录下所有的文件

```java
//深度搜索遍历文件夹下所有文件
public static void dfsListFile(String dirPath, List<String> list) {
    File file = new File(dirPath);
    File[] files = file.listFiles();
    for (File tmpFile : files) {
        if (tmpFile.isDirectory()) {
            dfsListFile(tmpFile.getAbsolutePath(), list);
        } else {
            list.add(tmpFile.getAbsolutePath());
        }
    }
}
```

bfs是浪费空间节省时间，dfs是浪费时间节省空间

### 2.动态规划

动态规划背后的基本思想非常简单。大致上，若要解一个给定问题，我们需要解其不同部分（即子问题），再根据子问题的解以得出原问题的解 

```java
//动态规划框架
//初始化 base case
dp[0][0][...] = base
//进行状态转移
for '状态1' in '状态1的所有取值'：
    for '状态2' in '状态2的所有取值'：
        for ...
            dp[状态1][状态2][...] = 求最值(选择1，选择2...)
```

通俗的解释：通过子问题的解逐步累积推导出原问题的解。

实例1：求最长回文子串

```java
//dp[j][i] 字符串从j->i是否为回文数
//动态回归方程d[i-1][j+i]是否为回文数
public String longestPalindrome(String s) {
    int len=s.length();
    boolean[][] dp = new boolean[len][len];
    int max=-1;
    String str="";
    for (int i = 0; i < len; i++) {
        for (int j = 0; j <= i; j++) {
            if (s.charAt(j) == s.charAt(i) && (i - j <= 2 || dp[j+1][i-1]))
                dp[j][i] = true;
            if(dp[j][i] && i-j>max) {
                max=i-j;
                str=s.substring(j,i+1);
            }

        }
    }
    return str;
}
```

实例2：输入一个整型数组，数组里有正数也有负数。数组中的一个或连续多个整数组成一个子数组。求所有子数组的和的最大值。要求时间复杂度为O(n)

```java
/*
字串问题一般都是分解子问题的过程，假设数组长度只有1个,不用求就只能是这个一元素的大小了，如果是2个元素，3个元素呢

元素个数 最大和

1 		[0]
2 		max([0]+[1],[1])
3 		max([1]+[2],[2])
4 		max([2]+[3],[3])
...
...
n max([n-1]+[n],[n])

分析:
1.当[n]>=[n-1]+[n]的时候说明[n-1]对结果产生了副作用，以n-1结尾的钱n项和还不如n项大，那么n项就作为起点位置了
2.当[n]<[n-1]+[n]的时候说明[n-1]对结果产生了正面作用，继续判断下一部分
3.综合以上分析这明显就是动态规划的特征（核心思想是把原问题分解成子问题进行求解，也就是分治的思想）
4.综合起来dp[i] 作为以i结尾连续字串最大和，本质就只有三行代码
	max = dp[0] = nums[0];
	dp[i] = Math.max(dp[i - 1] + nums[i], nums[i])
	max = Math.max(max, dp[i]);
5. 当[n]>[n-1]+[n]时记录起始位置下标，当[n-1]+[n]>[n]时记录结束坐标，dp[i]>max时，则记录最大和
*/
 //java代码
 public int maxSubArray(int[] nums) {
        int len = nums.length;
        int[] dp = new int[len];
        dp[0] = nums[0];
        int max = dp[0];
        for (int i = 1; i < len; i++) {
            dp[i] = Math.max(dp[i - 1] + nums[i], nums[i]);
            max = Math.max(max, dp[i]);
        }
        return max;
    }

```



### 3.贪心算法

+ 贪心算法（又称贪婪算法）是指，在对问题求解]时，总是做出在当前看来是最好的选择。也就是说，不从整体最优上加以考虑，算法得到的是在某种意义上的局部最优解

+ 案例
给定一个数组，它的第 *i* 个元素是一支给定股票第 *i* 天的价格。
设计一个算法来计算你所能获取的最大利润。你可以尽可能地完成更多的交易（多次买卖一支股票）

    ```java
  /*
  分析：股票的价格既然是给出的，那么相当于提前预测了未来股票的价格，既然这样，那么假设明天股价比今天高，就买入，假设明天价格比今天低就卖出
  */
  public int maxProfit(int[] prices) {
        int len=prices.length;
        int total=0;
        //表示买入时股价，-1表示没买
        int buyPrice=-1;
        for(int i=0;i<len-1;i++){
            //当明天比今天股价高且未买股票时买入
            if(buyPrice<0 && prices[i+1]>prices[i]){
                buyPrice=prices[i];
                total-=buyPrice;
            }
            //当明天比今天股价低且买股票时卖出
            if(buyPrice>-1 && prices[i+1]<prices[i]){
                total+=prices[i];
                buyPrice=-1;
            }
        }
         //买入状态下卖出最后一股
        if(buyPrice>-1){
              total+=prices[len-1];
          }
        return total;
  }
    ```

### 4. 分治算法 

 分治算法的基本思想是将一个规模为N的问题分解为K个规模较小的子问题，这些子问题相互独立且与原问题性质相同。求出子问题的解，就可得到原问题的解。即一种分目标完成程序算法，简单问题可用二分法完成。 

案例：快速排序

```java
public static void quickSort(int[] data, int low, int high) {
    if (low > high) {
        return;
    }
    int i, j, swap, temp;
    // 基准数
    temp = data[low];
    // 低位起始扫描位置
    i = low;
    // 高位起始扫描位置
    j = high;
    while (i < j) {
        // 每次必须从右边开始探测
        // 从右向左扫描找到小于基准数的位置停下来
        while (temp <= data[j] && i < j) {
            j--;
        }
        // 从左向右扫描找到大于基准数的位置停下来
        while (temp >= data[i] && i < j) {
            i++;
        }
        // 满足条件交换
        if (i < j) {
            swap = data[i];
            data[i] = data[j];
            data[j] = swap;
        }
    }
    // 最后将基准为与i和j相等位置的数字交换
    // 最低位（基准位置）和i位置交换
    data[low] = data[i];
    data[i] = temp;
    // 继续分治
    // 递归调用左半数组
    quickSort(data, low, j - 1);
    // 递归调用右半数组
    quickSort(data, j + 1, high);
}
```

