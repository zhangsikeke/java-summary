------

[TOC]



#### 一、安装

- 主页 https://www.npmjs.com/package/axios
- cdn方式(https://www.bootcdn.cn/axios/)
  ```html
  <!--vue-->
  <script src="https://cdn.bootcdn.net/ajax/libs/vue/2.6.12/vue.min.js"></script>
  <!--axios-->
  <script src="https://cdn.bootcss.com/axios/0.19.2/axios.min.js"></script>
  <!-- 用于post Content-Type: application/x-www-form-urlencoded编码 -->
  <script src="https://cdn.bootcss.com/qs/6.5.1/qs.min.js"></script>
  ```
- npm方式

  ```shell
  $ npm install axios
  ```

#### 二、使用
##### 1.get

  ```javascript
axios.get('http://xxx.aaa.com/user?ID=12345').then(function (response) {
    // handle success
    console.log("success:"+response.status+","+JSON.stringify(response.data));
}) .catch(function (error) {
    // handle error
    console.error("error:"+JSON.stringify(error));
}).then(function () {
    console.info("always executed")
});

// Optionally the request above could also be done as
var data={'id': 12345}
axios.get('/user', {params: data}).then(function (response) {
    console.log(response);
}).catch(function (error) {
    console.log(error);
}).finally(function () {
    // always executed
});  

// 添加header的调用方式
let _this = this
var header = {headers: {'Content-type': 'application/json', "tokenzsk1":"testToken"}}
axios.get('http://localhost:8080/services/test',header).then(function (response) {
    _this.data = response.data
}).catch(function (error) {
    _this.data = JSON.stringify(error)
}).then(function () {
    //console.info("always executed")
})
  ```

##### 2.post/application/json

```javascript
//(axios默认以application/json形式上传数据)
//一个请求
var data={firstName: 'Fred',lastName: 'Flintstone'}
axios.post('/user', data).then(function (response) {
	console.log(response);
  }).catch(function (error) {
	console.log(error);
  });

//同时多个请求
function getUserAccount() {
  return axios.get('/user/12345');
}
 
function getUserPermissions() {
  return axios.get('/user/12345/permissions');
}
axios.all([getUserAccount(), getUserPermissions()]).then(axios.spread(function (acct, perms) {
	// Both requests are now complete
  }));
```

##### 3.post/multipart/form-data

```html
//vue cdn引入方式带有进度的文件上传
//文件标签
<input @change="handleFileChange($event, 'zipFile')"  type="file" /> 
<script>
    //vue部分核心上传代码
    // methods文件选择方法保存文件对象
    handleFileChange: function(event, type) {
        if (type === "zipFile")
            this.zipFile = event.target.files[0]
        if (type === "imageFile")
            this.imageFile = event.target.files[0]
    },

    //创建表单
    var _this = this
    var formData = new FormData();
    formData.append("imageFile", _this.imageFile);
    formData.append("zipFile", _this.zipFile);

    //配置混合数据传递方式
    // 获取axios post 以multipart/form-data形式上传时的配置参数,callback为上传进度回调函数
    function getMultiFormConfig(callback) {
        config = {
            headers: {'Content-Type': 'multipart/form-data'},
            "onUploadProgress": function(progressEvent) {
                // 上传进度回调
                if (progressEvent.lengthComputable) {
                    var val = (progressEvent.loaded / progressEvent.total * 100);
                    val = parseInt(val)
                    console.log(val)
                    if (val < 100 && typeof callback === "function") {
                        callback(val)
                    }
                }
            }
        };
        return config
    }
    //开始上传，并获取上传进度
    axios.post("/api/his/upload", formData, getMultiFormConfig(function(percent) {
        _this.uploadPercent = percent
    })).then(function(response) {
        _this.uploadPercent = 100
        _this.initModelList()
    }).catch(function(error) {
        console.log(JSON.stringify(error))
    }).then(function() {
        console.log("always excute")
        _this.canupLoad =true
    })
</script>
```

##### 4.post/x-www-form-urlencoded

```javascript
//2.Content-Type: application/x-www-form-urlencoded
//可以不加header
var header = {headers: {'Content-type': 'application/x-www-form-urlencoded'}}
var data = {userName: "jack",psw: "123"}
axios.post('http://xxx.aaa.com/post/x-www-form-urlencoded', Qs.stringify(data), header)
	.then(function(response) {
	console.log("success:" + response.status + "," + JSON.stringify(response.data));
}).catch(function(error) {
	console.error("error:" + JSON.stringify(error));
}).then(function() {
	console.info("always executed")
});
```

##### 5.封裝api

```javascript
test_host = "http://192.168.103.126"
Api = {
	qMonInfo: function() {return axios.get(test_host + "/api/mon/info")},
	startPrint: function() {return axios.get(test_host + "/api/mon/start")}
}
//调用
Api.startPrint().then(function(response) {
	if(VueUtil.requestOK(response)) {
		_this.state = "free"
		Toast("已结束")
	} else {
		Toast(response.data.msg)
	}
}).catch((function(error) {
	Toast("结束失败" + error)
}))
```

##### 6.get添加header

```js
var header = {headers: {'Content-type': 'application/json',"tokenzsk":"testToken" }}
axios.get('/services/test',header).then(function (response) {
    _this.data = response.data
}).catch(function (error) {
    _this.data = JSON.stringify(error)
}).then(function () {
    //console.info("always executed")
})
```

