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

