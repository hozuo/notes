# ESlint自动格式化

由于vscode更新，自动使用exlint格式化需要添加如下配置

```json
"editor.codeActionsOnSave": {
  "source.fixAll.eslint": true,
},
```

![image-20200704175109995](C:\Users\ghzfr\AppData\Roaming\Typora\typora-user-images\image-20200704175109995.png)

配置好以后如下所示

```json
{
  "eslint.codeAction.disableRuleComment": {
  },
  "editor.codeActionsOnSave": {
    "source.fixAll.eslint": true,
  },
}
```

