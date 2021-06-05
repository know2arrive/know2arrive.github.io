## 1. typescript 带来了什么优势？

1. 在开发过程中，发现潜在问题，会有类型提示，参数检测等。
2. 更加友好的编辑器自动提示？
3. 代码语义更加清晰易懂

安装 typescript ：`yarn add typescript -D`

初始化 typescript ，生成 tsconfig.json 文件：`yarn tsc --init`

添加直接运行 ts 文件的第三方包：`yarn add ts-node @types/node -D`

运行某个 ts 文件`yarn ts-node demo.ts`

### 2. TypeScript 的类型

1. 基础类型：boolean,string,number,undefined,void,null,symbol

```jsx
// 无法进行类型推断
let count;
count = 1;

// 可以进行类型推断
let count = 1;
```

2.  对象类型：{},function,[],class

```jsx
// 声明函数的方法：

const toNumber = (str: string): number => {
  return parseInt(str, 10);
};

const toNum: (str: string) => number = (str) => {
  return parseInt(str);
};
// 冒号（：）后面跟的是类型，等号（=）后面跟的是具体实现！

// 还有Date类型
const date = new Date();
```

3.  类型注解：我们手动告诉 TS ，声明的变量是什么类型

```jsx

```

4.  类型推断：TS 会自动尝试去分析变量的类型，如果 TS 无法自动推断类型，那么需要类型注解。

```jsx
const add = (a: number, b: number) => {
  return a + b;
};
const result = add(10, 20);

// 对于返回值的类型，虽然TS能够进行类型推断，但是最好也要写，因为可能出现如下情况！
const add = (a: number, b: number) => {
  // 可能代码写错了，这样就，类型推断出的返回值就是string类型的
  return a + b + "string";
};
```

5.  函数的定义

```jsx
function add() {}
const sub = function () {};
const mul = () => {};
```

5.1 函数返回值为 `void` ,`never`

```jsx
// 返回值为void
const voidFunc = (): void => {
  // console.log(`this is a void function`)
};
// 返回值是never
const errorEmitter = (): never => {
  throw new Error(`this is a error`);
};
// 解构赋值
const add = ({ first, second }: { first: number, second: number }): number => {
  return first + second;
};
add({ first: 1, second: 2 });
```

### 3. 数组和元组的类型注解

3.1 `数组`

```jsx
const arr: (number | string)[] = [1, "2", 3];

const objArray: { name: string, age: number }[] = [{ name: "ts", age: 5 }];

// type alias
type User = { name: string, age: number };

// 与 class 的集成
class Person {
  name: string;
  age: number;
  address: string;
}

const objArr: Person[] = [
  { name: "ts", age: 5, address: "China" }, // 1. 数据结构和Person保持一致就行
  new Person(),
];
```

3.2 `元组`

```jsx
const tuple: [string, number] = ["ts", 4];
```

### 4. `interface` 和 `type`

```jsx
interface IPerson {
  name: string;
  [propName: string]: any;
}

type TPerson = {
  // 1. readonly name: string;
  name: string,
};

// 2. type 能实现基础类型的定义，interface不行，比如说下面👇：
type TBase = string | boolean;

const getPersonName = (person: TPerson): string => {
  return person.name;
};

const setPersonName = (person: IPerson, name: string): void => {
  person.name = name;
};

const person = {
  name: "js",
  address: "China",
};

// 3. 如果以字面量形式，TS会进行强校验
getPersonName({
  name: "js",
  address: "China",
});

// 4. 如果是使用了一个变量进行缓存，那么TS就不会进行强校验！这个和对象字面量直接赋值有关
getPersonName(person);
// 如果需要解决这个问题在interface中添加这个属性 [propName: string]: any;
interface IPerson {
  name: string;
  [propName: string]: any;
  say(): string; // 也可以添加一个方法
}

setPersonName(person, "js");
```

**能用 interface 实现的，就用 interface 实现，实在不行的，就用 type**

4.1. 使用 interface 定义函数类型,还有索引类型。

```jsx
interface ISayHi {
  (word: string): string;
}

const sayHi: ISayHi = (word) => {
  return `hello ${word}`;
};
```

### 5. 类

```jsx
class Persons {
  name = "ts";
  getName() {
    return this.name;
  }
}

class Teacher extends Persons {
  getTeacherName() {
    return `teacher`;
  }
  getName() {
    // 重写父类的方法，那么如果我就是想返回父类的方法呢？
    // 使用super调用父类的方法
    // return super.getName();
    return `js`;
  }
}

const teacher = new Teacher();
// console.log(`getTeacherName`, teacher.getTeacherName());
// console.log(`getName`, teacher.getName());
console.log(`getName`, teacher.getName());
```

5.1 访问修饰符

- `public`:允许在自己类的内部和外部都能被访问(默认);
- `private`:只允许在自己类的内部被访问;
- `protected`:允许在自己类的内部以及继承的子类中被访问;

```jsx
class Man {
  public name = "ts";
  private age = 10;
  protected address = "China";
  getName() {
    return this.name;
  }
  getAge() {
    return this.age;
  }
}

class Me extends Man {
  getAddress() {
    this.address; // protected
  }
}

const man = new Man();
console.log(`man:`, man);
```

5.2 类的构造函数： `constructor`

```jsx
class Parent {
  public name: string = "ts";
  constructor(name: string = "ts") {
    this.name = name;
  }
}
```

在构造函数中传参，还可以有以下的写法：

```jsx
class Parent {
  constructor(public name: string = "ts") {
    // .....
  }
}
```

5.3 类的继承：`super`

```jsx
class Parent {
  constructor(public name: string = "ts") {}
}

class Child extends Parent {
// 继承了 Parent类，如果在子类中调用了constructor
  constructor() {
    super();  // 就一定要在constructor中调用super
  }
}
```

5.4 类的静态属性：`getter` 和 `setter`

```jsx
class Human {
  constructor(private _name: string) {}
  get name() {
    return `get ${this._name}`;
  }
  set name(name: string) {
    this._name = `set ${name}`;
  }
}

const human = new Human(`ts`);
console.log(human.name);

human.name = "js";
console.log("set", human.name);
```

单例模式

```jsx
class Singleton {
  private constructor() {}
  private static instance: Singleton;

  static getInstance() {
    if (this.instance) return this.instance;
    this.instance = new Singleton();
    return this.instance;
  }
}

const single = Singleton.getInstance();
const singleTwo = Singleton.getInstance();
console.log(`isSame:`, singleTwo === single);
```
