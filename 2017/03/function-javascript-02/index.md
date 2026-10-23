# JavaScript 函数式编程（二）


# 高阶函数（Higher-Order Functions）

```js
const forEach = (array, fn) => {
    let i
    for (i = 0; i < array.length; i++) {
        fn(array[i])
    }
}

let array = [1, 2, 3]
forEach(array, (data) => console.log(data))
```



现在我们已经看到了如何创建和执行高阶函数。 一般来说，高阶函数通常用来抽象常见的问题。 换句话说，高阶函数只不过是定义抽象。

forEach本质上是遍历数组。 如何遍历JavaScript对象？遍历JavaScript对象具有以下步骤：

1. 迭代给定对象的所有键。
2. 确定键属于自己的对象。
3. 如果步骤2为真，则获取键的值。

```js
const forEachObject = (obj, fn) => {
    for (let prop in obj) {
        if (obj.hasOwnProperty(prop)) {
            fn(prop, obj[prop])
        }
    }
}

let object = {a: 1, b: 2}
forEachObject(object, (k, v) => console.log(k + ":" + v))
```



让我们创建一个名为`unless`的函数，这是一个简单的函数，它需要一个断言（应该是true或false）; 如果断言是假，则调用fn。

```js
const forEach = (array, fn) => {
    let i
    for (i = 0; i < array.length; i++) {
        fn(array[i])
    }
}

const unless = (predicate, fn) => {
     if (!predicate)
        fn()
}

forEach([1,2,3,4,5,6,7], (number) => {
    unless((number % 2), () => {
        console.log(number, ' is even')
    })
})
```



在上述情况下，我们从数组列表中获取偶数。 如果我们要从0到100获得偶数的列表怎么办？ 我们不能在这里使用（当然我们可以，如果我们有数组有[0,1,2 …..，100]）。 我们来看看另一个叫做times的高阶函数。

```js
const unless = (predicate, fn) => {
     if (!predicate)
        fn()
}

const times = (times, fn) => {
    for (let i = 0; i < times; i++)
        fn(i)
}

times(20, (n) => {
    unless(n % 2, () => console.log(n, 'is even'))
})
```





## 现实世界中的高阶函数

### every Function

通常作为JavaScript开发人员，我们需要检查内容数组是否是数字，自定义对象或其他内容。 我们通常写典型的循环方法来解决这些问题。 但是让我们把它们抽成一个叫做`every`函数。 每个函数都有两个参数：数组和函数。 它检查数组的所有元素是否被传递函数求值为true。ES6规格中， for..of循环可用于迭代数组元素

```js
const every = (arr, fn) => {
    let result = true;
    for (const value of arr) {
        result = result && fn(value)
    }

    return result
}

every([NaN, NaN, NaN], isNaN) // true
every([NaN, NaN, 4], isNaN) // false
```



every 一个典型的高阶函数，易于实现，同时它也非常有用！

## some Function

类似于`every`函数，我们也有一个称为`some`的函数。 `some`函数与`every`函数完全相反，如果数组中的任何一个元素对传递的函数返回true，那么`some`函数将返回true。 `some`函数也被称为`any`函数。 为了实现`some`函数，我们使用|| 而不是 &&

```js
const some = (arr, fn) => {
    let result = false;
    for (const value of arr) {
        result = result || fn(value)
    }

    return(result)
}

console.log(some([NaN, NaN, 4], isNaN)) // true
console.log(some([2, 3, 4], isNaN)) // false
```





## sort Function

`sort`是JavaScript的Array原型中提供的内置函数。

```js
var fruit = ['cherries', 'apples', 'bananas'];
console.log(fruit.sort())
// ["apples", "bananas", "cherries"]
```



排序函数是一个高阶函数，它接受一个函数作为其参数，这将有助于排序函数来决定排序逻辑。

```js
arr.sort([compareFunction])
```



这里的compareFunction是可选的。 如果没有提供compareFunction，则通过将元素转换为字符串并以Unicode代码顺序对比字符串进行排序。 为了在执行排序时将元素与我们自己的逻辑进行比较，我们需要设计我们自己compareFunction。 我们可以感觉到排序函数的设计如此灵活，以便它可以对JavaScript世界中的任何数据进行排序，只要我们通过一个compareFunction。 排序功能是灵活的，由于高阶功能的本质！

```js
const people = [
    {firstname: "aaFirstName", lastname: "cclastName"},
    {firstname: "ccFirstName", lastname: "aalastName"},
    {firstname:"bbFirstName", lastname:"bblastName"}
]

// compare firstname
people.sort((a, b) => {
    return (a.firstname < b.firstname) ? -1 : (a.firstname > b.firstname) ? 1 : 0
})

// compare lastname
people.sort((a,b) => {
    return (a.lastname < b.lastname) ? -1 : (a.lastname > b.lastname) ? 1 : 0
})
```



我们的compareFunction的算法，可以做得更好吗？ 我们可以将上述逻辑抽象成一个函数吗,而不是每次写比较函数？ 正如你在上面的例子中看到的，我们写了两个函数，用于比较firstName和lastName几乎相同的重复代码。 我们用高阶函数来解决这个问题。 现在我们要设计的函数将不会以函数为参数，而是返回一个函数。 （记住HOC也可以返回一个函数）。

```js
const people = [
    {firstname: "aaFirstName", lastname: "cclastName"},
    {firstname: "ccFirstName", lastname: "aalastName"},
    {firstname:"bbFirstName", lastname:"bblastName"}
]

const sortBy = (property) => {
    return (a, b) => {
        return (a[property] < b[property]) ? -1 : (a[property] > b[property]) ? 1 : 0
    }
}

people.sort(sortBy('lastname'))
```


---

> 作者: [张景林](https://zhangjinglin.github.io/about/)  
> URL: https://zhangjinglin.github.io/2017/03/function-javascript-02/  

