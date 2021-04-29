---
title: Element-ui和vue-element-admin
date: 2021-03-11 16:14:01
tags:

---

# Element-ui和vue-element-admin学习
## Element-ui
什么是Element-ui？

根据官网的说法，Element-ui，是一套为开发者、设计师和产品经理准备的基于Vue 2.0的由饿了么公司出品的桌面端组件库。
官网：https://element.eleme.cn/#/zh-CN

级联选择器（cascader）<img src="https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy8xMTQwNTU1OC01OWQ5ZTliZWNiMTkzN2ZkLnBuZw?x-oss-process=image/format,png">

```
<div class="block">
  <span class="demonstration">默认 click 触发子菜单</span>
  <el-cascader
    v-model="value"
    :options="options"
    @change="handleChange"></el-cascader>
</div>
<div class="block">
  <span class="demonstration">hover 触发子菜单</span>
  <el-cascader
    v-model="value"
    :options="options"
    :props="{ expandTrigger: 'hover' }"
    @change="handleChange"></el-cascader>
</div>

<script>
  export default {
    data() {
      return {
        value: [],
        options: [{
          value: 'zhinan',
          label: '指南',
          children: [{
            value: 'shejiyuanze',
            label: '设计原则',
            children: [{
              value: 'yizhi',
              label: '一致'
            }, {
              value: 'fankui',
              label: '反馈'
            }, {
              value: 'xiaolv',
              label: '效率'
            }, {
              value: 'kekong',
              label: '可控'
            }]
          }, {
            value: 'daohang',
            label: '导航',
            children: [{
              value: 'cexiangdaohang',
              label: '侧向导航'
            }, {
              value: 'dingbudaohang',
              label: '顶部导航'
            }]
          }]
        }
  }
```
## vue-element-admin
什么是vue-element-admin？

vue-element-admin是一款基于Vue和Element-ui的后台管理的集成解决方案（60%的前后端分离项目都使用它）。
官网：https://panjiachen.github.io/vue-element-admin-site/zh/

如何分析一个前端项目？

1、查看config、index.js，了解项目的基本配置。
2、查看main.js，了解导入了哪些组件。
3、查看路由配置文件index.js，寻找到所有的页面组件。
4、分析页面组件，准备的组件化操作：template/script/style。


ElementUI   VS  Ant Design
官网：
el-vue: https://element.eleme.cn	
a-vue: https://www.antdv.com/docs/vue
a-react: https://ant.design/docs/react/introduce-cn
源码：
el-vue: https://github.com/ElemeFE/element	
a-vue: https://github.com/vueComponent/ant-design-vue
a-react: https://github.com/ant-design/ant-design
人民币版
https://github.com/PanJiaChen/vue-element-admin
https://pro.ant.design/
