克隆项目后，使用npm install，提示使用npm install --save axios

## 深拷贝

使用等号复制引用对象会复制引用的拷贝，例如对象，修改引用下的值会导致原值发生变化，例如修改对象的成员

使用Json序列化实现深拷贝

```vue
   var temp = JSON.stringify(outbound)
   this.editOutboundForm = JSON.parse(temp)
```

## 计算属性

```
computed: {
	getNewIndex() {
	// data是使用的时候的参数        
	retun (data) => {            
		return data.index;        
	}      
},
```

## 插入背景图片

网络路径

```
<div :style="backimg"></div>

data() {
    return {
      formdata: {
        username: '',
        password: '',
        isRememberMe: true
      },
      backimg: {
        backgroundImage: 'url(http://a1.qpic.cn/psc?/V11vRcY23Wv5uu/6rbMufLWftp2YL.BXyUmnbP7l1lm4iAHo8pOayw0FKwQlAPK6DsvXogOl7bsVB6IfFeOX7eczWcpMhUN9NIi8Q!!/c)',
        height: '100%',
        width: '100%',
        float: 'left',
        backgroundRepeat: 'no - repeat',
        backgroundPosition: 'center',
        backgroundSize: 'cover',
        filter: 'blur(1px)'
      }
    }
```

本地路径

```
backgroundImage: 'url(' + require('../../assets/psc.jpg') + ')',
```

## 新建项目

`vue init webpack`

## 前端axios获取请求头

需要后端配置Access-Control-Expose-Headers

```
Access-Control-Expose-Headers
```

```
Access-Control-Expose-Headers
```

```
Access-Control-Expose-Headers
```

```java
httpResponse.setHeader("Access-Control-Expose-Headers", "_xdw_token_1, _xdw_token");
```

### 报http://localhost:8080/[object%20Promise]

img的src标签内容使用的函数使用async就会报这个错误，去掉async可以解决

## el-table表头无法对齐

添加全局样式

.el-table th {
display: table-cell !important;
}

## console.log()输出彩色

console.log('%c [request] %o', 'color:blue', config)