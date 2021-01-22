---
title: typeScript-class
date: 2021-01-20 14:32:11
tags: typeScript | share
---

与 es6 相同：
类成员的属性都是实例属性，而不是原型属性
类成员的方法都是实例方法

```
class Dog {
    constructor(name: string) {
        this.name = name;
    }
    name: string;

    run() {  }
}
console.log(Dog.prototype);
let dog = new Dog('dog1');
console.log(dog);
```

与 es6 不同：
实例的属性必须有初始值或被构造函数初始化

```
// 类的继承
class DogSon extends Dog {
    constructor(name: string, color: string) {
        super(name);
        this.color = color;
    }

    color: string;
}
```

### 类的成员修饰属性

| 类的成员  | 类的成员   | 详细解释                                                       |
| --------- | ---------- | -------------------------------------------------------------- |
| public    | 共有成员   | 类的所有属性默认都是 public 对所有人可见                       |
| private   | 私有成员   | 类的私有成员只能被类的本身调用，不能被实例和子类调用           |
| protected | 受保护成员 | 受保护成员只能在类本身和子类调用，不能被实例化调用             |
| readonly  | 只读属性   | 只能读取，不能修改                                             |
| static    | 静态成员   | 静态成员只能通过类名来调用，而不能通过实例化来调用，可以被继承 |

如果给 constructor 加上 private 修饰符，则类不能被实例化和继承
如果给 constructor 加上 protected 修饰符，则类不能被实例化，只能被继承，即基类
构造函数的参数也可以添加修饰符，将参数自动变成实例的属性，

```
class Cat {
    constructor(name: string, public color: string) {
        this.name = name;
    }
    public name: string;

    private pri() {
        console.log('私有哦');
    }

    protected pro() {
        console.log('受保护哦');
    }

    readonly times: string = '100';

    static food: string = 'mouse';
}
```

=================================================================

### 抽象类 abstract

抽象类 不能被实例化 只能被继承
抽象类中可以使用 abstract 修饰方法不指定固定实现，即抽象方法
抽象方法可以明确子类对此方法有其他的实现，没必要在父类中实现了
可以抽离出一些事务的共性，有利于代码复用和拓展，实现多态

```
abstract class Animal {
    constructor() {  }

    eat() { console.log('eat'); }

    abstract sleep(): void;
}

class Duck extends Animal{
    constructor(public name: string) {
        super();
    }

    do() {  }

    sleep() {
        console.log('我是Duck抽象方法在子类的实现');
    }
}

let duck = new Duck('鸭子');
console.log(duck);
duck.eat();

// ts的多态
class Pig extends Animal{
    constructor(public name: string) {
        super();
    }

    sleep() {
        console.log('我是Pig抽象方法在子类的实现');
    }
}
let pig = new Pig('猪');
console.log(pig);

let animals: Animal[] = [duck, pig];
console.log(animals);
animals.forEach(item => {
    item.sleep();
})
```

#### this 的使用 方便链式调用

this 的多态体现在父子类型的 this 可以相互再次调用，从而实现父子类调用的连贯性

```
class WorkFlow {
    constructor() { }

    fn1() { return this; }

    fn2() { return this; }
}

let workflow = new WorkFlow();
workflow.fn1().fn2();

class MyFlow extends WorkFlow {
    next() { return this; }
}

let myflow = new MyFlow();
myflow.fn1().next().fn2().next();
```
