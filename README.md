# RichEditor
* [1.development 开发文档](#dev)
* [2.production  使用文档](#pro)

## development 工程及脚手架说明文档
<a name="dev"></a>
### 1.scripts CLI

`npm run build` 编译模块代码到lib文件夹
`npm run build-dist` 打包生产版本代码到dist
`npm start` 启动调试，支持HMR
`npm publish` 发布到npm

### 2.package.json 设置全局变量BABEL_ENV

`Window 系统     ：SET BABEL_ENV=production               必须使用在shell中使用命令，写入package.json scripts中没有作用`
`Unix 系统       ：BABEL_ENV=production YOUR_COMMAND_HERE 可以在shell中使用，也可以直接写入package.json scripts中`
`cross-env 跨平台：cross-env BABEL_ENV=production YOUR_COMMAND_HERE            可以在shell中使用，也可以直接写入package.json scripts中`

### 3. "env"  .babelrc
```.babelrc
{
  "presets": [],
  "env": {
    // 1. env 指定的配置选项，将合并和覆盖 非env 配置选项。
    // 2. .babelrc "env" 会读取 process.env.BABEL_ENV 的值来判定采用何种配置，当这种不可用，就读取 process.env.NODE_ENV，如果连这也不可用，则默认为"development"。
    // 3. 可以使用以下设置此环境变量：cross-env YOUR_COMMAND_HERE (npm i cross-env --save-dev)
    "production": {
      "presets": [],
      "plugins": []
    },
    "development": {
       "presets": [],
       "plugins": []
    },
    "自定义环境": { 配置 }
  }
}
```
### FAQ
1.Module not found: Error: Can't resolve 'react-hot-loader/patch'
【解决方法】npm install react-hot-loader@next 使用beta 版本

2.打包后dist文件夹下的 js 无法正常引用
【解决方法】打包文件的自执行函数整体赋值给一个全局变量，然后在js中引用全局变量
```
打包文件后会生成如下的自执行函数：
(function(module){})([function(){},function(){}]);
然后，
var CFRichEditor = (function(module){})([function(){},function(){}]);
```

3.报错：you have multiple copies of React loaded（你有多个副本的反应加载）
【解决方案】
可能情况1：peerDependencies配置错误，导致版本重复
通常是在插件(modules)开发的场景下，你的插件需要某些依赖的支持，但是你又没必要去安装，
因为插件的宿主会去安装这些依赖，你就可以用peerDependencies去声明一下需要依赖的插件和版本，
如果出问题npm就会有警告来提醒使用者去解决版本冲突问题。

**so,简单的来说：就是插件所使用的依赖包不会直接安装，而会直接使用宿主的依赖包**
```
 "peerDependencies": {
    "react": "^0.14.0 || ^15.0.0-rc",
    "react-dom": "^0.14.0 || ^15.0.0-rc"
  },
  ```
可能情况2：插件开发，使用webpack打包，然后通过 HTML script 引入了react.js 和 react-dom.js 和 插件（例如这里的dist/CFRichEditor.js） ,
这时候webpack就应该把react,react-dom排除在打包之外。否则会重复
webpack配置:
```
externals: {
    "react": 'React', // 这时候，webpack会创建一个全局变量 React 替代react模块,而不去打包react.js。
    'react-dom': 'ReactDOM' // 同理
}
```

## production 使用文档
<a name="pro"></a>
inlineStyle:以行为单位设置，display:inline
blockStyle:以块为单位设置,display:block
*正在开发... 暂时不要使用*