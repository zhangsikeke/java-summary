# vue生命周期以及常用标签

## vue生命周期

```javascript
new Vue({
	el: "#app",
    data(){
      return {
          userName : "test",
      }  
    },
	beforeCreate() {
        //实例在内存中被创建还没有初始化好data和metheds
        console.group('beforeCreate 创建前状态===============》');
    },
    created() {
        //实例被创建完毕，data和metheds在内存中被创建，但还未编译模板
        console.group('created 创建完毕状态===============》');
    },
    beforeMount() {
        //完成模板编译名单还未挂载到页面
        console.group('beforeMount 挂载前状态===============》');
    },
    mounted() {
        //模板已经挂载到页面上，一般在这做数据初始化
        console.group('mounted 挂载结束状态===============》');
    },
    beforeUpdate() {
        //实例更新前执行此函数，data已经是最新的，界面还是旧数据，dom还未被重新渲染
        console.group('beforeUpdate 更新前状态===============》');
    },
    updated() {
        //实例更新完毕调用此函数，界面已经渲染完毕
        console.group('updated 更新完成状态===============》');
    },
    beforeDestroy() {
        //实例销毁前调用，这一步用例任然可用
        console.group('beforeDestroy 销毁前状态===============》');
    },
    destroyed() {
        //实例销毁后调用，vue实例指示的所有都会解绑，所有的事件监听都会解绑，所有的子实例都会被销毁
        console.group('destroyed 销毁完成状态===============》');
    },
    methods: {
        //普通方法区域
        btn(){
            this.message = "change"
        }
    }
    //路由
	router: router
})
```

## 指令标签

- {{}} :  插值表达式用于输出对象和返回值，例如{{user.name}}

- v-html 输出原生的字符不转义

- v-model 双向绑定数据

- v-text 输出文本
- v-bind 绑定属性 例如 **v-bind:class="d-header"** 简写 **:class="d-header"**
- **v-on:click** 简写 **@click** 绑定事件
- v-if 根据条件决定是否插入dom，与v-show的区别在于是否渲染dom，v-if直接不渲染，v-show渲染但根据条件决定是否显示，其中v-else，v-else-if必须跟在v-if的后面

    ```javascript
    模板语法
    {{ #if seen }}
        <p>看见我了</p>
    {{ if }}
    指令语法
    <p v-show="seen">看见我了</p>
    ```

- v-for 遍历数据

    ```javascript
    v-for="site in sites" //对象数组
    v-for="value in obj" //迭代对象
    v-for="(value,key) in obj" //迭代对象
    v-for="(value,key,index) in obj" //迭代对象
    v-for="index in 10" //迭代整数
    ```

## 过滤器

```javascript
//全局时间格式化过滤器
Vue.filter("formatDate", function(date, format) {
	if(format == null) {
		format = 'yyyy/MM/dd h:m:s:S'
	}
	date = new Date(date);
	var map = {
		"M": date.getMonth() + 1, // 月份
		"d": date.getDate(), // 日
		"h": date.getHours(), // 小时
		"m": date.getMinutes(), // 分
		"s": date.getSeconds(), // 秒
		"q": Math.floor((date.getMonth() + 3) / 3), // 季度
		"S": date.getMilliseconds()
		// 毫秒
	};
	format = format.replace(/([yMdhmsqS])+/g, function(all, t) {
		var v = map[t];
		if(v !== undefined) {
			if(all.length > 1) {
				v = '0' + v;
				v = v.substr(v.length - 2);
			}
			return v;
		} else if(t === 'y') {
			return(date.getFullYear() + '').substr(4 - all.length);
		}
		return all;
	});
	return format;
})
```

## 路由

```javascript
const routes = [{
		path: '/login',
		component: app_login
	},
	{
		path: '/404',
		component: app_404
	},
	{
		name: "用户管理",
		path: "/",
		component: app_main,
		children: [{
			name: "用户管理",
			path: 'app/user',
			component: app_user_mgr
		}, ]
	},
	{
		path: "*", 
		redirect: "/404"
	}

]

const router = new VueRouter({
	linkActiveClass: 'active',
	routes: routes,
})


router.beforeEach((to, from, next) => {
  if (to.path == '/login') {
    //sessionStorage.removeItem('user');
  }
  let user = sessionStorage.getItem('userName');
  if (!user && to.path != '/login') {
    next({ path: '/login' })
  } else {
    next()
  }
})
```

