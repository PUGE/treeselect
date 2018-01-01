# treeselect

二级菜单选择

## 安装

Use npm to download code:

```
npm install treeselect -S
```

then import it into your project, add below code into your `main.js`:

```js
import TreeSelect from 'treeselect'
```

## 使用

```html
<template>
  <TreeSelect :data="data"></TreeSelect>
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
