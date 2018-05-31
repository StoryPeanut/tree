# tree

> 基于vue2.x编写的tree树形组件，支持同时选中多个节点，支持保持单一节点打开

## tree 属性说明
|    属性名     |            描述            |     类型     |   默认值   |
| :-----------: | :-----------------------: | :---------:  | :---------: |
|   tree-list   |          tree源数据        |    Array    |       空    |
|  isSiblings   | 是否保持单一节点展开        |  Boolean     |    false    |
| isCheckBox    | 是否可以多选               | Boolean      | false      |
| callback      | 事件回调                   | Function     | null       |

## callback 回调参数说明
| 参数名 |  说明 |
| :--------: | :------: |
| thisNode | 当前点击节点对象 |
| treeData |  所有节点对象  | 
| type |  'click': 单击事件，'dbclick':双击事件，'open':节点打开关闭事件 | 

## Build Setup

``` bash
# install dependencies
npm install

# serve with hot reload at localhost:8080
npm run dev

# build for production with minification
npm run build

# build for production and view the bundle analyzer report
npm run build --report
```

For a detailed explanation on how things work, check out the [guide](http://vuejs-templates.github.io/webpack/) and [docs for vue-loader](http://vuejs.github.io/vue-loader).
