---
title: "Express, Passport and JSON Web Token(jwt) 认证入门"
date: 2017-05-06T06:04:43+00:00
url: "/2017/05/express-passport-jwt/"
draft: false
---

在使用前端框架的时候，我们的后端平台可能是 API，我们在每次请求的时候发送 jwt 而不是依赖于 Session。我们完成两个路由功能

1. 创建 `/login` 路由请求令牌
2. 创建 `/secret` 路由，只有持有令牌的认证用户能够访问。

首先， 创建项目目录。

```bash
mkdir project-dir
cd project-dir
npm init -y
yarn add express body-parser passport passport-jwt jsonwebtoken lodash
```





我们先来创建 `index.js`，启动一个最简单的 express

```js
// file: index.js
const express = require('express')
const app = express()

app.get('/', (req, res) => {
    res.json({message: 'Express is up!'})
})

app.listen(3000, () => {
    console.log('Express running')
})
```





启动我们的服务器

```bash
nodemon index.js
```





访问 `http://localhost:3000` 查看结果。
![](/2017/05/express-passport-jwt/express-passport-jwt-01.png)

修改我们的 `index.js`，导入相应的包

```js
const _ = require('lodash')
const express = require('express')
const bodyParser = require('body-parser')
const jwt = require('jsonwebtoken')
const passport = require('passport')
const passportJWT  = require('passport-jwt')

const ExtractJWT = passportJWT.ExtractJWT
const JwtStrategy = passportJWT.Strategy
```





下面创建模拟用户数组，实际工作中这些数据应该来自数据库，但现在我们采用数组。

```js
let users = [
    {
        id: 1,
        name: 'zhangjinglin',
        password: '111111'
    },
    {
        id: 2,
        name: 'test',
        password: '222222'
    }
]
```





`passport.js` 中存在 **策略** 的概念。他们是一系列的中间件，用于处理请求如何到达路由。如果定义的身份验证策略失败，这意味着回调将被调用，回调中的错误不为 `null` 或第二个参数表示用户对象但是为 `false`，那么该路由将不会被调用，而是将发送 `401` 未授权的响应。

```js
let jwtOptions = {
    jwtFromRequest: ExtractJWT.fromAuthHeader(),
    secretOrKey:  'zhangjinglin'
}

let strategy = new JwtStrategy(jwtOptions, (jwt_payload, next) => {
    console.log('payload received', jwt_payload)
    let user = users[_.findIndex(users, {id: jwt_payload.id})]
    if (user) {
        next(null, user)
    } else {
        next(null, false)
    }
})

passport.use(strategy)
```





使用 `passport body-parser`

```js

const app = express()

app.use(passport.initialize())

app.use(bodyParser.urlencoded({extended: true}))
app.use(bodyParser.json())
```





下面我们完成登录

```js
app.post('/login', (req, res) => {
    if (req.body.name && req.body.password) {
        let name = req.body.name
        let password = req.body.password
        //通常应该访问数据库
        let user = users[_.findIndex(users, { name })]
        if (!user) {
            res.status(401).json({ message: 'no such user found' })
        }
        if (user.password === password) {
            let payload = { id: user.id }
            let token = jwt.sign(payload, jwtOptions.secretOrKey)
            res.json({message: 'ok', token})
        } else {
            res.status(401).json({message: 'password not correct'})
        }
    } else {
        res.status(401).json({message: 'name and password must be vaid'})
    }
}
```





![](/2017/05/express-passport-jwt/express-passport-jwt-02.png)
下面我们要准备保护我们的路由

```js
app.get('/secret', passport.authenticate('jwt', { session: false }), (req, res) => {
    res.json('Success! You can not see this without a token')
})
```





直接访问，不提供token
![](/2017/05/express-passport-jwt/express-passport-jwt-03.png)

提供token
![](/2017/05/express-passport-jwt/express-passport-jwt-04.png)

OK，大功告成！
