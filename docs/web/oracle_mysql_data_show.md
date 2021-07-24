```html
<!DOCTYPE html>
<html>
<head>
<meta charset="UTF-8">
<title>Insert title here</title>
 <link rel="stylesheet" href="https://cdn.staticfile.org/twitter-bootstrap/3.3.7/css/bootstrap.min.css">
<script src="https://cdn.bootcdn.net/ajax/libs/vue/2.6.12/vue.min.js"></script>
<script src="https://cdn.bootcss.com/axios/0.19.2/axios.min.js"></script>
<style type="text/css">
	td{
		padding: 0.2em;
	}
	.container,html,body{
		background-color: black;
		color: aquamarine;
	}
</style>
<!--springboot jpa oracle+mysql https://www.cnblogs.com/cloudman-open/p/13368977.html-->
</head>
<body class="container">
	<br/>
	<div id="app">
		<input type="text" class="form-control" placeholder="项目名"><br/>
		<button @click="getProjectRole" type="button" class="btn btn-primary"> 项目角色</button>
		<button @click="getProjectRole"  type="button" class="btn btn-primary"> 项目群角色</button>
		<button @click="getProjectRole"  type="button" class="btn btn-primary"> 项目群角色成员</button>
		<div v-html="projectHtml"></div>
		<hr/>
		<button @click="getFolderRole" type="button" class="btn btn-primary"> 文件夹</button>
		<button @click="getFolderRole" type="button" class="btn btn-primary"> 文件夹域ID</button>
		<button @click="getFolderRole" type="button" class="btn btn-primary"> 域角色</button>
		<button @click="getFolderRole" type="button" class="btn btn-primary"> 域角色群</button>
		<div v-html="folerHtml"></div>
		<button @click="getTestData" type="button" class="btn btn-primary">Test</button>
		<h1 v-text="data"></h1>L
	</div>
</body>
<script type="text/javascript">
	new Vue({
		el: "#app",
	    data(){
	      return {
			  projectHtml: '',
			  folerHtml: '',
			  testData: [{'id':1,'name':'testName','createData':'2020-11-12'},
			  {'id':2,'name':'testName','createData':'2020-11-12'},
			  {'id':3,'name':'testName','createData':'2020-11-12'},
			  {'id':4,'name':'testName','createData':'2020-11-12'}],
	          data : "test",
	      }  
	    },
	    methods:{
			getProjectRole(){
				this.genData(50)
				let _this =this
				let html = '<table border="1">'
				for(key in this.testData[1]){
					html += '<td>'+key+'</td>'
				}
				for(item of this.testData){
					html+='<tr>'
					for(key in item){
						html += '<td>'+item[key]+'</td>'
					}
					html+='</tr>'
				}
				html+='</table>'
				_this.projectHtml = html
			},
			getFolderRole(){
				this.genData(100)
				let html = '<table border="1">'
				for(key in this.testData[1]){
					html += '<td>'+key+'</td>'
				}
				for(item of this.testData){
					html+='<tr>'
					for(key in item){
						html += '<td>'+item[key]+'</td>'
					}
					html+='</tr>'
				}
				html+='</table>'
				this.folerHtml = html
			},
			genData(start){
				this.testData = []
				for(let i=start;i<start+10;i++){
					var d = new Date();
					this.testData.push({'id':i,'name':this.guid(),'createData':d.getTime()})
				}
			},
			guid() {
				return 'xxxxxxxx-xxxx-4xxx-yxxx-xxxxxxxxxxxx'.replace(/[xy]/g, function (c) {
					var r = Math.random() * 16 | 0,
						v = c == 'x' ? r : (r & 0x3 | 0x8);
					return v.toString(16);
				});
			},
	    	getTestData(){
	    		let _this = this
	    		var header = {
    				headers: {
    					'Content-type': 'application/json',
    					"tokenzsk1":"testToken"
    				}
    			}
	    		axios.get('http://localhost:8080/services/test',header)
	    		.then(function (response) {
	    			_this.data = response.data
	    		})
	    		.catch(function (error) {
	    			_this.data = JSON.stringify(error)
	    		})
	    		.then(function () {
	    			//console.info("always executed")
	    		})
	    	}
	    }
	})
</script>
</html>
```