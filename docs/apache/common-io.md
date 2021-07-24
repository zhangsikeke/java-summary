###  apache common io 常用方法

- 官网 http://commons.apache.org/proper/commons-io/index.html
- 参考文档：https://blog.csdn.net/langgufu314/article/details/84721816
- 参考文档：https://blog.csdn.net/chen13579867831/article/details/78993861

#### 功能列表

- **工具类**
- **输入**
- **输出**
- **过滤器**
- **比较器**
- **文件监控器**

```xml
<!--可用版本https://mvnrepository.com/artifact/commons-io/commons-io-->
<dependency>
    <groupId>commons-io</groupId>
    <artifactId>commons-io</artifactId>
    <version>2.8.0</version>
</dependency>
```

```java

String path = "C:\\Users\\admin\\Desktop\\test1\\test.txt";
String path1 = "C:\\Users\\admin\\Desktop\\test1.txt";
File file = FileUtils.getFile(path);

// 写文件-会自动创建文件夹
FileUtils.write(file, "hello", StandardCharsets.UTF_8, true);

// 复制文件(不是文件夹),新文件存在则创建,会抛异常
FileUtils.copyFile(file, FileUtils.getFile(path1));

// 复制整个文件夹 test1->test2，如果新文件夹不存在自动创建
FileUtils.copyDirectory(FileUtils.getFile("C:\\Users\\admin\\Desktop\\test1"),
                        FileUtils.getFile("C:\\Users\\admin\\Desktop\\test2"));

// 复制整个文件夹到文件夹下 test1->test3，如果新文件夹不存在自动创建
FileUtils.copyDirectoryToDirectory(FileUtils.getFile("C:\\Users\\admin\\Desktop\\test1"),
                                   FileUtils.getFile("C:\\Users\\admin\\Desktop\\test3"));

// 会抛出异常，例如java.io.FileNotFoundException
FileUtils.forceDelete(file);

// 删除-不抛出异常
FileUtils.deleteQuietly(file);

// 删除文件夹
FileUtils.deleteDirectory(file);
```

