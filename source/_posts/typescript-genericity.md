---
title: typescript-genericity
date: 2021-01-20 15:47:20
tags: typeScript | share
---

## 泛型

希望一个函数或类支持多种数据类型 有很大的灵活性
不确定的数据类型，具体的使用类型在使用的时候才能确定

### 泛型 + 函数

举例：实现一个函数输入什么返回什么

```
// function log(val: string): string {
//     console.log(val);
//     return val;
// }
```

##### 方案一：函数重载

```
function log(val: string): string;
function log(val: string[]): string[];
function log(val: any): any {
    console.log(val);
    return val;
}
```

##### 方案二：联合类型

```
function log1(val: string | string[]): string | string[] {
    console.log(val);
    return val;
}
```

##### 方案三：使用泛型

保证了输入参数和返回值类型是一致的
如果使用 any 则不能保证输入参数和返回值类型是一致的

```
function log2<T>(val: T): T {
console.log(val);
return val;
}
log2<string[]>(['aaa', 'bbb']);
log2(['aaa', 'bbb']); // 推荐这么玩
```

不仅可以定义一个函数，也可以定义一个函数类型

```
type Logs = <T>(val: T) => T;
let logs: Logs = val => val;
logs(['123', '222']);
logs(123);

interface Log3 {
    <T>(val: T): T
}
// 这样接口的所有成员都受到泛型的约束
interface Log4<T> {
    (val: T): T
}
interface Log5<T = string> {
    (val: T): T
}
let log3: Log3 = val => val;
let log4: Log4<string> = val => val;
let log5: Log5 = val => val;
let log6: Log5<number> = val => val;
log3(123);
log4('123');
log5('123');
log6(555);
```

### 泛型 + 类

```
class LogClass1<T> {
    run(val: T) {
        console.log(val);
        return val;
    }
}
// 可以给泛型赋默认值
class LogClass2<T = number> {
    run(val: T) {
        console.log(val);
        return val;
    }
    // 泛型不能应用于类的静态成员 报错静态成员不能引用类类型参数
    // static xiaoli(val: T): string {
    //     return val;
    // }
}

let logClass1 = new LogClass1<number>();
let logClass2 = new LogClass1();
let logClass3 = new LogClass2();
let logClass4 = new LogClass2<boolean>();
logClass1.run(123);
logClass2.run('123');
logClass3.run(123);
logClass4.run(false);
```

### 泛型的类型约束

```
interface Length {
    length: number;
}
function getLength<T extends Length>(val: T): T {
    console.log(val, val.length);
    return val;
}
// 输入的 val 必须有 length 属性
getLength('123');
// 报错
getLength(123);
```

### 总结 使用泛型的好处

函数和类能轻松支持多种类型，增强程序的扩展性
不必写多条函数重载、冗长的联合类型声明，增强代码的可读性
灵活控制类型之间的约束
