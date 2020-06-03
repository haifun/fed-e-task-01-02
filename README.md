##### 1.描述引用计数的工作原理和优缺点？

答：核心思想是设置对象的一个引用数，判断当前引用数是否为0，引用数为0时，立即回收；引用关系改变时，修改引用数字

有点：发现垃圾时，会立即回收，可以最大限度的减少程序暂定，保证系统稳定性

缺点：无法回收循环引用的对象，
需要时刻监视对象的引用，时间开销比较大

##### 2.描述标记整理算法的工作流程。

核心思想：

分标记和清除两个阶段完成

遍历所有对象找标记活动对象

清理阶段，会先整理对象位置，然后进行清理并回收

##### 3.描述V8中新生代存储区垃圾回收的流程。

基本过程：
回收过程采用复制算法+标记整理

新生代内存区分为二个等大小空间

使用空间为From, 空闲空间为To

活动对象存储于From空间

标记整理后将活动对象拷贝至To

From与To交换空间完成释放

##### 4.描述增量标记算法在何时使用，及工作原理。

在老生代对象回收中，采用增量标记进行效率优化

工作原理：

遍历对象进行标记

分段（交替）进行增量标记，跟程序交替执行

完成清除


### 编码题

##### 1.基于以下代码完成下面四个练习


```
const fp = require("lodash/fp");

const cars = [
  { name: "Ferrari FF", horsepower: 660, dollar_value: 700000, in_stock: true },
  {
    name: "Spyker C12 Zagato",
    horsepower: 650,
    dollar_value: 648000,
    in_stock: false,
  },
  {
    name: "Jaguar XKR-S",
    horsepower: 550,
    dollar_value: 132000,
    in_stock: false,
  },
  { name: "Audi R8", horsepower: 525, dollar_value: 114200, in_stock: false },
  {
    name: "Aston Martin One-77",
    horsepower: 750,
    dollar_value: 1850000,
    in_stock: true,
  },
  {
    name: "Pagani Huayra",
    horsepower: 700,
    dollar_value: 1300000,
    in_stock: false,
  },
];
```

练习1：
使用函数组合fp.flowRight()重新实现下面这个函数

```
let isLastInstork = funtion(cars) {
    // 获取最后一条数据
    let last_car = fp.last(cars)
    // 获取最后一条数据的in_stock属性值
    return fp.prop('in_stock',last_car)
}
```
答案：

```
fp.flowRight(fp.prop('in_stock'), fp.last)
```


练习2：使用fp.flowRight()、fp.prop()和fp.first()获取第一个car的name

答案：
```
fp.flowRight(fp.prop('name'), fp.first)
```



练习3：

使用帮助函数 _average重构averageDollarValue,使用函数组合的方式实现

```
let _average = function(xs){
    return fp.reduce(fp.add,0,xs)/xs.length
}

let averageDollarValue = function(cars) {
    let dollar_values = fp.map(function(car){
        return car.dollar_value
    }, cars)
    return _average(dollar_values)
}

```
答案：

```
fp.flowRight(_average, fp.prop('dollar_value'))
```

练习4：使用flowRight 写一个sanitizeNames()函数，返回一个下划线链接的小写字符串，把数组中的name转换为这种形式：例如：sanitizeNames(["Hello World"])=>["hello_world"]


```
let _underscore = fp.replace(/\W+/g, '_')

```
答案：
```
const sanitizeNames = arr => fp.map(fp.flowRight(_underscore, fp.toLower),arr)
```


### 代码题2

```
// support.js
class Container {
  
  static of(value) {
    return new Container(value)
  }
  constructor(value) {
    this._value = value
  }
  map(fn) {
    return Container.of(fn(this._value))
  }
}

class Maybe {
  static of(x) {
    return new Maybe(x)
  }
  isNothing() {
    return this._value === null || this._value === undefined
  }
  constructor(x) {
    this._value = x
  }
  map(fn) {
    return this.isNothing() ? this : Maybe.of(fn(this._value))
  }
}

module.exports = {
  Maybe,
  Container,
}
```

练习1： 使用 fp.add(x, y) 和 fp.map(f, x) 创建一个能让 functor 里的值增加的函数 ex1
```
const fp = require('lodash/fp')
const { Maybe, Container } = require('./support')

let maybe = Maybe.of([5, 6, 1])
// let ex1 =  // ... code
```
答案：
```
let ex1 = num = maybe.map(arr => fp.map(fp.add(num),arr))
```


练习2：实现一个函数 ex2 ，能够使用 fp.first 获取列表的第一个元素

```
const fp = require('lodash/fp')
const { Maybe, Container } = require('./support')

let xs = Container.of(['do', 'ray', 'me', 'fa', 'so', 'la', 'ti', 'do'])
// let ex2 = //... code
```
答案：
```
let ex2 = () => xs.map(arr => fp.first(arr));
```

练习3： 实现一个函数 ex3 ，使用 safeProp 和 fp.first 找到 user 的名字的首字母

```
const fp = require('lodash/fp')
const { Maybe, Container } = require('./support')

let safeProp = fp.cuury(function (x, o) {
  return Maybe.of(o[x])
})
let user = { id: 2, name: 'Albert' }
// let ex3 = //...  code
```
答案：
```
let ex3 = user => safeProp('name', user).map(fp.first)
```

练习4：使用 Maybe 重写 ex4 ，不要有 if 语句

```
const fp = require('lodash/fp')
const { Maybe, Container } = require('./support')

let ex4 = function (n) {
  if (n) {
    return parseInt(n)
  }
}
```

答案：
```
let ex4 = n => Maybe.of(n).map(parseInt);
```