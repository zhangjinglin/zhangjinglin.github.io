# 使用基于 Knex 的 Bookshelf ORM和 Sqlite 创建 Node Express 项目


使用基于 Knex 的 Bookshelf ORM和 Sqlite 创建 Node Express 项目

## 设置项目

```bash
mkdir project-dir
cd project-dir
mkdir bin server
npm init -y
```





## Express

```bash
yarn add express body-parser morgan
touch app.js
```



app.js 文件内容

```js
const express = require('express')
const logger = require('morgan')
const bodyParser = require('body-parser')

const app = express()

app.use(logger('dev'))
app.use(bodyParser.json())
app.use(bodyParser.urlencoded({extended: false}))

app.get('*', (req, res) => {
  res.status(200).send({
    message: 'Welcome to the beginning of nothingness.'
  })
})

module.exports = app
```





在 `bin` 目录中创建 `www` 文件

```js
const http = require('http')
const app = require('../app')

const port = parseInt(process.env.PORT, 10) || 8000
app.set('port', port)

const server = http.createServer(app)
server.listen(port, () => {
  console.log('server running on ' + port)
})
```





为了避免每次修改代码都需要重新启动服务，我们使用 `nodemon`。

```plain
yarn add nodemon --dev
```





修改 `package.json` 创建启动服务器的命令。

```
...
"scripts": {
  "start:dev": "nodemon ./bin/www",
  "test": "echo \"Error: no test specified\" && exit 1"
},
...
```





尝试启动服务器

```bash
yarn run start:dev
```





打开浏览器可以看见结果

```json
{
  "message": "Welcome to the beginning of nothingness."
}
```







## Knex 设置

创建 `db` 目录，我们所有的数据库相关的东东放在这个位置。创建 `sqlite` 数据库

```bash
touch db/todos-dev.db
```





安装 `Knex` 工具

```bash
npm install -g knex
```





安装 `bookshelf knex sqlite`

```bash
yarn add knex bookshelf sqlite3
```





初始化 `knex`

```bash
knex init
```





这样就创建了 `knexfile.js`，我们可以配置此文件来满足我们的需求。

```js
module.exports = {
  client: 'sqlite3',
  connection: {
    filename: './db/todos-dev.db'
  },
  migrations: {
    directory: './db/migrations'
  },
  seeds: {
    directory: './db/seeds'
  }
};
```



我们来生成数据库迁移文件：

```bash
knex migrate:make create_todo
knex migrate:make create_todo_item
```




- `/db/migrations/<date>-create_todo.js`
```js
exports.up = function(knex, Promise) {
  return knex.schema.createTable('Todos', (table) => {
    table.increments()
    table.string('title')
    table.timestamps()
  })
};

exports.down = function(knex, Promise) {
  return knex.schema.dropTable('todos')
};
```

- `/db/migrations/<date>_create_todo_item.js`
```js

exports.up = function(knex, Promise) {
  return knex.schema.createTable('TodoItems', (table) => {
    table.increments()
    table.string('content')
    table.boolean('complete')
    table.integer('todo_id').references('Todos.id')
    table.timestamps()
  })
};

exports.down = function(knex, Promise) {
  return knex.schema.dropTable('TodoItems')
};
```


现在可以进行数据库迁移了

```bash
knex migrate:latest
```





下面我们来生成一些种子，以便测试。

```bash
knex seed:make todo_seed
knex seed:make todo_item_seed
```






- `/db/seeds/todo_seed.js`
```js
exports.seed = function(knex, Promise) {
  // Deletes ALL existing entries
  return knex('Todos').del()
    .then(function () {
      // Inserts seed entries
      return knex('Todos').insert([
        {id: 1, title: 'The first todo'},
        {id: 2, title: 'The second todo'},
      ]);
    });
};
```

- `/db/seeds/todo_item_seed.js`
```js
exports.seed = function(knex, Promise) {
  // Deletes ALL existing entries
  return knex('TodoItems').del()
    .then(function () {
      // Inserts seed entries
      return knex('TodoItems').insert([
        {id: 1, content: 'the content 1 in todo 1', todo_id: 1},
        {id: 2, content: 'the content 2 in todo 1', todo_id: 1},
        {id: 3, content: 'the content 1 in todo 2', todo_id: 2}
      ]);
    });
};
```


运行种子。

```bash
knex seed:run
```





可以打开 `sqlite` 数据库控制台查看插入的结果。

### 生成模型

我们先创建 `/db/db.js` 文件，来进行 `knex` 和 `bookshelf` 的初始化，因为应该在项目中使用单一实例。

```js
const knex = require('knex')(require('../knexfile'))

const bookshelf = require('bookshelf')(knex)

module.exports = {
  knex,
  bookshelf
```





我们有两个模型 `Todo` 和 `TodoItem`，是一对多的关系。让我们生成模型和集合类。

