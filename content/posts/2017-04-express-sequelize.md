---
title: "创建基于 Sequelize ORM 和 Sqlite 的 Node Express 项目"
date: 2017-04-24T08:12:01+00:00
url: "/2017/04/express-sequelize/"
draft: false
---

如何使用 Sequelize ORM 来创建一个 Express 的项目

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
server.listen(port)
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







## Sequelize 设置

安装 `Sequelize CLI` 工具

```bash
npm install -g sequelize-cli
```





创建 `.sequelizerc` 文件描述路径。

```js
const path = require('path')

module.exports = {
  "config": path.resolve('./server/config', 'config.json'),
  "models-path": path.resolve('./server/models'),
  "seeders-path": path.resolve('./server/seeders'),
  "migrations-path": path.resolve('./server/migrations')
}
```





安装 `sequelize sqlite`

```bash
yarn add sequelize sqlite3
```





初始化 `sequelize`

```bash
sequelize init
```





修改 `server/models/index.js` 从 `es5` 修改为 `es2015`

```js
'use strict';

const fs        = require('fs');
const path      = require('path');
const Sequelize = require('sequelize');
const basename  = path.basename(module.filename);
const env       = process.env.NODE_ENV || 'development';
const config    = require(__dirname + '/../config/config.json')[env];
const db        = {};

let sequelize

if (config.use_env_variable) {
  sequelize = new Sequelize(process.env[config.use_env_variable]);
} else {
  sequelize = new Sequelize(config.database, config.username, config.password, config);
}

fs
  .readdirSync(__dirname)
  .filter(function(file) {
    return (file.indexOf('.') !== 0) && (file !== basename) && (file.slice(-3) === '.js');
  })
  .forEach(function(file) {
    var model = sequelize['import'](path.join(__dirname, file));
    db[model.name] = model;
  });

Object.keys(db).forEach(function(modelName) {
  if (db[modelName].associate) {
    db[modelName].associate(db);
  }
});

db.sequelize = sequelize;
db.Sequelize = Sequelize;

module.exports = db;
```





创建 `sqlite` 数据库

```bash
touch todos-dev.db
```





修改 `server/config/config.json`

```json
{
  "development": {
    "storage": "todos-dev.db",
    "dialect": "sqlite"
  },
  "test": {
    "storage": "todos-test.db",
    "dialect": "sqlite"
  }
}
```







### 生成模型

我们有两个模型 `Todo` 和 `TodoItem`，是一对多的关系。让我们生成 `Todo` 模型。

```bash
sequelize model:create --name Todo --attributes title:string
```





生成了 `server/models/todo.js` 和 `server/migrations/<date>-create-todo.js` 两个文件，一个是模型文件，一个是数据库迁移文件。

生成 `TodoItem` 模型。

```bash
sequelize model:create --name TodoItem --attributes content:string,complete:boolean
```





修改 `todo.js`，添加关系并更改为 `es2015`

```js
module.exports = (sequelize, DataTypes) => {
  const Todo = sequelize.define('Todo', {
    title: {
      type: DataTypes.STRING,
      allowNull: false,
    }
  }, {
    classMethods: {
      associate: (models) => {
        // associations can be defined here
        Todo.hasMany(models.TodoItem, {
          foreignKey: 'todoId',
          as: 'todoItems',
        })
      }
    }
  });
  return Todo;
};
```





修改 `todoitem.js`，添加关系并更改为 `es2015`

```js
module.exports = (sequelize, DataTypes) => {
  const TodoItem = sequelize.define('TodoItem', {
    content: {
      type: DataTypes.STRING,
      allowNull: false,
    },
    complete: {
      type: DataTypes.BOOLEAN,
      defaultValue: false
    }
  }, {
    classMethods: {
      associate: (models) => {
        // associations can be defined here
        TodoItem.belongsTo(models.Todo, {
          foreignKey: 'todoId',
          onDelete: 'CASCADE'
        })
      }
    }
  });
  return TodoItem;
};
```





为了保持一致性，我们修改 `<date>-create-todo.js` 迁移文件并更改为 `es2015`

```js
module.exports = {
  up: (queryInterface, Sequelize) => {
    return queryInterface.createTable('Todos', {
      id: {
        allowNull: false,
        autoIncrement: true,
        primaryKey: true,
        type: Sequelize.INTEGER
      },
      title: {
        type: Sequelize.STRING,
        allowNull: false
      },
      createdAt: {
        allowNull: false,
        type: Sequelize.DATE
      },
      updatedAt: {
        allowNull: false,
        type: Sequelize.DATE
      }
    });
  },
  down: (queryInterface, Sequelize) => {
    return queryInterface.dropTable('Todos');
  }
};
```





