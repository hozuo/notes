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

## 倒计时

```js
time120run() {
  this.time120second = 120
  this.sendButtonWord = '重新发送(' + this.time120second + 's)'
  clearInterval(this.timer)
  this.timer = setInterval(() => {
    this.time120second--
    this.sendButtonWord = '重新发送(' + this.time120second + 's)'
    if (this.time120second <= 0) {
      clearInterval(this.timer)
      this.sendButtonWord = '重新发送'
    }
  }, 1000)
}
```

## url转义

<img src="https://img-blog.csdnimg.cn/20190322142935704.png" style="zoom:150%;" />

## 禁用缩放

```js
window.addEventListener(
	"touchmove",
	function(event) {
		if(event.scale !== 1) {
			event.preventDefault();
		}
	}, {
		passive: false
	}
);
```

注意：禁用双指缩放后，scroll事件需要重新绑定，滚动条的事件监听touchmove，touchstart,touchend;

## 显示和隐藏

### 占用空间，隐藏时不可见

visibility="hidden";

visibility="visible";

### 不占用空间，隐藏时消失

// 隐藏

display="none";

// 显示

display="";

## 排序

  临时构造函数

```
    getSortFun(order, sortBy) {
      var ordAlpah = (order === 'asc') ? '>' : '<'
      var sortFun = new Function('a', 'b', 'return a.' + sortBy + ordAlpah + 'b.' + sortBy + '?1:-1')
      return sortFun
    }
    
    array.sort(getSortFun('desc', 'phone'));
```

指定函数

```
    getSortFun(a, b) {
      if (a.cusId === b.cusId) {
        return a.createTime > b.createTime ? -1 : 1
      }
      return a.cusId > b.cusId ? -1 : 1
    }
```

## el-table合并相同行

建立数组，相同时增加第一个相同元素的所占行数，列数固定为1

```
        const sortArray = data.records.sort(this.getSortFun)
        var numArray = []
        var sameIndex = 0
        for (let i = 0; i < sortArray.length; i++) {
          if (i === 0) {
            numArray.push(1)
            continue
          }
          if (sortArray[i].cusId === sortArray[i - 1].cusId) {
            numArray[sameIndex] += 1
            numArray.push(0)
          } else {
            sameIndex = i
            numArray.push(1)
          }
        }
```

返回值为当前单元格所占行数和列数

```
    arraySpanMethod({ row, column, rowIndex, columnIndex }) {
      if (columnIndex === 0) {
        return [this.numArray[rowIndex], 1]
      }
    },
```

## 过滤器

```
    getCdkList(queryString) {
      return queryString
        ? this.cdkList.filter(this.createFilter(queryString))
        : this.cdkList
    },

    createFilter(queryString) {
      return (cdk) => {
        return (
          cdk.snCode.toLowerCase().indexOf(queryString.toLowerCase()) === 0
        )
      }
    },
```

## 浏览器查看源码

打开config下index.js文件，找到 productionSourceMap: true ，改为 false 即可隐藏源码。