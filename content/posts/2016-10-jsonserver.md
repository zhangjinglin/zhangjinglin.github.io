---
title: "Github上的开发神器 之 JSON Server & Faker"
date: 2016-10-18T09:02:23+00:00
url: "/2016/10/jsonserver/"
draft: false
---

## JSON Server: 无需任何代码，30秒搭建全功能的REST API。

## Faker: 快速生成大量的模拟数据

[JSON Server 项目地址](https://https://github.com/typicode/json-server)
[Faker 项目地址](https://github.com/Marak/faker.js)

### 什么东东？

JSON Server 是一个后端的原型工具，很多时候我们在开发SPA(Single Page Application)项目的时候，都需要后端提供REST API供前端访问，在开发过程中，JSON Server可以作为Mock Server存在，非常快速非常简单。

有了Mock Server还需要大量的数据，我们可以使用`Faker`来快速生成模拟数据。

两个神器结合，就可以快速完成有大量数据的REST API服务了。

### JSON Server 怎么安装

```plain
npm install -g json-server //or
yarn global add json-server
```





### 怎么使用

新建一个 `db.json` 文件

```json
{
  "posts": [
    {"id": 1, "title": "json-server", "author": "typicode"},
    {"id": 2, "title": "另一篇博客", "author": "张景林"}
  ],
  "comments": [
    {"id": 1, "body": "some comments", "postId": 1}
  ],
  "profile": {
    "name": "typicode"
  }
}
```



在上面的json文件中我们有三个顶级段，分别是`posts`、`comments`、`profile`，运行下面的命令

```plain
json-server --watch db.json
```



大功告成，控制台输出：

```plain

\{^_^}/ hi!

Loading db.json
Done

Resources
http://localhost:3000/posts
http://localhost:3000/comments
http://localhost:3000/profile

Home
http://localhost:3000

Type s + enter at any time to create a snapshot of the database
Watching...
```



哈哈，自动生成了三个顶级段的API端点。

尝试访问以下：

```plain
http://localhost:3000/posts  //返回全部的posts数据
http://localhost:3000/posts/1 //返回第一个post
http://localhost:3000/comments //返回全部comments数据
http://localhost:3000/posts/1/comments //返回第一个post的全部comment，有没有大惊失色？？
http://localhost:3000/posts/1/commnets/1 //返回第一个post的第一个comment，请注意comment中的关联关系
http://localhost:3000 //json-server的运行状态
```



JSON Server支持GET、POST、PUT、DELETE常见动词，满足RESTful的需求。

### 结合Faker快速生成模拟数据

我们不可能手工生成大量数据，结合`Faker`就可以快速的完成。

添加`faker`依赖：

```plain
yarn add faker //or
npm install faker --save

//需要lodash帮助我们少些代码
yarn add lodash //or
npm install loadsh --save
```



创建 `database.js`文件，返回`faker`的数据供`json server`使用。

```js
module.exports = () => {
  const faker = require('faker/locale/zh_CN')
  const _ = require('lodash')

  return {
    people: _.times(100, (n) => {
      return {
        id: n,
        name: faker.name.findName(),
        email: faker.internet.email(),
        phone: faker.phone.phoneNumber(),
        avatar: faker.internet.avatar()
      }
    })
  }
}
```



通过下面的方式运行：

```plain
json-server database.js
```



查看服务器，反回了100条数据。It’s so easy.
