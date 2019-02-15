# 基础概念

**<u>Ant Design React</u>**  作为一个 UI 库，可以和任何 React 生态圈内的应用框架搭配使用

<u>**Ant Design Pro **</u> 是基于ES5、React、antd、dva、g2开发的开源框架

**<u>dva</u>**  是一个基于 React 和 Redux 的轻量应用框架，概念来自 elm，支持 side effects、热替换、动态加载、react-native、SSR 等，已在生产环境广泛应用

**<u>puppeteer</u>**  翻译是操纵木偶的人，利用这个工具，我们能做一个操纵页面的人。puppeteer是一个nodejs的库，支持调用Chrome的API来操纵Web

**<u>脚手架（Scaffolding）</u>**指的是能够快速搭建项目“骨架”的一类工具。例如大多数的React项目都有src，public，webpack配置文件等等，而src目录中又包含components目录等等。每次在新建项目时，手动创建这些固定的文件目录。脚手架的作用就是帮助你完成这些重复性的工作，包括一键生成主要的目录结构、安装依赖等等。

**<u>Nodejs</u>** 是一个基于 Chrome V8 引擎的 JavaScript 运行环境。

# 常用命令行

1,

> sudo npm i npm@latest -g

更新npm到最新版本,后面的-*g*表示将包安装到全局环境中 

2,安装create-react-app，它将会放在 /usr/local/lib/node_modules/ 下。nmp下载的一些工具都在这

ant-design-pro-cli  	create-react-app      npm     antd-init		dva-cl

```
npm install -g create-react-app
```





# 安装步骤

## 命令行本地安装

安装：

1，直接 clone git 仓库

```
$ git clone --depth=1 https://github.com/ant-design/ant-design-pro.git my-project
$ cd my-project
```

或者使用命令行工具（你可以使用集成化的命令行工具 [ant-design-pro-cli](https://github.com/ant-design/ant-design-pro-cli)。）

```
$ npm install ant-design-pro-cli -g
$ mkdir my-project && cd my-project
$ pro new  # 安装脚手架
```

2，本地开发-安装依赖

```
　$ npm install
```

临时使用淘宝镜像 安装依赖

```
npm --registry https://registry.npm.taobao.org install express 
```

长期使用

```
npm config set registry https://registry.npm.taobao.org
```

3，$ npm start 启动项目

在项目名目录下执行这些操作



安装好的目录结构如下

├── mock                     # 本地模拟数据
├── public
│   └── favicon.ico          # Favicon
├── src
│   ├── assets                    # 本地静态资源
│   ├── common               # 应用公用配置，如导航信息
│   ├── components         # 业务通用组件
│   ├── e2e                         # 集成测试用例
│   ├── layouts                  # 通用布局
│   ├── models                  # dva model
│   ├── routes                    # 业务页面入口和常用模板
│   ├── services                 # 后台接口服务 
│   ├── utils                        # 工具库
│   ├── g2.js                       # 可视化图形配置
│   ├── theme.js                # 主题配置
│   ├── index.ejs                # HTML 入口模板
│   ├── index.js                  # 应用入口
│   ├── index.less             # 全局样式
│   └── router.js              # 路由入口
├── tests                            # 测试工具  
├── README.md
└── package.json



## intellj idea 安装

1，安装intellj插件（这一步没成功我也能运行，估计后面还要安装）

intellj idea->Preference->Plugins→Browse repositories→搜索nodejs下载安装即可

2、在上面获取代码后，打开idea，使用File→Open打开项目目录即可

2、打开idea内嵌terminal，执行【其实此步骤可以直接拷贝其他项目下的node_modules文件】

```
npm install
```

3、在项目的package.json上右键，点击show npm scripts，如果找不到可以通过，View→tools windows→npm打开，

4、双击start执行即可跳出页面



# 报错问题

1，

```
sh-3.2# npm install dva-cli -g
npm WARN deprecated minimatch@2.0.10: Please update to minimatch 3.0.2 or higher to avoid a RegExp DoS issue
```

反对意味着它没有进一步发展，但也不会再次改变。如果一个模块依赖于一个有bug的不推荐的包，那么唯一的解决方法就是升级这个依赖项。

2,

```
npm WARN React-native@0.35.0 requires a peer of react@^15.3.1 but none was installed.
```

React-native@0.35.0需要一个大于等于react@15.3.1的包

方法一：

```
npm install -save react@^15.3.1
```

方法二：在package.json中可以添加依赖

```
"dependencies": {
"react": "^15.3.2",
"react-native": "^0.35.0",
"react-native-orientation": "^1.17.0",
"react-native-scrollable-tab-view": "^0.6.0",
}
```

3,

在pro new安装脚手架时会报错

```
ERROR: Failed to download Chromium r575458! Set "PUPPETEER_SKIP_CHROMIUM_DOW
```

原因是在这个过程中在有安装puppeteer，执行安装它的过程中需要执行install.js，这里会下载Chromium,官网建议是进行跳过，我们可以执行 —ignore-scripts 忽略这个js执行。

```
npm i --save puppeteer --ignore-scripts
```

也可以通过设置环境变量set PUPPETEER_SKIP_CHROMIUM_DOWNLOAD=1阻止下载 Chromium （这里我设置了但是失败了）

下载地址：[https://download-chromium.appspot.com/](https://link.jianshu.com/?t=https%3A%2F%2Fdownload-chromium.appspot.com%2F) (打开蓝灯翻墙软件...)

把下载刚刚下载的文件解压出来会有chrome-win32文件夹，把里面的文件拷贝到项目新建的chromium文件夹中（但是我解压出来是一个可执行软件）

有些bug并没有解决，但是我的最后还说运行出来了ant design pro 页面。why？

 

# 知识模块