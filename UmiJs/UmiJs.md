# Umi 是什么？

Umi，中文可发音为乌米，是可扩展的企业级前端应用框架。
应用场景：中后台管理系统。整合了router，webpack，antd等。

---

# 快速上手

## 环境准备
node版本要求（up 10.13）

```bash
$ node -v
v10.13.0
```

## 脚手架
创建项目
```bash
$ mkdir umiapp && cd umiapp
$ yarn create @umijs/umi-app
```
安装依赖并启动
```bash
$ yarn
$ yarn start
```
默认是8000端口，在浏览器里打开 [http://localhost:8000/](http://localhost:8000/)即可

---

# 目录结构

一个基础的 Umi 项目大致是这样的，

	.
	├── package.json
	├── .umirc.ts
	├── .env
	├── dist
	├── mock
	├── public
	└── src
	    ├── .umi
	    ├── layouts/index.tsx
	    ├── pages
	        ├── index.less
	        └── index.tsx
	    └── app.ts

## 根目录
### .umirc.ts

配置文件，包含 umi 内置功能和插件的配置。

如果项目的配置不复杂，推荐在 `.umirc.ts` 中写配置；
如果项目的配置比较复杂，可以将配置写在 `config/config.ts` 中，并把配置的一部分拆出去，比如路由配置可以拆成单独的 `config/routes.ts`。

两种方式二选一，`.umirc.ts` 优先级更高。

注意⚠️： 若存在`.umirc.ts`文件，`config/config.ts`里的配置将不会生效。


### .env

环境变量。

比如：

	PORT=8888
	COMPRESS=none

### mock 目录

存储 mock 文件，此目录下所有 js 和 ts 文件会被解析为 mock 文件。

### public 目录

此目录下所有文件会被 copy 到输出路径。

## `/src` 目录

### .umi 目录

临时文件目录，比如入口文件、路由等，都会被临时生成到这里。**不要提交 .umi 目录到 git 仓库，他们会在 umi dev 和 umi build 时被删除并重新生成。**

### layouts/index.tsx

约定式路由时的全局布局文件。

### pages 目录

所有路由组件存放在这里。

### app.ts

运行时配置文件，可以在这里扩展运行时的能力，比如修改路由、修改 render 方法等。

---


# 命令行

## umi generate

内置的生成器功能，内置的类型有 `page` ，用于生成最简页面。支持别名调用 `umi g`。

```bash
$ umi generate <type> <name> [options]
```

```bash
umi generate page pageName
umi generate page pageName --typescript
umi generate page pageName --less
```

## umi webpack

查看 umi 使用的 webpack 配置。

```bash
$ umi webpack [options]
```

参数，

| 可选参数 | 说明 |
|  :-  | :-:  |
| --rules | 查看 webpack.module.rules 配置详情 |
| --rule=[name] |  查看 webpack.module.rules 中某个规则的配置详情 |
| --plugins |  查看 webpack.plugins 配置详情 |
| --plugin=[name] |  查看 webpack.plugins 中某个插件的配置详情 |

示例，

```bash
umi webpack --rules
umi webpack --rule=less
umi webpack --plugins
umi webpack --plugin=hmr
```

---

# Mock 数据

Mock 数据是前端开发过程中必不可少的一环，是分离前后端开发的关键链路。通过预先跟服务器端约定好的接口，模拟请求数据甚至逻辑，能够让前端开发独立自主，不会被服务端的开发所阻塞。


## 编写 Mock 文件

如果 `/mock/api.ts` 的内容如下，

```js
export default {
  // 支持值为 Object 和 Array
  'GET /api/users': { users: [1, 2] },

  // GET 可忽略
  '/api/users/1': { id: 1 },

  // 支持自定义函数，API 参考 express@4
  'POST /api/users/create': (req, res) => {
    // 添加跨域请求头
    res.setHeader('Access-Control-Allow-Origin', '*');
    res.end('ok');
  },
}
```

## Mock配置

默认开启

### 如何关闭 Mock？

可以通过配置关闭，

```js
export default {
  mock: false,
};
```

---

# 样式和资源文件

## 全局样式

Umi 中约定 `src/global.css` 为全局样式，如果存在此文件，会被自动引入到入口文件最前面。

比如用于覆盖样式，

```css
.ant-select-selection {
  max-height: 51px;
  overflow: auto;
}
```

## 使用图片

### JS 里使用图片

通过 require 引用相对路径的图片。

比如：

```js
export default () => <img src={require('./foo.png')} />
```

支持别名，比如通过 `@` 指向 src 目录：

```js
export default () => <img src={require('@/foo.png')} />
```

### JS 里使用svg

**组件式引入**

```jsx
import { ReactComponent as Logo } from './logo.svg'

function Analysis() {
  return <Logo width={90} height={120} />
}
```

**url式引入**

```jsx
import logoSrc from './logo.svg'

function Analysis() {
  return <img src={logoSrc} alt="logo" />
}
```


### CSS 里使用图片

通过相对路径引用。

比如，

```css
.logo {
  background: url(./foo.png);
}
```

CSS 里也支持别名，但需要在前面加 `~` 前缀，

```css
.logo {
  background: url(~@/foo.png);
}
```