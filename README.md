# treeselect

二级菜单选择

## 安装

安装模块

```
npm install  -S treeselect
```

在工程中的 `main.js`文件里引入模块:

```js
import TreeSelect from 'treeselect'
```

## 使用

```html
<template>
  <TreeSelect :dataList="data"></TreeSelect>
</template>

<script>
export default {
  data() {
    return {
      data: [{
        label: '江苏',
        children: [{
          label: '南京'
        }]
      }, {
        label: '浙江',
        children: [{
          label: '杭州',
          children: [{
            label: '建邺区',
            isChecked: true,
            children: []
          }]
        }]
      }]
    }
  }
}
</script>
```
