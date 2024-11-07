# 一、集成[Element-plus](https://element-plus.youqua.cn/zh-CN/guide/quickstart.html)配置自动导入

【1】安装依赖

```
npm install element-plus --save
```

自动导入【`推荐`】

```
npm install -D unplugin-vue-components unplugin-auto-import
```
[^unplugin-vue-components]: 允许你导入Vue组件，而不需要在你的代码中显式地导入它们。可让你按需导入组件，从而减少初始加载大小。
[^unplugin-auto-import]: 可自动导入Vue.js相关API，如Vue自身，Vue的RFC(响应式)，Composition API，以及其他一些常用Vue功能。

【2】配置文件修改

vite.config.ts文件补充配置

```cmd
import { defineConfig } from 'vite'
import AutoImport from 'unplugin-auto-import/vite'
import Components from 'unplugin-vue-components/vite'
import { ElementPlusResolver } from 'unplugin-vue-components/resolvers'

export default defineConfig({
  // ...
  plugins: [
    // ...
    AutoImport({
      resolvers: [ElementPlusResolver()],
    }),
    Components({
      resolvers: [ElementPlusResolver()],
    }),
  ],
})
```
【3】自动导入文件说明

移除App.vue中HelloWorld组件导入，重启项目后根目录下面会多出两个自动导入的ts声明文件：

![image-20241107183744689](C:/Users/Administrator/AppData/Roaming/Typora/typora-user-images/image-20241107183744689.png)

在components.d.ts中可以看到已自动导入了HelloWorld组件，所以依靠两个插件已经实现了[vue组件](https://so.csdn.net/so/search?q=vue组件&spm=1001.2101.3001.7020)的自动引入和element的按需引入。

【4】调整vite.config.ts文件路径

```
//...
import path from 'path'

AutoImport({
    // ....
    dts: path.resolve(__dirname, 'types/auto-imports.d.ts')
}),
Components({
    // ....
    dts: path.resolve(__dirname, 'types/components.d.ts')
}),
```

# 二、按需引入element-plus图标

【1】安装依赖

```
npm install @element-plus/icons-vue
npm i -D unplugin-icons
```

[^unplugin-icons]:**unplugin-icons**是一个强大的开源项目，它允许开发者便捷地在不同框架中按需接入成千上万的图标。这些图标涵盖了约150套流行图标集，包括超过200,000个图标、logo、表情符号等。

【2】配置自动导入

```
// ...
import Icons from 'unplugin-icons/vite'
import IconsResolver from 'unplugin-icons/resolver'

plugins: [
// 自动导入里面添加一个图标组件的导入
 AutoImport({
     resolvers: [
      //...
      // 自动导入图标组件 
      IconsResolver({
        prefix: 'Icon',
      }),
    ],
 }),
 // 组件里面补充图标的注册
 Components({
        resolvers: [
              // ....
              // 自动注册图标组件
              IconsResolver({
                enabledCollections: ['ep'],
              }),
          ],
  }),
  //补充一个图标的导入配置
  Icons({
    autoInstall: true,
  }),
]
```

【3】代码测试

在HelloWorld.vue文件中添加如下图标代码:

```
<el-button type="success">点我好吗</el-button>
<el-icon size="25" color="red"> 
    <i-ep-edit /> 
</el-icon>
```

![image-20241107190253592](C:/Users/Administrator/AppData/Roaming/Typora/typora-user-images/image-20241107190253592.png)
