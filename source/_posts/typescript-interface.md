---
title: typescript-interface
date: 2021-01-22 14:48:18
tags: typeScript | share
---

## 接口

TypeScript 的核心原则之一是对值所具有的结构进行类型检查。
它有时被称做“鸭式辨型法”或“结构性子类型化”。
在 TypeScript 里，接口的作用就是为这些类型命名和为你的代码或第三方代码定义契约。

```
interface funObj {
    readonly name: String
    id: Number
}
```

接口使用枚举类型

```
enum enms_001 {
    red,
    green: "green"
}

interface TimeObj {
    col0: enms_001
    col1: enms_001.red
}

const timeObj: TimeObj = {
  col0: enms_001.green,
  col1: enms_001.red,
};

```

#### readonly vs const

最简单判断该用 readonly 还是 const 的方法是看要把它做为变量使用还是做为一个属性。
做为变量使用的话用 const，若做为属性则使用 readonly。

```
let arr_001: Array<number | string> = [1, 2, 3];
let arr_002: ReadonlyArray<number | string> = arr_001; // 数组arr_002只能读 或者执行不改变原数组的方法
let arr_003: Array<number | string> = [1, 2];
arr_003 = arr_001;
arr_002 = arr_003;
// arr_001 = arr_002; // 活值不能接受死值的赋值 报错
arr_001 = arr_002 as Array<number>; // 但是你可以用类型断言重写

```

#### 可选属性

```
interface SquareConfig {
  color?: string;
  width?: number;
}
function createSquare(config: SquareConfig): { color: string; area: number } {
  // ...
  console.log(config);
  return {
    color: "111",
    area: 100,
  };
}
// 对象字面量会被特殊对待而且会经过 额外属性检查，当将它们赋值给变量或作为参数传递的时候。
// 如果一个对象字面量存在任何“目标类型”不包含的属性时，你会得到一个错误。
let mySquare = createSquare({ color: "red", width: 100, time: 100 }); // Error

// 这样可以通过
const createSquareParams = { color: "red", width: 100, time: 100 };
let mySquare = createSquare(createSquareParams);

// 绕开这些检查非常简单。 最简便的方法是使用类型断言：
let mySquare = createSquare({
  color: "red",
  width: 100,
  time: 100,
} as SquareConfig);
```

#### 更多属性

```
interface baseConfig {
  id: number;
  name: string;
  [propName: string]: any;
}
const fn000 = (params: baseConfig): boolean => {
  console.log(params);
  return true;
};
fn000({ id: 1, name: "name", price: 1000 });
```

---

## 函数接口

```
interface SearchFunc {
  (source: string, subString: number): boolean;
}
let mySearch: SearchFunc = (sou, subString) => {
  let result = sou == subString.toString();
  return result;
};
mySearch("2", 1); // false
```

#### 定义函数的方式

```
// 函数接口的方案一
let numberFn: (x: number, y: number) => number;
numberFn = (x, y) => x + y;

// 函数接口的方案二
interface AddFn {
    (x: number, y: number): number;
}
let addFn: AddFn = (x, y) => x + y;

// 函数接口的方案三 类型别名
type Jian = (x: number, y: number) => number;
let jian: Jian = (x, y) => x - y;

// 方案四 函数定义
function cheng1 (x: number, y: number): number {
    return x * y;
}
// ts函数类型推断省去返回值的类型
function cheng2 (x: number, y: number) {
    return x * y;
}
```

#### 混合类型接口

即可以定义函数，也可以像对象一样，拥有属性和方法

```
interface Lib {
    (): void;
    version: string;
    doSomething(): void;
}


function getLib() {
    let lib: Lib = (() => {}) as Lib;
    lib.version = '1.0.0';
    lib.doSomething = () => {};

    return lib;
}

let lib1 = getLib();
lib1();
lib1.doSomething();
lib1.version = '1.0.1';

let lib2 = getLib();
lib2();
lib2.doSomething();
lib2.version = '1.0.2';
```

#### 函数的可选参数 必选参数必须在可选参数之前

```
function add5(x: number, y?: number) {
    return y ? x + y : x;
}
// console.log(add5(1));
```

带默认值 在必选参数前，默认值是不能省略的，必须明确传入 undefined 来获取默认值
必选参数之后的默认值可以不传

```
function add6(x: number, y = 2, z: number, t = 1) {
    return x + y + z + t;
}
// console.log(add6(5, undefined, 3));
```

参数个数不确定 使用剩余参数

```
function add7(x: number, ...rest: Array<number>) {
    return x + rest.reduce((pre, cur) => pre + cur);
}
// console.log(add7(1, 2, 3, 4, 5, 6));
```