- `/db/models.js`
```js
var bookshelf = require('./db').bookshelf

const Todo = bookshelf.Model.extend({
  tableName: 'Todos',
  hasTimestamps: true,
  items() {
    return this.hasMany(TodoItem, 'todo_id')
  }
})

const TodoItem = bookshelf.Model.extend({
  tableName: 'TodoItems',
  hasTimestamps: true,
  todo() {
    return this.belongsTo(Todo, 'todo_id')
  }
})

module.exports = {
  Todo,
  Todos,
  TodoItem,
  TodoItems,
}
```




## 创建控制器和路由

- 创建文件 `controllers/todos.js`
```js
const Todo = require('../db/models').Todo

module.exports = {
  create(req, res) {
    return Todo.forge({title: req.body.title}).save()
      .then(todo => res.status(201).send(todo))
      .catch(error => res.status(400).send(error))
  },

  list(req, res) {
    return Todo.forge().fetchAll()
      .then((todos) => res.status(200).send(todos))
      .catch(error => res.status(400).send(error))
  },

  retrieve(req, res) {
    Todo.forge({ id: req.params.todoId }).fetch({ withRelated: ['items'] })
      .then(todo => {
        if (!todo) {
          res.status(400).send({
            message: 'Todo Not Found'
          })
        }
        res.status(200).send(todo)
      })
      .catch(error => res.status(400).send(error))
  },

  update(req, res) {
    Todo.forge({ id: req.params.todoId }).fetch()
      .then(todo => {
        if (!todo) {
          res.status(400).send({
            message: 'Todo Not Found'
          })
        }
        todo.save({
          title: req.body.title || todo.title
        }).then(todo => {
          res.status(200).send(todo)
        })
      })
  },

  destroy(req, res) {
    Todo.forge({ id: req.params.todoId }).fetch()
      .then(todo => {
        if (!todo) {
          res.status(400).send({
            message: 'Todo Not Found'
          })
        }
        todo.destroy()
          .then(() => res.status(204).send({
            message: 'Todo deleted successful'
          }))
      })
  }
}
```

- 创建文件 `controllers/todoitems.js`
```js
const TodoItem = require('../db/models').TodoItem

module.exports = {
  create(req, res) {
    // console.log(req.body.content, req.params.todoId);
    // return res.status(200).send({msg: 'OK'})
    return TodoItem.forge({ content: req.body.content, todo_id: req.params.todoId }).save()
      .then(todoItem => res.status(201).send(todoItem))
      .catch(error => res.status(400).send(error))
  },

  update(req, res) {
    TodoItem.forge({ id: req.params.todoItemId }).fetch()
      .then(todoItem => {
        if (!todoItem) {
          return res.status(400).send({message: 'TodoItem Not Found'})
        }

        todoItem.save({
          content: req.body.content || todoItem.content,
          complete: req.body.complete || todoItem.complete
        })
          .then(updateTodoItem => res.status(200).send(updateTodoItem))
          .catch(error => res.status(400).send(error))
      })
  },

  destroy(req, res) {
    TodoItem.forge({ id: req.params.todoItemId }).fetch()
      .then(todoItem => {
        if (!todoItem) {
          res.status(400).send({message: 'TodoItem Not Found'})
        }

        todoItem.destroy()
          .then(res.status(204).send())
          .catch(error => res.status(400).send(error))
      })
      .catch(error => res.status(400).send(error))
  }
}
```

- 创建 `controllers/index.js`，暴露我们所有的控制器
```js
const todos = require('./todos')
const todoItems = require('./todoitems')

module.exports = {
  todos,
  todoItems
}
```

- 创建 `routes/index.js` 控制路由
```js
const todosController = require('../controllers').todos
const todoItemsController = require('../controllers').todoItems

module.exports = (app) => {
  app.get('/api', (req, res) => res.status(200).send({
    message: 'Welcome to the Todo api'
  }))
  app.get('/api/todos', todosController.list)
  app.post('/api/todos', todosController.create)
  app.get('/api/todos/:todoId', todosController.retrieve)
  app.put('/api/todos/:todoId', todosController.update)
  app.delete('/api/todos/:todoId', todosController.destroy)

  app.post('/api/todos/:todoId/items', todoItemsController.create)
  app.put('/api/todos/:todoId/items/:todoItemId', todoItemsController.update)
  app.delete('/api/todos/:todoId/items/:todoItemId', todoItemsController.destroy)
}
```

- 修改 `app.js` ，添加路由
```js
...
app.use(bodyParser.urlencoded({extended: false}))

require('./routes')(app)

app.get('*', (req, res) => {
...
```


---

> 作者: [张景林](https://zhangjinglin.github.io/about/)  
> URL: https://zhangjinglin.github.io/2017/04/bookshelf-knex/  

