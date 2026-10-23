# Harp.js


# Harp.js?

[Harp.js](http://harpjs.com/)

> The static web server with built-in preprocessing.
> Harp serves Jade, Markdown, EJS, CoffeeScript, Sass, LESS and Stylus as HTML, CSS & JavaScript—no configuration necessary.

## How to use

1. install

```bash
npm i -g harp
```




1. create website

```plain
harp init hello-harp
cd hello-harp
harp server
```




1. compile

```plain
harp compile
```




1. metadata

- _data.json

```plain
"team": {
  "title": "Team page",
  "members": ["Rob", "Brock", "Jorge"]
}
```




- ejs or jade

```plain
h1= title
ul
  each member in members
    li= member
```


---

> 作者: [张景林](https://zhangjinglin.github.io/about/)  
> URL: https://zhangjinglin.github.io/2017/04/harpjs/  

