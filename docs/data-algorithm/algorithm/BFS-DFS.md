#### BFS(breadth first search)

以广度优先路径的搜索方法，也称层级搜索。使用一个队列保存当前层节点，当队列不为空时弹出节点，将子节点继续加入队列，直到遍历完成为止。
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

#### [剑指 Offer 32 - I. 从上到下打印二叉树](https://leetcode-cn.com/problems/cong-shang-dao-xia-da-yin-er-cha-shu-lcof/)

```java
public int[] levelOrder(TreeNode root) {
    if(root == null){
        return new int[]{};
    }
    List<Integer> list = new ArrayList<>();
    Queue<TreeNode> queue = new LinkedList<>();
    queue.offer(root);
    while(!queue.isEmpty()){
        //遍历每一层树
        for(int i=queue.size();i>0;i--){
            TreeNode node = queue.poll();
            list.add(node.val);
            if(node.left != null) queue.offer(node.left);
            if(node.right != null) queue.offer(node.right);
        }
    }
    return list.stream().mapToInt(Integer::valueOf).toArray();
}
```



#### DFS(Depth  first search)

以深度路径为优先路径的探索方法，使用递归实现
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