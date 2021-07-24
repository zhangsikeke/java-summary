#### 数组-对象遍历

```javascript
// 循环数组=========================
var arr  = [a,b,c,d]
for (var i in arr){
    console.log(i) //0 1 2 3
}
for (var i of arr){
    console.log(i) //a b c d
}
// 循环json=========================
var json = {'a' : 'A', 'b' : 'B', 'c' : 'D', 'd' : 'D'}
for (var key in json){
    console.log(key) //'a' 'b' 'c' 'd'
}
// Map对象==========================
var map = new Map()
// 设置：
map.set(name, value)
// 获取：
map.get(name)
// 删除：
map.delete(name)
// for of 循环 map：
var map = new Map()
map.set('a', 'A')
map.set('b', 'B')
map.set('c', 'C')
for(var name of map){
    console.log(name) //['a', 'A'], ['b', 'B'], ['c', 'C'] 
}
for(var key of map.keys()){   //只循环key
    console.log(key ) //a b c
}
for(var value of map.values()){  //只循环value
    console.log(value ) //A B C
}
```

#### 常用函数

```javascript
JSON.parse(jsonstr); //可以将json字符串转换成json对象 
JSON.stringify(jsonobj); //可以将json对象转换成json对符串 
// 生成uuid
guid() {
    return 'xxxxxxxx-xxxx-4xxx-yxxx-xxxxxxxxxxxx'.replace(/[xy]/g, function (c) {
        var r = Math.random() * 16 | 0
        var v = c == 'x' ? r : (r & 0x3 | 0x8)
        return v.toString(16)
    });
}
```

