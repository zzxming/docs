# vite按需引入element-plus以及icon图标

以下为个人项目使用过程中配置按需引入的记录和个人理解

<br>

# 按需引入element-plus组件

在 element-plus 的官方文档里写了，按需引入需要额外的插件支持，官方推荐的自动导入的插件是 `unplugin-vue-components` 和 `unplugin-auto-import`

```
npm install -D unplugin-vue-components unplugin-auto-import
```

```unplugin-auto-import```自动按需引入 vue\vue-router\pinia 等的 api

```unplugin-vue-components```自动按需引入第三方的组件库组件和自定义的组件

安装官方配置可以实现组件的自动引入，但 css 还是需要在 main.ts 中全部导入
```
// vite.config.ts
import { defineConfig } from 'vite'
import AutoImport from 'unplugin-auto-import/vite'
import Components from 'unplugin-vue-components/vite'
import { ElementPlusResolver } from 'unplugin-vue-components/resolvers'

export default defineConfig({
  // ...
  plugins: [
    // ...
    AutoImport({
      // 自动导入 vue/vue-router/pinia 的方法
      imports: [
        'vue',
        'vue-router',
        'pinia',
      ],
      dts: 'src/auto-imports.d.ts',
      resolvers: [ElementPlusResolver()],
    }),
    Components({
      // 以 .vue 结尾的文件
      extensions: ['vue'],
      // 解析组件
      resolvers: [
        // 自动导入 Element Plus 组件
        ElementPlusResolver(),
      ],
      dts: 'src/components.d.ts',
      // 递归查找子目录
      deep: true
    }),
  ],
})
```
如果你使用了 ts，要指定自动生成的 `components.d.ts` 和 `auto-imports.d.ts` 文件路径放在 src 下，否则在自动导入的情况下 ts 获得不到类型检查从而报错

<br>

# 按需引入element-plus的css
按需引入css需要另一个插件支持 `vite-plugin-style-import`
```
npm install -D vite-plugin-style-import
```

配置 `resolveStyle` 的路径是 node_modules 文件夹下 element-plus 的 css 文件存放路径
```
// vite.config.ts
import { defineConfig } from 'vite'
import { createStyleImportPlugin } from 'vite-plugin-style-import'

export default defineConfig({
  // ...
  plugins: [
    // ...
    createStyleImportPlugin({
      libs: [
        {
          libraryName: 'element-plus',
          esModule: true,
          resolveStyle: name => {
            return `element-plus/theme-chalk/${name}.css`
          }
        }
      ]
    }),
  ],
})

```
在 vite 重启的时候可能会有没有 `consola` 依赖支持的报错，直接安装 `consola` 就行了
```
npm install -D consola
```

<br>

# 按需引入icon

要按需引入 icon 又需要安装另外的一个插件 `unplugin-icons`，这个插件核心是用来做 svg Icon 按需解析并加载的，同时它可以基于 iconify 图标库按需图标，我们在使用的时候也不需要下载图标库，可以按需下载

```
npm install -D unplugin-icons
```

```
// vite.config.ts
import { defineConfig } from 'vite'
import Components from 'unplugin-vue-components/vite'
import { ElementPlusResolver } from 'unplugin-vue-components/resolvers'
import { createStyleImportPlugin } from 'vite-plugin-style-import'
import Icons from 'unplugin-icons/vite'
import IconsResolver from 'unplugin-icons/resolver'

export default defineConfig({
  // ...
  plugins: [
    // ...
    Components({
      extensions: ['vue'],
      resolvers: [
        ElementPlusResolver(),
        IconsResolver({ 
          prefix: 'icon',   // 默认 i，false可关闭
          enabledCollections: ['ep'],
        }),
      ],
      dts: 'src/components.d.ts',
      // 递归查找子目录
      deep: true
    }),
    Icons({
      // 自动安装
      autoInstall: true,
      compiler: 'vue3',
    }),
  ],
}) 
```

之后我们在使用的时候不需要引入，但使用的时候需要加上前缀，比如使用 Search 组件，以前引入后组件中使用：`<Search />`，通过上面的配置后使用：`<IconEpSearch />` 或 `<icon-ep-search />`

<br>

# 自定义SVG图片

首先将准备好的 svg 文件存放在一个文件夹中，文件夹下可以按模块分多个文件夹，我将以下面两个 svg 为例
```
src/assets/svg/home/logo.svg
scr/assets/svg/about/comment.svg
```

`unplugin-icons` 插件中有一个 `customCollections` 属性，用来做自定义图标的加载，此插件也为我们提供的 svg 的 loader 可以直接引入

```
import { FileSystemIconLoader } from 'unplugin-icons/loaders'
export default defineConfig({
  // ...
  plugins: [
    // ...
    Components({
      extensions: ['vue'],
      resolvers: [
        ElementPlusResolver(),
        IconsResolver({ 
          prefix: 'icon',
          enabledCollections: ['ep'],
          customCollections: ['home', 'about']
        }),
      ],
      dts: 'src/components.d.ts',
    }),
    Icons({
      autoInstall: true,
      compiler: 'vue3',
      customCollections: {
        'home': FileSystemIconLoader('src/assets/svg/home'),
        'about': FileSystemIconLoader('src/assets/svg/about')
      }
    }),
  ],
}) 
```

同样，在配置完成后使用需要加上前缀
```
<IconHomeLogo />
<IconAboutComment />
```

# 完整配置

贴上完整流程下来的配置

```
import { defineConfig } from 'vite'
import vue from '@vitejs/plugin-vue'
import AutoImport from 'unplugin-auto-import/vite'
import Components from 'unplugin-vue-components/vite'
import { ElementPlusResolver } from 'unplugin-vue-components/resolvers'
import { createStyleImportPlugin } from 'vite-plugin-style-import'
import Icons from 'unplugin-icons/vite'
import IconsResolver from 'unplugin-icons/resolver'
import { FileSystemIconLoader } from 'unplugin-icons/loaders'

export default defineConfig({
  plugins: [
    vue(),
    AutoImport({
      imports: [
        'vue',
        'vue-router',
        'pinia',
      ],
      dts: 'src/auto-imports.d.ts',
      resolvers: [ElementPlusResolver()],
    }),
    Components({
      extensions: ['vue'],
      resolvers: [
        ElementPlusResolver(),
        IconsResolver({ 
          prefix: 'icon',
          enabledCollections: ['ep'],
          customCollections: ['home', 'about']
        }),
      ],
      dts: 'src/components.d.ts',
      deep: true
    }),
    Icons({
      autoInstall: true,
      compiler: 'vue3',
      customCollections: {
        'home': FileSystemIconLoader('src/assets/svg/home'),
        'about': FileSystemIconLoader('src/assets/svg/about')
      }
    }),
    createStyleImportPlugin({
      libs: [
        {
          libraryName: 'element-plus',
          esModule: true,
          resolveStyle: name => {
            return `element-plus/theme-chalk/${name}.css`
          }
        }
      ]
    }),
  ],
})

```


# 其他

在完成这一套下来，我的项目最大 js 从 1.3m 降到了 370kb，最大 css 从 400kb 降到 70kb，是很大的降幅了，当然可以通过 gzip 进一步降低

# 参考

[Vue3！ElementPlus！更加优雅的使用Icon](https://blog.csdn.net/qq_43430453/article/details/123267638)

[vite 自动导入组件、API，包括element-plus、icon](https://www.cnblogs.com/caoshufang/p/16554899.html)



