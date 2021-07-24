[TOC]



#### 安装node.js

##### 1.下载地址https://nodejs.org/en/，选择LTS版本，国外网速慢，可以在腾讯软件中心下载https://pc.qq.com/

##### 2.安装node.js后设置全局和缓存路径

* 设置缓存文件夹
  npm config set cache "E:\nodejs\node_cache" 

* 设置全局模块存放路径
  npm config set prefix "E:\nodejs\node_global"

* ~~安装cnpm（淘宝cnpm）~~
  ~~npm install -g cnpm --registry=https://registry.npm.taobao.org~~

* 设置淘宝镜像(**建议npm设置淘宝镜像即可，cnpm可能会出现莫名错误**)
  npm config set registry https://registry.npm.taobao.org

##### 3.设置环境变量使vue等命令全局可用

- 添加path E:\nodejs\\nodejs
- 添加path E:\nodejs\node_global

##### 4.安裝vue

- npm install vue-cli -g 全局安装vue-cli

##### 5.创建项目

```shell
  vue init webpack "myPrj"

  E:\vuePrj>vue init webpack "dlp-f2-app"
  ? Project name dlp-f2-app
  ? Project description niiam dlp f2 device app
  ? Author zhangsike <zhangsikeke@126.com>
  ? Vue build standalone
  ? Install vue-router? Yes
  ? Use ESLint to lint your code? No
  ? Set up unit tests No
  ? Setup e2e tests with Nightwatch? No
  ? Should we run `npm install` for you after the project has been created? (recommended) 
```
##### 6.最后在项目目录中执行npm install
##### 7.运行项目 npm run dev 

##### 8.npm常见后缀含义

+ --save、简写-S 意思是把模块的版本信息保存到dependencies（生产环境依赖）中，即你的package.json文件的dependencies字段中；
+ --save-dev 、 简写-D 参数意思是把模块版本信息保存到devDependencies（开发环境依赖）中，即你的package.json文件的devDependencies字段中；
+ --save-optional 、 -O参数意思是把模块安装到optionalDependencies（可选环境依赖）中，即你的package.json文件的optionalDependencies字段中。
+ 例子
    ```shell
    npm install packagename -g 或 --global
    npm install grunt-filerev-replace -D 或 --save-dev
    npm install grunt-filerev-replace -S 或 --save
    npm install grunt-filerev-replace@0.1.5 -S 或 --save  //指定安装版本
    ```


​    

   


