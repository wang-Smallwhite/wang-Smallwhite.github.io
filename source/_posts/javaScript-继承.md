---
title: JavaScript 实现继承的方法
code_highlight: true
tags: 
  - JavaScript
---

![banner](https://hidden-hours-1257704377.cos.ap-beijing.myqcloud.com/20240614170248378.webp)

### 原型链继承  

  * 关键：子类构造函数的原型为父类构造函数的实例对象  
  * 缺点：  
    * 子类构造函数无法向父类构造函数传参  
    * 所有的子类实例共享着一个原型对象，一旦原型对象的属性变化，所有子类都会受影响  
    * 如果给子类原型上添加方法，必须放在 Son.prototype = new Father() 语句之后，否则添加的方法报错不存在  
    
```js
  function Father(name) {
    this.name = name;
  }

  Father.prototype.showName = function () {
    console.log(this.name);
  };

  function Son(age = 10) {
    this.age = age;
  }
  // 原型链继承， 将子函数的原型绑定到父函数的实例上，子函数可以通过原型链查找到父函数的原型，实现继承
  Son.prototype = new Father();
  // 将 Son 原型的构造函数指回 Son， 否则 Son 实例的 constructor 会 指向Father
  Son.prototype.constructor = Son;
  Son.prototype.showAge = function () {
    console.log(this.age);
  };
  let father = new Father("father");
  let son = new Son(22, "name"); // 无法向父构造函数里传参
  // 子类构造函数的实例继承了父类构造函数原型的属性，所以可以访问到父类构造函数原型里的showName 方法
  // 子类构造函数的实例继承了父类构造函数的属性，但是无法传参赋值，所以是 this.name 是 undefined
  // 无法传参 ： 子类的构造函数会继承父类的属性，但是这些属性是绑定到子类的原型对象上的，而不是实际的实例属性
  console.log(father);
  father.showName(); // father
  console.log(son);
  son.showAge(); // 22
  son.showName(); // undefined
```

### 盗用构造函数继承  

  * 关键：用 call 和 apply 方法，在子类构造函数中调用父类构造函数  
  * 缺点：  
    * 只继承了父类构造函数属性，没有继承父类原型的属性  
    * 无法实现函数复用，如果父类构造函数里面有一个方法，会导致每个子类上面都有相同的方法  

``` JS
  function Father(name) {
    this.name = name;
  }
  Father.prototype.showName = function () {
    console.log(this.name);
  };
  function Son(name, age) {
    Father.call(this, name); // 在Son中借用了Father函数,只继承了父类构造函数的属性，没有继承父类原型的属性。 // 相当于 this.name = name
    this.age = age;
  }
  let s = new Son("十七", 20); // 可以给父构造函数传参
  console.log(s);
  console.log(s.name); // '十七'
  console.log(s.showName); // undefined
```
### 组合继承  

  * 关键：原型链继承 + 借用构造函数继承  
  * 缺点：使用组合继承时，父类构造函数会被调用两次，子类实例对象与子类的原型上会有相同的方法与属性，浪费内存。  

``` JS
  function Father(name) {
    this.name = name;
    this.say = function () {
      console.log("SAY");
    };
  }
  Father.prototype.showName = function () {
    console.log("name", this.name);
  };

  function Son(name, age) {
    Father.call(this, name); // 盗用构造函数继承
    this.age = age;
  }

  // 原型链继承
  Son.prototype = new Father(); // Son 实例的原型上，会有同样的属性，父类构造函数相当于调用了两次
  // 将Son 原型的构造函数指回Son， 否则 Son 实例的 constructor 会指向 Father;
  Son.prototype.constructor = Son;
  Son.prototype.showAge = function () {
    console.log(this.age);
  };
  let son = new Son("十七", 22);
  console.log(son);
  son.showName(); // 十七
  son.showAge(); // 22
  son.say();
```
### 原型式继承  

  * 关键：创建一个函数，将要继承的对象通过参数传递给这个函数，最终返回一个对象，他的隐式原型指向传入的对象。（Object.create()方法的底层就是原型式继承）  
  * 缺点：只能继承父类函数原型对象上的属性和方法，无法给父类构造函数传参  

``` JS
  function createObj(obj) {
    function F() {} // 申明一个构造函数
    F.prototype = obj; // 将这个构造函数的原型指向传入对象
    F.prototype.constructor = F; //construct 属性指向 子类构造函数
    return new F();
  }

  function Father() {
    this.name = "十七";
  }
  Father.prototype.showName = function () {
    console.log(this.name, "123");
  };
  Father.prototype.age = "123";
  const son = createObj(Father.prototype);
  console.log(son);
  console.log(son.age);
  son.showName(); // undefined 继承了原型上的方法， 但是没有继承构造函数里的name 属性
```

### 寄生式继承  

  * 关键：在原型式继承的函数里，给继承的对象上添加属性和方法，增强这个对象  
  * 缺点：只能继承父类函数原型对象上的属性和方法，无法给父类构造函数传参  

``` JS
  function createObj(obj) {
    function F() {}
    F.prototype = obj;
    F.prototype.construct = F;
    F.prototype.age = 20; // 给F函数的原型添加属性和方法,增强对象
    F.prototype.showAge = function () {
      console.log(this.age);
    };
    return new F();
  }
  function Father() {
    this.name = "十七";
  }
  Father.prototype.showName = function () {
    console.log(this.name);
  };
  const son = createObj(Father.prototype);
  son.showName(); // undefined
  son.showAge(); // 20
```

### 寄生组合继承  

  * 关键： 原型式继承 + 构造函数继承  
  * JS 最佳的继承方式， 只调用了一次父类构造函数  

``` JS
  function Father(name) {
    this.name = name;
    this.say = function () {
      console.log("saysay");
    };
  }
  Father.prototype.showName = function () {
    console.log(this.name);
  };
  function Son(name, age) {
    Father.call(this, name);
    this.age = age;
  }
  Son.prototype = Object.create(Father.prototype); // Object.create 方法返回一个对象，它的隐式原型指向传入的对象。
  Son.prototype.constructor = Son;
  const son = new Son("十七", 22);
  console.log(son);
  // console.log(son.prototype.name); // 原型上已经没有 name 属性了，所以这里会报错
```

### 混入继承  

  * 关键：利用 Object.assign 的方法多个父类函数的原型拷贝给子类原型  

``` JS
  function Father(name) {
    this.name = name;
  }
  Father.prototype.showName = function () {
    console.log(this.name);
  };

  function Mather(color) {
    this.color = color;
  }
  Mather.prototype.showColor = function () {
    console.log(this.color);
  };

  function Son(name, color, age) {
    // 调用两个父类函数
    Father.call(this, name);
    Mather.call(this, color);
    this.age = age;
  }
  // Son.prototype = Object.create(Father.prototype);
  Object.assign(Son.prototype, Mather.prototype, Father.prototype); // 将Mather父类函数的原型拷贝给子类函数
  const son = new Son("十七", "red", 20);
  console.log(son);
  son.showColor(); // red
```

### class 类继承  

  * 关键：class里的extends和super关键字，继承效果与寄生组合继承一样  

``` JS
  class Father {
    constructor(name) {
      this.name = name;
    }
    showName() {
      console.log(this.name);
    }
  }
  class Son extends Father {
    // 子类通过extends继承父类
    constructor(name, age) {
      super(name); // 调用父类里的constructor函数,等同于Father.call(this,name)
      this.age = age;
    }
    showAge() {
      console.log(this.age);
    }
  }
  const son = new Son("十七", 20);
  son.showName(); // '十七'
  son.showAge(); // 20
```