同样处理 `<date>-create-todo-item.js`

```js
'use strict';
module.exports = {
  up: (queryInterface, Sequelize) => {
    return queryInterface.createTable('TodoItems', {
      id: {
        allowNull: false,
        autoIncrement: true,
        primaryKey: true,
        type: Sequelize.INTEGER
      },
      content: {
        type: Sequelize.STRING,
        allowNull: false,
      },
      complete: {
        type: Sequelize.BOOLEAN,
        defaultValue: false,
      },
      createdAt: {
        allowNull: false,
        type: Sequelize.DATE
      },
      updatedAt: {
        allowNull: false,
        type: Sequelize.DATE
      },
      todoId: {
        type: Sequelize.INTEGER,
        onDelete: 'CASCADE',
        refrences: {
          model: 'Todos',
          key: 'id',
          as: 'todoId'
        }
      }
    });
  },
  down: (queryInterface, Sequelize) => {
    return queryInterface.dropTable('TodoItems');
  }
};
```





现在我们可以进行数据库迁移了

```bash
sequelize db:migrate
```







## 创建控制器和路由

创建文件 `server/controllers/todos.js`

```js
const Todo = require('../models').Todo

module.exports = {
  create(req, res) {
    return Todo.create({
      title: req.body.title,
    })
    .then(todo => res.status(201).send(todo))
    .catch(error => res.status(400).send(error))
  }
}
```





创建 `server/controllers/index.js`，暴露我们所有的控制器

```js
const Todo = require('../models').Todo

module.exports = {
  create(req, res) {
    return Todo.create({
      title: req.body.title,
    })
    .then(todo => res.status(201).send(todo))
    .catch(error => res.status(400).send(error))
  }
}
```





创建 `server/controllers/index.js`，暴露我们所有的控制器

```js
const todos = require('./todos')

module.exports = {
  todos,
}
```





创建 `server/routes/index.js` 控制路由

```js
const todosController = require('../controllers').todos

module.exports = (app) =>  {
  app.get('/api', (req, res) => res.status(200).send({
    message: 'Welcome to the Todos API'
  }))
  app.post('/api/todos', todosController.create)
}
```





修改 `app.js` ，添加路由

```js
...
app.use(bodyParser.urlencoded({extended: false}))

require('./server/routes/')(app)

app.get('*', (req, res) => {
...
```





启动 `postman` 来进行测试
![](/2017/04/express-sequelize/node-sequelize-sqlite-53958.png)

### 查询全部待办事项

修改 `server/controllers/todos.js`

```js
const Todo = require('../models').Todo

module.exports = {
  create(req, res) {
    ...
  },

  list(req, res) {
    return Todo
      .all()
      .then(todos => res.status(200).send(todos))
      .catch(error => res.status(400).send(error))
  }
}
```





修改路由文件 `server/routes/index.js`

```js
const todosController = require('../controllers').todos

module.exports = (app) =>  {
  ...
  app.post('/api/todos', todosController.create)
  app.get('/api/todos', todosController.list)
}
```





使用 `postman` 测试新路由
![](/2017/04/express-sequelize/node-sequelize-sqlite-bc8c9.png)

### 创建 Todo Items

- `server/controllers/todoitems.js`

```js
const TodoItem = require('../models').TodoItem

module.exports = {
  create(req, res) {
    return TodoItem
      .create({
        content: req.body.content,
        todoId: req.params.todoId,
      })
      .then(todoItem => res.status(201).send(todoItem))
      .catch(error => res.status(400).send(error))
  },
}
```




- `server/controllers/index.js`
```js
const todos = require('./todos')
const todoItems = require('./todoitems')

module.exports = {
  todos,
  todoItems,
}
```

- `server/routes/index.js`
```js
const todosController = require('../controllers').todos
const todoItemsController = require('../controllers').todoItems

module.exports = (app) =>  {
  ...
  app.get('/api/todos', todosController.list)
  app.post('/api/todos/:todoId/items', todoItemsController.create)
}
```

- postman 测试   ![](/2017/04/express-sequelize/node-sequelize-sqlite-6d615.png)

- 查询某个 todo 中的所有 todo-items `server/controllers/todos.js`
```js
const Todo = require('../models').Todo
const TodoItem = require('../models').TodoItem

module.exports = {
  create(req, res) {
    ...
  },

  list(req, res) {
    return Todo
      .findAll({
        include: [{
          model: TodoItem,
          as: 'todoItems'
        }]
      })
      .then(todos => res.status(200).send(todos))
      .catch(error => res.status(400).send(error))
  },
}
```


![](/2017/04/express-sequelize/node-sequelize-sqlite-4d4d2.png)

