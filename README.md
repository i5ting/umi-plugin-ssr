# umi-plugin-ssr

SSR(server-side render) plugin for umi.

[![NPM version](https://img.shields.io/npm/v/umi-plugin-ssr.svg?style=flat)](https://npmjs.org/package/umi-plugin-ssr)
[![Build Status](https://img.shields.io/travis/umijs/umi-plugin-ssr.svg?style=flat)](https://travis-ci.org/umijs/umi-plugin-ssr)
[![NPM downloads](http://img.shields.io/npm/dm/umi-plugin-ssr.svg?style=flat)](https://npmjs.org/package/umi-plugin-ssr)

## Why

```
const nodeExternals = require('webpack-node-externals')
const paths = require('./paths')

module.exports = merge(baseConfig, {
  mode: 'production',
  entry: {
    app: './web/page/app/index.js'
  },
  target: 'async-node',
  externals: nodeExternals({
    whilelist: /\.(css|less|sass|scss)$/
  }),
  output: {
    path: paths.appBuild,
    publicPath: '/dist',
    filename: '[name].js',
    libraryTarget: 'commonjs2'
  },

  plugins: [
    new webpack.DefinePlugin({
      __isBrowser__: false
    })
  ]
})

```

## Features

* ✔︎ 禁用 umi 内置的 HTML 生成
* ✔︎ 禁用 umi 内置的路由功能
* ✔︎ 禁用 umi 默认生成的 entry 配置
* ✔︎ 支持通过 targets 配置的补丁方案
* ✔︎ 支持自动查找 `src/pages` 下的 js 文件为 entry
* ✔︎ import 的 html 文件会被生成到 dist 目录下
* ✔︎ Hot Module Replacement
* ✔︎ 通过 `splitChunks` 配置提取公共部分
* ✔︎ 通过 `html` 配置根据入口文件自动生成 html
* ✔︎ 通过 `selectEntry` 配置选择部分 entry 以提升调试效率
* ✔︎ 支持查看 entry 列表，默认是 `index.html`，冲突时切换为 `__index.html`

## Installation

```bash
$ yarn add umi-plugin-ssr
```

## Usage

Config it in `.umirc.js` or `config/config.js`,

```js
export default {
  plugins: ['umi-plugin-ssr'],
};
```

with options,

```js
export default {
  plugins: [
    ['umi-plugin-ssr', options],
  ],
};
```

## Options

### entry

指定 webpack 的 entry 。

* Type: `Object`
* Default: null

如果没有设置 entry，会自动查找 `src/pages` 下的 js 或 ts 文件为 entry 。 

### htmlName

指定 import 生成的 html 文件的文件名。

* Type: `String`
* Default: `[name].[ext]`

可以用 `[name]`、`[path]`、`[hash]` 和 `[ext]`，详见 https://github.com/webpack-contrib/file-loader 。

### splitChunks

配置 webpack 的 splitChunks，用于提取 common 或 vendors 等。

* Type: `Boolean | Object`
* Default: false

如果值为 `true`，等于配置了 `{ chunks: 'async', name: 'vendors' }`，并且 html 的 chunks 会配置为 `["vendors", "<%= page %>"]`，详见 https://webpack.js.org/plugins/split-chunks-plugin/ 。

### html

配置给 [html-webpack-plugin](https://github.com/jantimon/html-webpack-plugin) 的配置，用于为每个 entry 自动生成 html 文件。

* Type: `Object`
* Default: `null`

如有配置，则会基于以下默认值进行覆盖，

```js
{
  template,
  filename: `${page}.html`,
  chunks: [page]
}
```

其中，

* template 有一个[默认模板](http://github.com/umijs/umi-plugin-ssr/tree/master/templates/document.ejs)，可通过配置进行覆盖
* 如果有一个和 entry 文件同目录同文件名但后缀为 `.ejs` 的文件，则会用次文件作为其 template，且优先级最高
* chunks 有一个特殊项为 `<%= page %>`，如果配置了，会被替换为当前 page 名

更多配置方式，详见 https://github.com/jantimon/html-webpack-plugin#options 。

### selectEntry

是否开启 entry 选择，以提升开发效率。

* Type: `Boolean | Object`
* Default: `false`

注：

1. 值为 Object 时会用于覆盖默认的 inquirer 配置，详见https://github.com/SBoudrias/Inquirer.js#question
2. 适用于 entry 量大的项目，只在 dev 时开启
3. 由于使用了 deasync-promise，所以在入口选择界面上按 Ctrl+C 退出会失败，且进程清理不干净。这时需手动强制退出相关 node 进程。

## LICENSE

MIT
