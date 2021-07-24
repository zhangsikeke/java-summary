https://blog.csdn.net/q42368773/article/details/107216016

官方：http://maven.apache.org/guides/mini/guide-mirror-settings.html

参考：https://www.jianshu.com/p/274c363ffd7c

```
id 唯一标识
mirrorOf 指定镜像的规则。就是什么情况会从镜像仓库拉取，而不是从原本的仓库拉取
可选项参考链接：
* 匹配所有
external:* 除了本地缓存之后的所有仓库
repo,repo1 repo 或者 repo1。 这里repo指的是仓库的id，下文会提到
*,!repo1 除了repo1的所有仓库
name 名称描述
url 地址

maven项目使用的仓库一共有如下几种方式：
中央仓库，这是默认的仓库
镜像仓库，通过 sttings.xml 中的 settings.mirrors.mirror 配置
全局profile仓库，通过 settings.xml 中的 settings.repositories.repository 配置
项目仓库，通过 pom.xml 中的 project.repositories.repository 配置
项目profile仓库，通过 pom.xml 中的 project.profiles.profile.repositories.repository 配置
本地仓库
搜索顺序如下：
local_repo > settings_profile_repo > pom_profile_repo > pom_repositories > settings_mirror > central
```

```xml
<mirrorOf>central</mirrorOf> 中央仓库走此链接
<mirrorOf>*</mirrorOf> 所有远程仓库都走链接 
<mirrorOf>external:*,!foo</mirrorOf> 除了本地仓库和foo仓库都走此链接
<mirrorOf>repo1,repo2</mirrorOf> 匹配仓库repo1和repo2，使用逗号分隔多个远程仓库。 
<mirrorOf>*,!repo1</miiroOf>匹配所有远程仓库，repo1除外，使用感叹号将仓库从匹配中排除
<mirror>
      <id>tz-mirror</id>
      <mirrorOf>external:*,!mmkj</mirrorOf>
      <name>tz test nexus repository</name>
      <url>http://xxxxx:30003/repository/maven-proxy</url>
</mirror>
<mirror>
    <id>nexus-aliyun</id>
    <!--如果配置成central，就代表我们访问阿里云的central仓库，就是阿里的中央库(http://central.maven.org/maven2)-->
    <!--mirrorOf 配置成 * 就代表我们通过访问阿里云的maven仓库从而访问网路上所有的远程仓库-->
    <mirrorOf>central</mirrorOf> 
    <name>Nexus aliyun</name>
    <url>http://maven.aliyun.com/nexus/content/groups/public</url>
</mirror>
```

```xml
<mirrors>
    <mirror>
        <id>aliyun_nexus</id>
        <!--对所有仓库使用该镜像,除了一个名为maven_nexus_201的仓库除外-->
        <!--这个名为maven_nexus_201的仓库可以在javamaven项目中配置一个repository-->
        <mirrorOf>*,!maven_nexus_201</mirrorOf> 
        <url>http://maven.aliyun.com/nexus/content/groups/public/</url>
    </mirror>
  </mirrors>
  
  <repositories>
        <!-- 192.168.0.201远程仓库 -->
        <repository>
            <id>maven_nexus_201</id>
            <name>maven_nexus_201</name>
            <layout>default</layout>
            <url>http://192.168.0.201:8081/nexus/content/groups/public/</url>
            <snapshots>  
                <enabled>true</enabled>  
              </snapshots>
        </repository>
</repositories>
```

```xml
<localRepository>E:/repo</localRepository>
<mirror>
    <id>aliyunmaven</id>
    <name>aliyunmaven</name>
    <url>https://maven.aliyun.com/repository/public</url>
    <mirrorOf>central</mirrorOf>
</mirror>
```

  settings.xml 中可以使用变量，可以尝试使用变量解决。 

```xml
<mirrors>
  <mirror>
    <id>aliyun</id>
    <url>https://maven.aliyun.com/repository/public</url>
	<mirrorOf>${aliyun}</mirrorOf>
  </mirror>
  <mirror>
    <id>netease</id>
    <url>http://mirrors.163.com/maven/repository/maven-public/</url>
    <mirrorOf>${netease}</mirrorOf>
  </mirror>
   <mirror>
    <id>default</id>
    <url>http://192.168.0.100/nexus/repository/maven-public/</url>
    <mirrorOf>central</mirrorOf>
  </mirror>
</mirrors>
激活 
mvn help-effective-settings -Daliyun=central
mvn help:effective-settings -Dnetease=central
```