### 获取指定 todo

- `server/controllers/todos.js`
```js
const Todo = require('../models').Todo
const TodoItem = require('../models').TodoItem

module.exports = {
  create(req, res) {
    ...
  },

  list(req, res) {
    ...
  },

  retrieve(req, res) {
    return Todo
      .findById(req.params.todoId, {
        include: [{
          model: TodoItem,
          as: 'todoItems'
        }]
      })
      .then(todo => {
        if (!todo) {
          return res.status(400).send({
            message: 'Todo Not Found'
          })
        }
        return res.status(200).send(todo)
      })
      .catch(error => res.status(400).send(error))
  },
}
```

- 修改路由 `server/routes/index.js`
```js
const todosController = require('../controllers').todos
const todoItemsController = require('../controllers').todoItems

module.exports = (app) =>  {
  ...
  app.get('/api/todos/:todoId', todosController.retrieve)
  ...
```


![](/2017/04/express-sequelize/node-sequelize-sqlite-5084d.png)

### 更新一个 todo

- `server/controllers/todos.js`
```js
update(req, res) {
  return Todo
    .findById(req.params.todoId, {
      include: [{
        model: TodoItem,
        as: 'todoItems'
      }]
    })
    .then(todo => {
      if (!todo) {
        return res.status(400).send({
          message: 'Todo Not Found'
        })
      }
      return todo
        .update({
          title: req.body.title || todo.title,
        })
        .then(() => res.status(200).send(todo))
        .catch((error) => res.status(400).send(error))
    })
},
```

- `server/routes/index.js`
```js
...
app.put('/api/todos/:todoId', todosController.update)
...
```


![](/2017/04/express-sequelize/node-sequelize-sqlite-6835a.png)

### 删除 todo

- `server/controllers/todos.js`
```js
destroy(req, res) {
  return Todo
    .findById(req.params.todoId)
    .then(todo => {
      if (!todo) {
        return res.status(400).send({
          message: 'Todo Not Found'
        })
      }

      return todo
        .destroy()
        .then(() => res.status(204).send())
        .catch(error => res.status(400).send(error))
    })
    .catch(error => res.status(400).send(error))
}
```

- `server/routes/index.js`
```js
...
app.delete('/api/todos/:todoId', todosController.destroy)
...
```


![](/2017/04/express-sequelize/node-sequelize-sqlite-9d858.png)

## 更新和删除 todo items

- `server/controllers/todoitems.js`
```js
const TodoItem = require('../models').TodoItem

module.exports = {
  create(req, res) {
    return TodoItem
      .create({
        content: req.body.content,
        todoId: req.params.todoId,
      })
      .then(todoItem => res.status(201).send(todoItem))
      .catch(error => res.status(400).send(error))
  },

  update(req, res) {
    return TodoItem
      .find({
        where: {
          id: req.params.todoItemId,
        },
      })
      .then(todoItem => {
        if (!todoItem) {
          return res.status(400).send({message: 'TodoItem Not Found'})
        }

        return todoItem
          .update({
            content: req.body.content || todoItem.content,
            complete: req.body.complete || todoItem.complete
          })
          .then(updatedTodoItem => res.status(200).send(updatedTodoItem))
          .catch(error => res.status(400).send(error))
      })
      .catch(error => res.status(400).send(error));
  },

  destroy(req, res) {
    return TodoItem
      .find({
        where: {
          id: req.params.todoItemId,
        },
      })
      .then(todoItem => {
        if (!todoItem) {
          return res.status(400).send({message: 'TodoItem Not Found'})
        }
        return todoItem
          .destroy()
          .then(() => res.status(204).send())
          .catch(error => res.status(400).send(error))
      })
      .catch(error => res.status(400).send(error))
  }
}
```

- `server/routes/index.js`
```js
const todosController = require('../controllers').todos
const todoItemsController = require('../controllers').todoItems

module.exports = (app) =>  {
  app.get('/api', (req, res) => res.status(200).send({
    message: 'Welcome to the Todos API'
  }))
  app.post('/api/todos', todosController.create)
  app.get('/api/todos', todosController.list)
  app.get('/api/todos/:todoId', todosController.retrieve)
  app.put('/api/todos/:todoId', todosController.update)
  app.delete('/api/todos/:todoId', todosController.destroy)

  app.post('/api/todos/:todoId/items', todoItemsController.create)
  app.put('/api/todos/:todoId/items/:todoItemId', todoItemsController.update)
  app.delete('/api/todos/:todoId/items/:todoItemId', todoItemsController.destroy)

  app.all('/api/todos/:todoId/items', (req, res) => {
    res.status(405).send({
      message: 'Method Not Allowed'
    })
  })
}
```
