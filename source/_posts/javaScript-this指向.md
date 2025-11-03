---
title: javaScript - this的指向
tags: 
  - JavaScript
---
![banner](https://hidden-hours-1257704377.cos.ap-beijing.myqcloud.com/20240614153950872.jpg)
### 什么是this
  
*  就是一个上下文指针

##### 在全局作用域中

  ```JS
  this === window // true
  ```

##### 在普通函数中
  ```
  function add(a, b) {
    console.log('普通函数中的this', this)
    return a + b
  }
  ```
  this 指向 window

  <!-- let、const和var声明变量对于全局作用区别 -->

  ```JS
  var c = 8
    function add(a, b) {
      console.log('普通函数中的this', this)
      return a + b + this.c
    }
    console.log(add(3, 5))  // 16
  ```
  var 声明的全局变量是绑定到window上的

  ```JS
  let c = 8
    function add(a, b) {
      console.log('普通函数中的this', this)
      console.log('this.c', this.c)
      return a + b + this.c
    }
    console.log(add(3, 5))  // NaN 非数字
  ```
  let 和 const 声明的全局变量是不绑定到window对象上的

##### 函数作为方法的时候
  ```JS
  const obj = {
    name: '十七',
    coding() {
      console.log('this', this.name)
    }
  }
  obj.coding()  // 十七
  ```
  函数作为方法的时候，谁调用，this指向谁

  ```JS
  const obj = {
    name: '十七',
    coding() {
      console.log('this', this.name)
    }
  }
  let coding = obj.coding
  coding() // 啥也没有，因为此时的this指向window
  ```
  等价于
  ```js
  const obj = {
      name: '十七',
      coding: function() {
        console.log('this', this.name)
      }
    }
    let coding = function() {
      // 这里的this指向谁? window
      console.log('this', this === window)
    }
    coding()
  ```

  ------------------------------------

  ```JS
  let objA = {
    name: '小红',
    coding() {
      console.log('name', this.name)
    }
  }
  let objB = {
    name: '小明',
    coding: objA.coding
  }
  objB.coding()
  let coding = objB.coding
  coding()
  ```



  ```JS
  let objA = {
      name: '小红',
      coding() {
        console.log('name', this.name)
      }
    }
    let objB = {
      name: '小明',
      coding: objA.coding
    }
    console.log(objB.coding()) // 小明
    let coding = objB.coding
    console.log(coding()) // 啥也没有
  ```    
  等价于
  ```JS
  let objA = {
      name: '小红',
      coding: function() {
        console.log('name', this.name)
      }
    }
    let objB = {
      name: '小明',
      coding: function() {
        console.log('name', this.name)
      }
    }
    console.log(objB.coding()) // 小明
    let coding = function() {
      // 普通函数，this指向window
      console.log('name', this.name)
    }
    console.log(coding()) // 啥也没有
  ```
  ------------------------------------
  ```JS
  let name = '小明'
  let objA = {
    name: '小红',
    coding() {
      console.log('name', this.name)
    }
  }
  let objB = {
    name: '小明',
    coding: objA.coding
  }
  console.log(objB.coding())
  let coding = objB.coding
  console.log(coding())
  ```

-------------------------------------
```JS
let objA = {
      a: '',
      b: '',
      name: '小红',
      coding() {
        console.log('this', this.name)
        function printHtml() {
          console.log('this', this)
          this.a = 'HTML'
        }
        function printJs() {
          this.b = 'ECMAScript'
        }
        printHtml()
        printJs()
      }
    }
    objA.coding()
    console.log(objA.a, objA.b)
    console.log(a, b)
```

方法中的嵌套函数中的this指向的是window

--------------------------

```JS
let objA = {
      a: '',
      b: '',
      name: '小红',
      coding() {
        // 词法作用域，变量或函数在定义的时候作用域已经确定
        let self = this
        function printHtml() {
          console.log('this', this)
          self.a = 'HTML'
        }
        function printJs() {
          self.b = 'ECMAScript'
        }
        printHtml()
        printJs()
      }
    }
    objA.coding()
    console.log(objA.a, objA.b)
    console.log(a, b)
```
```JS
let objA = {
      a: '',
      b: '',
      name: '小红',
      coding() {
        console.log('this', this)
        const printHtml = () => {
          this.a = 'HTML'
        }
        const printJs = () => {
          this.b = 'ECMAScript'
        }
        printHtml()
        printJs()
      }
    }
    objA.coding()
    console.log(objA.a, objA.b)
```
```JS
const title = document.querySelector('#title')
    // 全局  window
    title.addEventListener('click', () => {
      console.log('this', this)
    })
```
作用域： 变量的作用范围
作用域链： 变量的查找范围

* 箭头函数中的this
  
  箭头函数没有自己的this，它是通过查找作用域的方式来确定自己的this的，

  也就是说： 它的this指向最近一层的非箭头函数的this

* 验证箭头函数的this指向
  ```JS
  // 全局 window
  let objA = {
    a: '',
    b: '',
    name: '小红',
    coding: () => {
      console.log('this', this)
      const printHtml = () => {
        this.a = 'HTML'
      }
      const printJs = () => {
        this.b = 'ECMAScript'
      }
      printHtml()
      printJs()
    }
  }
  objA.coding()
  console.log(objA.a, objA.b)
  ```

* setTimeout()和setInterval()中的this
  
  window.setTimeout()
  window.setInterva()

  ```JS
  setTimeout(function() {
      console.log('setTimeout', this)
    }, 1000)
    setInterval(function() {
      console.log('setInterval', this)
    }, 1000)
  ```
  其中的 this 都指向window

#### 如何改变this的指向
* 调用函数的时候进行修改
  ```
  call()和apply()
  ```
  ```JS
  function add(a, b) {
    console.log('thisC', this)
    return a + b + this.c
  }
  const obj = { a: 12, b: 18, c: 10 }
  console.log(add.call(obj, 3, 5))
  console.log(add.apply(obj, [3, 5]))
  ```
  两个方法实现的功能是一样的，只是参数不一样而已

  call 参数列表

  apply 参数数组

* 复制函数的时候进行修改
  bind()

  ```JS
  function add(a, b) {
      console.log('thisC', this)
      return a + b + this.c
    }
  const obj = { a: 12, b: 18, c: 10 }
  const sum = add.bind(obj, 10, 20)
  console.log(sum())
  ```
  ```JS
  const sum = add.bind(obj)
  console.log(sum(10, 20))
  ```

#### 构造函数
```JS
function Fn(name, age) {
  this.name = name
  this.age = age
  this.showName = function() {
    console.log('name', this.name)
  }
}
const fn = new Fn()
```
构造函数名的首字母要大写

通过new关键字来进行实例化

```JS
function Fn(name, age) {
  this.name = name
  this.age = age
  console.log('name', name)
  console.log('age', age)
  this.showName = function() {
    console.log('name', this.name)
  }
}
// 实例化并进行初始化赋值，func是一个实例化对象
const func = new Fn('小明', 1)
```
实例化对象可以调用构造函数里的所有属性和方法