#### 函数重载

静态语言类型（c++，java）两个函数名称相同，但是参数个数或类型不同，那么就函数重载，不需要为了相似功能的函数选用不同的函数名称 增加了函数的可读性
ts 函数重载的时候会去查询定义的列表（如下），尝试第一个定义，如果匹配的话，就使用匹配到的函数，不匹配继续向下查找，所以最容易匹配到的写在前边

```
function add8(...rest: number[]): number;
function add8(...rest: string[]): string;
function add8(...rest: any[]): any {
    let first = rest[0];
    if (typeof first == 'string') {
        return rest.join('');
    } else if (typeof first == 'number') {
        return rest.reduce((pre, cur) => pre + cur);
    }
}
console.log(add8(1, 2, 3, 4));
console.log(add8('A', 'B', 'C', 'D'));
```

---

## 对象接口

```
interface List {
    readonly id: number; // 只读属性不允许修改
    name: string;
    age?: number; // 可选属性
}

interface Result {
    // data: Array<List>
    data: List[];
}

function render(result: Result) {
    result.data.map(item => {
        console.log(item.id, item.name);
        if (item.age) {
            console.log(item.age);
            // item.id++; // 只读属性不允许修改
        }
    })
}
```

##### 这样调用 sex 属性会被 ts 检测，sex 不在 List 预定范围中

```
render({
    data: [
        { id: 1, name: '小李', sex: '男' },
        { id: 2, name: '张三' },
    ]
});
```

##### 解决方案一

```
let result = {
    data: [
        { id: 1, name: '小李', sex: '男' },
        { id: 2, name: '张三', age: 200 },
    ]
}
render(result);
```

##### 解决方案二 都是类型断言的写法

建议使用这个 使用类型断言 告知编译器对象类型是 Result 绕过类型检查

```
render({
    data: [
        { id: 1, name: '小李', sex: '男' },
        { id: 2, name: '张三' },
    ]
} as Result);
```

在 React 中会产生歧义
使用类型断言 告知编译器对象类型是 Result 绕过类型检查

```
render(<Result> {
    data: [
        { id: 1, name: '小李', sex: '男' },
        { id: 2, name: '张三' },
    ]
});
```

##### 解决方案三 使用字符串索引签名 修改定义的 List 接口

用任意的字符串去索引 List 可以得到任意的结果

```
interface List {
    id: number;
    name: string;
    [more: string]: any;
}
```

##### 接口混用

```
interface StringArray {
    [index: number]: string;
}

let strArr: StringArray = ['A', 'B', 'C'];
```

数字和字符串索引混用接口
数字索引签名的返回值一定要是字符串索引签名的返回值的子类型（保持类型的兼容性） 因为 js 会进行类型转换 number 转化为 string

```
interface Names {
    [x: string]: string;
    [y: number]: string;
}
```

数字索引签名的返回值一定要是字符串索引签名的返回值的子类型（保持类型的兼容性） 因为 js 会进行类型转换 number 转化为 string

```
interface Namess {
    [x: string]: string | number;
    [y: number]: number;
}
```

## 类的接口

```
interface Human {
    // new(name: string): void;
    name: string;
    eat(): void;
}

class Asian implements Human {
    constructor(public name: string) {

    }

    eat() { }

    sleep() { }
}
```

类类型接口 类实现接口的时候，必须实现所有的成员
接口只能约束类的功能成员 不能约束构造函数

```
const asian = new Asian('xiaoli');
console.log(asian);
```

接口继承 一个接口可以继承多个接口
可以抽离出重用的接口，也可以将多个接口合并成一个接口

```
interface Man extends Human {
    run(): void;
}
interface Child {
    cry(): void;
}
interface Boy extends Man, Child { };

let boy: Boy = {
    name: 'name',
    run() { },
    eat() { },
    cry() { }
};
```

接口继承类

```
class Auto {
    state = 1;
}

interface AutoInterFace extends Auto {
    id: string;
}

class C implements AutoInterFace {
    state = 1;
    id = '100';
}
```

bus 继承 auto 实现 autointerface 接口
此处不必实现 state 属性，因为是 auto 的子类，继承了 state 属性
接口在抽离类的成员的时候，不仅抽离了公共成员，而且抽离了私有成员，受保护成员

```
class Bus extends Auto implements AutoInterFace {
    id = '1';
}
```

### 总结:

接口和类都可以继承自己的类型
接口继承：实现接口复用
类继承：实现方法和属性复用
接口可以通过类实现，只能约束类的 public 成员
接口可以抽离类的成员（public, private, protected）
