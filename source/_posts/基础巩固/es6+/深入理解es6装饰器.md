---
title: 深入理解es6装饰器
date: 2020-06-07 16:25:27
tags:
- es6+
- 面向对象
- class
categories:
- 基础巩固
- es6+
banner_img: https://pic4.zhimg.com/80/v2-84a47569a1117310a2fb58086a8ed2b3_1440w.jpg
index_img: https://pic4.zhimg.com/80/v2-84a47569a1117310a2fb58086a8ed2b3_1440w.jpg
---

> 本文需要提前掌握es5中构造函数相关知识点：[我是知识点](https://juejin.im/post/5ec6464a518825434062f38e)

### class基本语法
#### es6中class定义的类和es5中的构造函数有什么区别?
```
// es6中的类形如
class Point {
  constructor(x, y) {
    this.x = x;
    this.y = y;
  }

  toString() {
    return '(' + this.x + ', ' + this.y + ')';
  }
}
```
- 类可以看作一个**语法糖**，是构造函数的另外一种写法
- 类只是让对象原型的写法更加清晰，更像**面向对象编程**的语法
- **constructor方法**，就是构造方法（es5中的构造函数），this关键字代表实例对象
- 类的所有方法都定义在**类的prototype属性**上面
- 类的内部所有定义的方法，都是**不可枚举**的
- **类必须使用new调用**，否则会报错，而普通构造函数不用new也可以执行

#### 类的constructor方法是什么？
- 是类的默认方法，new命令生成对象实例时自动调用该方法
    > 一个类必须有constructor方法,如果没有显式定义，一个空的constructor方法会被默认添加
- 默认返回实例对象，可以制定返回另一个对象

#### 类的实例有什么需要注意的？
- 实例的属性除非显示定义在其本身，否则都是定义在原型上
- 类的所有实例共享一个原型对象

#### 类中取值函数（getter）和存值函数（setter）的使用
- 与 ES5 一样，在“类”的内部可以使用get和set关键字，对某个属性设置存值函数和取值函数，拦截该属性的存取行为
- 存值函数和取值函数是设置在属性的 Descriptor 对象上的
    ```
    get prop() {
     return 'getter';
    }
    set prop(value) {
     console.log('setter: '+value);
    }
    ```

#### 使用类的时候需要注意的点
1. 类和模块的内部本来就是严格模式，所以不需要用use strict指定运行模式
2. 不存在变量提升
3. 类只是对普通构造函数的一层包裹，所以函数的许多特性都被class继承
4. Generator方法。如果在某个方法前面加上星号（*）,就表示该方法是一个Generator函数
5. this的指向问题
    - 类的方法内部如果含有this,默认指向类的实例，但因为方法中的this的确是指向实例，但是如果将该方法单独提取出来的话，this会指向该方法运行时所在的函数（class内部是严格模式，所以this指向undefined）
        ```
            class Logger {
              printName(name = "there") {
                this.print(`Hello ${name}`);
              }
            }
            const logger = new Logger();
            const { printName } = logger;
            logger.printName(); //ok
            printName(); //error
        ```
    - 解决办法
        1. 构造方法中绑定this
        > constructor() {this.printName = this.printName.bind(this);}

        2. 使用箭头函数
        ```
        class Obj {
          constructor() {
            this.getThis = () => this;
          }
        }

        const myObj = new Obj();
        myObj.getThis() === myObj // true
        ```
        3. 使用proxy，获取方法的时候，自动绑定this

#### 静态方法
- **定义**：在一个方法前，加上static关键字，就表示该方法不会被实例继承，而是**直接通过类**来调用，这就称为“静态方法”。
- 如果静态方法包含this,这个this指向的是类，而不是实例
- 父类的静态方法，可以被子类继承，子类可以调用这个方法
- 静态方法也可以从super对象上调用

#### 实例属性的写法
- 除了可以定义在constructor的this上面，也可以定义在类的最顶层
    ```
    class IncreasingCounter {
      constructor() {
        this._count = 0;
      }
      /** _count=0 和上面这种方法带来的效果一样
       ** 注释掉的这种写法的好处是，所有的实例对象的属性都定义在类的头部，
       ** 看上去比较整齐，一眼就能看出这个类有哪些实例属性
       */
      get value() {
        console.log('Getting the current value!');
        return this._count;
      }
      increment() {
        this._count++;
      }
    }
    ```
#### 静态属性
- **定义**：指的是class本身的属性，而不是定义在实例对象的（this）上的属性
- 现有一个提案提供了类的静态属性，写法是在实例属性的前面，加上static关键字
    ```
    // 老写法
    class Foo {
      // ...
    }
    Foo.prop = 1;
    // 新写法-es7
    class Foo {
      static prop = 1;
    }
    ```
#### 私有方法和私有属性
> 私有方法和私有属性，是只能在类的内部访问的方法和属性，外部不能访问。这是常见需求，有利于代码的封装，但 ES6 不提供，只能通过变通方法模拟实现。

- 私有属性语法
    - 声明私有属性直接使用#开头作为属性名，声明不能在私有属性前加this，但是使用时必须使用this调用
    - 不能在子类调用父类的私有属性，但是可以在子类定义与父类同名的私有属性，并且互不干扰，但最好不要这样做
    - 不能在constructor中定义私有属性和方法
- es7实现，通过#来声明
    ```
    class Foo{
        #x = 10;
        constructor(x = 0){
            this.num = this.#x; //公有属性可以获取私有属性的值
        }
        set x(value){
            this.#x +=value; //这里可以理解为公有方法内部给私有属性写入值
        }
        get x(){
            return this.#x; //这里可以理解为公有方法内部读取私有属性的值
        }
        sum(){
            console.log(this.num + this.#x); //这里可以理解为公有方法读取私有属性的值
        }
    }
    class Fun extends Foo{
        constructor(){
            super()
        }
        y(){
            // 子类方法不能直接使用父类的私有属性，babel插件无法编译
            //  return this.#x;
        }
    }
    ```
- 使用**私有属性**需要注意的点
    - **实例对象可以调用类定义的公开方法获取和修改私有属性，可以调用类的公开方法执行私有方法。实例对象一定要通过类的公有方法才能使用私有属性和方法**
    - 静态属性的this指向类，私有属性的this指向实例对象

### class的继承
#### 怎么实现class的继承？
```
class Point {
  constructor(x, y) {
    this.x = x;
    this.y = y;
  }
}
class ColorPoint extends Point {
  constructor(x, y, color) {
    this.color = color; // ReferenceError
    super(x, y);
    this.color = color; // 正确
  }
}
```
- Class 可以通过extends关键字实现继承，这比 ES5 的通过修改原型链实现继承，要清晰和方便很多
- super关键字，它在这里表示父类的构造函数，用来新建父类的this对象
    - super作为函数调用时，代表父类的构造函数，es6要求子类的构造函数必须执行一次super函数（super执行时指向子类的构造函数）
    - super作为对象时，在普通方法中，指向父类的原型对象；在静态方法中，指向父类
        > 由于super指向父类的原型对象，所以定义在父类实例上的方法或属性，是无法通过super调用的
- 子类必须在constructor方法中调用super方法，否则新建实例时会报错
    > 这是因为子类自己的this对象，必须先通过父类的构造函数完成塑造，得到与父类同样的实例属性和方法，然后再对其进行加工，加上子类自己的实例属性和方法。如果不调用super方法，子类就得不到this对象。
- 不管有没有显式定义，任何一个子类都有constructor方法。如果子类没有定义constructor方法，这个方法会被默认添加
- 父类的静态方法，也会被子类继承


### 什么是装饰器？
- 是一种函数，写成@ + 函数名。可以放在类和类的方法的定义前面
#### 类装饰器
- 是一个对类进行处理的函数
    ```
    @decorator
    class A {}
    // 等同于
    class A {}
    A = decorator(A) || A;
    ```
- 装饰器函数的第一个参数就是所要装饰的目标类
    - 为类添加一个静态属性
        ```
        function testable(target) {
          target.prototype.isTestable = true;
        }
        @testable
        class MyTestableClass {}
        let obj = new MyTestableClass();
        obj.isTestable // true
        ```

- 如果一个参数不够用，可以在装饰器外面再封装一层函数，装饰器可以接受参数，这就等于可以修改装饰器的行为
    ```
    function testable(isTestable) {
      return function(target) {
        target.isTestable = isTestable;
      };
    }
    @testable(true)
    class MyTestableClass {}
    MyTestableClass.isTestable; // true
    ```
> 装饰器对类的行为的改变，是代码编译时发生的，而不是在运行时。装饰器的本质就是编译时执行的函数
- 在实例上添加属性，通过目标类的prototype对象操作

    ```
    function testable(target) {
      target.prototype.isTestable = true;
    }
    @testable
    class MyTestableClass {}
    let obj = new MyTestableClass();
    obj.isTestable // true
    ```


#### 方法装饰器
```
class Math {
  @log
  add(a, b) {
    return a + b;
  }
}
function log(target, name, descriptor) {
  var oldValue = descriptor.value;
  descriptor.value = function() {
    console.log(`Calling ${name} with`, arguments);
    return oldValue.apply(this, arguments);
  };
  return descriptor;
}
const math = new Math();
// passed parameters should get logged now
math.add(2, 4);
```
- 一共接受三个参数
    - target：类的原型对象
    - name：所要装饰的属性名
    - descriptor：该属性的描述对象
- 同一个方法有多个装饰器，会像剥洋葱一样，先从外到内进入，然后由内向外执行