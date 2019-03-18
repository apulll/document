---
title: webpack和图片引入的一些问题
---

## 问题描述
1. 前提

    文件结构
      ```
      dist
        -assets
          --images
      public
        -assets
          --images
      ```
    webpack配置片段
      ```javascript
      resolve: {
          alias: {
              static: path.resolve(__dirname, "public/assets")
          }
      },
      plugins: [
        new CopyWebpackPlugin([
            { from: 'from/directory/**/*', to: '/absolute/path' },
        ])
      ]
      ```    
 2. jsx文件中使用图片

    - `<img src="./assets/images/**/xxx">`
    - `<img src={require('static/images/**/xxxx')}>`
    -  ```javascript
        import xxx from 'static/images/**/xxx'
        <img src={xxx}>
        ```
3. 在css中使用图片
    ```javascript
    'background-image': url('~static/images/**/xxx.jpg')
    ```
## 解决方案

1. 设置webpack
```javascript
resolve: {
    alias: {
        static: path.resolve(__dirname, "yourpath")
    }
}
```
2. 在 jsx文件中引用
```javascript
import xxx from 'static/xxxx'

<img src={xxx}/>
```
3. 在css文件中引用

```javascript
'background-image': url('~static/xxx.jpg')
```
#### 


问题描述

需要使用<img>标签引入多个图片
理想方案是将图片的相关信息（图片文字，图片相对路径）整合到一个数组imageList
然后对imageList做一个map render出图片列表
const imageList = [
  {id:1,info:'中国银行',uri:'./assets/1.jpg'},
  {id:2,info:'中国农业银行',uri:'./assets/2.jpg'},
  {id:3,info:'中国建设银行',uri:'./assets/3.jpg'}
]

imageList.map((img)=>{
    return (
    <div>
      {img.info}
      <img src={img.uri} />
    </div>
    )
})


但是这样做导致了图片无法找到
发现请求图片路径出现了问题：所有图片请求的路径变成
了当前的url拼接uri



换成import image from './assets/1.jpg'将image给src就可以拿到图片
发现这是webpack引入图片的方式

问题原因

Q1:这两种方式的区别？
A2:主要是资源路径的不同

赋给src相对路径：导致资源请求的路径变成：当前url+src的值
赋给src import的文件：

首先使用import可以返回一个字符串
这个字符串是文件被打包之后（在静态目录下）的绝对路径。
因此当前请求图片的路径就会变成：端口号+import的值






最后请求图片的路径很好解释：

如果src的值以slash开头那么会被认为是以静态文件为根目录的绝对路径
如果src的值不以slash开头那么会被认为是当前路径的相对路径

总结为：静态服务器可以被当做文件系统看待，这些uri就是文件路径


Q2:图片是如何被webpack打包的？
A2: 仅对于图片的import:

打包时机：在项目中的图片并不是都会被打包的，经过尝试发现通过import或者background:url(image path)引入的图片才会被打包
打包位置：通过对file-loader的设置
{
  test: /\.(jpg|png|svg|pdf)$/,
  exclude: /font.*\.svg$/,
  loader: 'file-loader?name=[path][name].[hash].[ext]'
}
//根据这样的设置，webpack会将所有**使用到的图片全部加载到当前path并且名字不变但是加上.hash.后缀**







Q3: img src的值可以是什么？background：url的值又可以是什么？
A3:

img src:

路径（url）
require（或者import的值）
data url（base64）


background：url：

路径（相对）
data url（base64）







Q4:为什么在css中使用background-url（）可以使用图片的相对路径但是在js文件中就不能使用相对路径赋值给src？
A4: 首先先说明我发现的区别

src：会发请求，请求静态的外部文件资源（只要是src不论值是什么（除base64）都会发送请求）
css background：不会发送请求



webpack对他们的处理方式也完全不同：
- src：url ：不会被webpack处理
- src：require（）：会被webpack处理
- background：url：会被webpack处理

总结：使用import或者require或者background都会被webpack的file-loader当做依赖模块处理：
  - 打包的时候执行到引入部分（import。。。）
  - 将对应的静态文件打包到配置好的位置
  - 生成打包后的绝对路径赋值给引入部分



Q5:客户端图片引入的方式有几种？他们分别有什么不同的原理？
A5:所谓客户端图片：就是不想通过http请求的方式获取的图片

通过background:url('./****')
通过background:url(图片base64)
通过src=图片base64
通过src=require('./******')



其他知识

K1:到今天终于明白file-loader的作用


当其他模块需要引用文件，file-loader就将对应文件依照name属性要求的文件路径和文件名进行打包。通过计算文件大小，小于limit的值，就将文件转成base64赋给需要这些文件的位置，大于limit的值就将文件的打包后路径赋给需要这些文件的位置

我的疑问：

网上有~说是作为src值的开头可以将这部分的请求作为依赖模块处理但是我没有尝试成功

我的解决方案

由于我还不知道该如何给background-url编程循环引入图片所以我决定采用：
因此我采用编写一个对象数组，对对象数组进行map，return<img src={require(bank.img+'')}>


const BANK_LIST = [
  {name: '中国银行', img: './assets/bank-logo-6.png', id: 1},
  {name: '中国建设银行', img: './assets/bank-logo-7.png', id: 2},
  {name: '兴业银行', img: './assets/bank-logo-8.png', id: 3}
]

BANK_LIST.map((bank)=>{
    return (
        <img src={require(bank.img+'')} />
    )
})



我的另外一种解决方案（由于项目中的eslint要求不支持动态引入文件）

使用webpack带的require.context('pathToDirectory')他可以循环的将一个目录下的所有文件引入

  const BANK_LIST = {
  './bank-logo-6.png': {name: '中国银行', id: 1},
  './bank-logo-7.png': {name: '中国建设银行', id: 2},
  './bank-logo-8.png': {name: '兴业银行', id: 3}
  }

  const pathToImages = require.context('./assets');
  const originalBankList = pathToImages.keys().map((key) => {
  return Object.assign({}, BANK_LIST[key], {url: pathToImages(key)})
  });
  
//这时候originalBankList的每一项会变成
{name: '中国银行', id: 1，url: "/app/src/containers/WisePort/CheckoutCounterConta
