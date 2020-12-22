# TypeScript

## TypeScript 配置文件

`tsconfig.json` 是否生效：

```bash
tsc --init
# 会生成 tsconfig.json 文件

tsc demo.ts
# 编译 ./demo.ts 文件，生成 ./demo.js 文件
# ! 但是 tsconfig.json 并没有生效！！！

tsc
# ！！！只有单独运行 tsc 命令编译 ./ 所有文件的时候，tsconfig.json 才会生效
# 执行该命令的时候会读取 tsconfig.json 的内容，判断需要编译什么，怎么编译

ts-node demo.ts
# 使用 ts-node 工具只编译 demo.ts 会读取 tsconfig.json 的配置（和 tsc 不同
```

配置内容参考[官方文档 -> TSConfig Reference](https://www.typescriptlang.org/tsconfig)

```json
{
  "compilerOptions": {
    "target": "es5" /* Specify ECMAScript target version: 'ES3' (default), 'ES5', 'ES2015', 'ES2016', 'ES2017', 'ES2018', 'ES2019' or 'ESNEXT'. */,
    "module": "amd" /* Specify module code generation: 'none', 'commonjs', 'amd', 'system', 'umd', 'es2015', or 'ESNext'. */,
    "outDir": "./build" /* 编译后的输出目录 */,
    "rootDir": "./src" /* 需要编译的源文件的根目录 */,
    "outFile": "./buid/page.js" /* 将所有输出的文件放进一个文件，这样就不用相互引用了（但是只支持 module 为 amd 和 system 的情况） */,
    "allowJs": true /* 默认是不编译 JS 文件到 outDir 下（转换成 target） */,
    "checkJs": true /* 编辑器是否提示 JS 的语法问题 */,
    "sourceMap": true /* 生成 .map 文件 */,
      
    "noUnusedParameters": true, /* 未使用的函数参数（举其中一个例子，这是编辑器会报警告，同时编译也会报问题）（有点像 eslint，我觉得不算在编译的基础设置） */
  }
}
```

## TypeScript 的编译运转过程（改变文件 => 自动编译、自动执行）

```json
{
    "dev-contrast": "ts-node ./src/croller.ts" /* （不能监听文件变化）手动编译、自动执行；只是方便开发，并没有生成 js 文件，不可以把源文件交给别人使用； */,
    "build-contrast": "tsc" /* （不能监听文件变化）手动编译；虽然可以生成 js 文件了，但是每次都要手动执行命令，没有 ts-node 方便 */,
    
    "dev:build": "tsc -w" /* （监听文件变化）自动编译、但是不能自动执行（只编译生成 JS） */,
    "dev:start": "nodemon node ./build/index.js" /* 监听文件发生变化，运行后面的命令 *//* 可配置忽略文件  nodemonConfig */,
    "dev": "tsc && concurrently npm:dev:*" /* 以上两条命令需要两个命令行窗口，可以下载 concurrently 一条命令同时一直运行两条命令 *//* 先编译一次，因为可能还没编译好，就执行了 nodemon */,
}
```

## 联合类型 | 和类型保护（四种方法）

应该只有联合类型的时候需要做类型保护，**有以下四种方式**：

- 类型断言 as
- in 判断对象属性是否存在
- typeof 判断基础类型
- instanceof 判断对象类型（class 实例）

```typescript
interface Bird {
    canFly: boolean;
    sing: () => {};
}

interface Dog {
    canFly: boolean;
    bark: () => {};
}

// 类型断言
function trainAnimal(animal: Bird | Dog) { // Bird | Dog 称为“联合类型”
    // 编辑器只会提示联合类型共同具有的属性
    // 此时代码输入 animal. 会提示 .canFly 但却不会提示 .sing 或者 .bark；
    
    if (animal.canFly) {
        // 虽然你知道会飞的就是鸟，但是编辑器不知道
        // 此时如果只输入 animal.sing() 不做类型断言，编辑器就会报错
        (animal as Bird).sing();
    } else {
        (animal as Dog).bark();
    }
}

// in 语法
function trainAnimal1(animal: Bird | Dog) {
    if ('sing' in animal) {
        animal.sing();
    } else {
        // 此时代码输入 animal. 会提示 .bark，且不报错，因为编辑器可以推断出来
        animal.bark();
    }
}

// typeof 语法
function add(first: string | number, second: string | number) {
    
    // return first + second; // 会报错，“+” 无法作用在联合类型上
    
    if (typeof first === 'string' || typeof second === 'string') {
        return `${first}${second}`;
    }
    return first + second;
}

class NumberObj {
    count: number;
}
// instanceof 语法
function add(first: object | NumberObj, second: object | NumberObj) {
    if (first instanceof NumberObj && second instanceof NumberObj) {
        return first.count + second.count;
    }
    return 0;
}
```

## Enum 枚举类型

```typescript
enum Status {
    OFFLINE,
    ONLINE = 3,
    DELETED
}
// 分别为 0，3，4

console.log(Status.OFFLINE, Status[0], Status[1])
// 0, 'OFFLINE', undefined
// ！！！也就是说，可以根据 value 值反着取 key 值
```

## 泛型

函数泛型：

```typescript
function join<T, P>(first: T, second: P): T {
    return T;
}

join<number, string>(1, '1');
join(1, '1'); // 类型推断出 T = number, P = string
```

类泛型：

```typescript
interface Item {
  name: string;
}

// 要求泛型 T 具有 Item 类型的所有属性（不能随意传递泛型，对泛型做了约束
class DataManager<T extends Item> {
  constructor(private data: T[]) {}
  getItem(index: number): string {
    return this.data[index].name;
  }
}

const data = new DataManager([
  {
    name: 'dell'
  }
]);
```

## namespace

namespace 更多的是作用在编译之后的代码。

假如以下代码如果没有 namespace，编译之后就是四个**全局变量**`Header`、`Content`、`Footer`，可以在外部直接调用，但是我们只想外部只能调用`new Page()`，所以使用 namespace，模块化开发。

编译之后，`Page` 会被挂载到 `Home` 全局变量中（在编译后的文件中体现这一点`Home.Page = Page;`）（没有被`export`出去的就没有被挂载），外部使用就只能`new Home.Page()`。

注意第二个文件的 reference 的用法！！！**定义的 namespace 可以直接被使用！不需要被引用！**

这种用法引用关系不明显，可以使用 import/export 语法替代掉。

```tsx
// src/components.ts
namespace Components {
  export interface user {
    name: string;
  }

  export class Header {
    constructor() {
      const elem = document.createElement('div');
      elem.innerText = 'This is Header';
      document.body.appendChild(elem);
    }
  }

  export class Content {
    constructor() {
      const elem = document.createElement('div');
      elem.innerText = 'This is Content';
      document.body.appendChild(elem);
    }
  }

  export class Footer {
    constructor() {
      const elem = document.createElement('div');
      elem.innerText = 'This is Footer';
      document.body.appendChild(elem);
    }
  }
}
    
```

```tsx
// src/page.ts

///<reference path="components.ts" />

namespace Home {
  export namespace Dell {
    export const teacher: Components.user = {
      name: 'dell'
    };
  }
  export class Page {
    constructor() {
      // 即使没有 reference 那段注释，Components 命名空间也可以直接使用！！！
      // reference 注释只是为了表现这种引用关系（不然不知道 Components 哪里来的），只是一种注释方式！并没有实际作用！Components 可以直接使用！（以前真不知道）
      new Components.Header();
      new Components.Content();
      new Components.Footer();
      new Components.Footer();
    }
  }
}

```

## import

去掉上例所有的 namespace，使用 import/export 替代。

```tsx
// src/page.ts

import { Header, Content, Footer } from './components';

export default class Page {
  constructor() {
    new Header();
    new Content();
    new Footer();
  }
}
```

`tsconfig.json`中配置`module`编译为`amd`，所以浏览器不能识别`define`语法，所以在`index.html`中引入`requrire`相关解释代码，同时使用`amd`语法调用`Page`函数。

```html
<script src="https://cdnjs.cloudflare.com/ajax/libs/require.js/2.3.6/require.js"></script>
<script src="./dist/page.js"></script>
<script>
    require(['page'], function(page) {
        new page.default();
    });
</script>
```

编译后的部分 JS 代码如下所示：

```js
define("page", ["require", "exports", "components"], function (require, exports, components_1) {
    "use strict";
    Object.defineProperty(exports, "__esModule", { value: true });
    var Page = /** @class */ (function () {
        function Page() {
            new components_1.Header();
            new components_1.Content();
            new components_1.Footer();
        }
        return Page;
    }());
    exports.default = Page; // 挂载 default，define 定义 page，使用是 new page.default();
});
```

## Parcel 打包

在`index.html`中直接引入`page.ts`是不可行的；利用上面的方式手动引入编译后的 js 文件，同时引入 require.js 又非常麻烦，所以 Parcel 出现，可以直接引入 ts 文件，帮忙编译，同时启动一个本地服务器。

```bash
yarn add --dev parcel@next
parcel ./src/index.html
```

## 描述文件中的全局类型 .d.ts

在 index.html 中直接引入 jquery 的 cdn 地址，在 ts 文件中是无法直接使用的（因为 ts 不认识 jquery 里的变量，你又没 import 之类的）。所以要定义全局类型，帮助 ts 理解。（定义之后，就相当于引入一个全局变量，可以想象成 Math.PI 之类的）

```tsx
// ./jquery.d.ts

interface JqueryInstance {
    html: (html: string) => JqueryInstance;
}

declare var $: (param: () => void) => void; // 定义全局 变量

declare function $(readyFunc: () => void): void; // 定义全局 函数

declare function $(selector: string): JqueryInstance; // 允许函数重载（不同参数）
```

以上代码可以使用 interface 替代：

```tsx
// 使用 interface 的语法，实现函数重载
interface JQuery {
    (readyFunc: () => void): void;
    (selector: string): JqueryInstance;
}

declare var $: JQuery;

// 如何定义对象类型，以及对类进行定义
declare namespace $ {
    namespace fn {
        class init {}
    }
}
// new $.fn.init();
```

## 模块代码的类型描述文件 declare

如果将上面 cdn 的形式替换为 module 引入（yarn add jquery），要使用不同的方式定义全局，不定义全局的变量，而是定义模块，再模块内导出。

```tsx
// ./jquery.d.ts
// Es6 模块化
declare module 'jquery' { // 定义模块
  interface JqueryInstance {
    html: (html: string) => JqueryInstance;
  }
  // 混合类型
  function $(readyFunc: () => void): void;
  function $(selector: string): JqueryInstance;
  namespace $ {
    namespace fn {
      class init {}
    }
  }
  export = $; // 导出
}
```

```jsx
import $ from 'jquery'; // 因为没有 add @types/jquery，所以没有上面的定义，引入会报错
```

## 泛型中 keyof 语法

```tsx
type NAME = 'name'; // 类型不仅可以是 string, number, undefined，还可以是特定的字符串
const test: NAME = 'abc'; // 报错，因为只能是 'name'
```

```tsx
interface Person {
  name: string;
  age: number;
  gender: string;
}

class Teacher {
  constructor(private info: Person) {}
  getInfo<T extends keyof Person>(key: T): Person[T] { // ！！！
    return this.info[key];
  }
}

const teacher = new Teacher({
  name: 'dell',
  age: 18,
  gender: 'male'
});

const test = teacher.getInfo('name');
console.log(test);

// keyof 相当于遍历循环：

// 第一次循环
// type T = 'name'
// key: 'name';
// Person['name'];

// 第二次循环
// type T = 'age'
// key: 'age'
// Person['age']

// 第三次循环
// type T = 'gender'
// key: 'gender'
// Person['gender']
```

## 使用 TS 编写 Express 代码遇到的问题

### 问题 1：.d.ts 文件类型不准确

- 问题：Express 库的类型定义文件 .d.ts 文件类型描述不准确
- 现象：编写 request.body.password 的时候，编辑器并没有报错
- **原因**：request.body 在 .d.ts 文件中为 any 类型，在未引入解析库 body-parser 之前，实则整个 request.body 为 undefined，password 自然没有被定义

```tsx
// @types/express 中 request 的定义，从中可以看到 ReqBody = any？
// interface Request<P = core.ParamsDictionary, ResBody = any, ReqBody = any, ReqQuery = core.Query> extends core.Request<P, ResBody, ReqBody, ReqQuery> { }
// 解决方案：继承，覆盖
interface RequestWithBody extends Request {
    body: {
        // password: string | undefined;
        [props: string]: string | undefined;
    }
}

router.post('/getData', (req: RequestWithBody, res: Response) => {
   const { password } = req.body; 
});
```

### 问题 2：修改 request 或 response

- 问题：使用中间件对 request 或者 response 做出修改后，它们的类型并没有改变，并不能很好地做代码提示、错误提示
- 现象：比如我的中间件在我的 request 中添加了 authorName 属性，但是我输入 request. 点之后，依旧没有代码提示 authorName（因为不知道）

```tsx
// @types/express 定义
// declare global {
//     namespace Express {
//         // These open interfaces may be extended in an application-specific manner via declaration merging.
//         // See for example method-override.d.ts (https://github.com/DefinitelyTyped/DefinitelyTyped/blob/master/types/method-override/index.d.ts)
//         interface Request { }
//         interface Response { }
//         interface Application { }
//     }
// }

// 解决方案：类型融合
// 仿照 @types/express 中的定义在新建的 ./custom.d.ts 编写，然后就可以直接使用
//（源码里提示说去看官网（编写完可能需要重启 vscode
declare namespace Express {
    interface Request {
        authorName: string;
    }
}
```

## 类的装饰器

装饰器**本身是一个函数**，是用来修饰类的（**定义时候执行**，并不是新建实例执行）

- 通过 @ 符号使用
- 装饰器函数传入一个参数——构造函数
- tsconfig 需要开启实验选项

### 例一：基础使用

```tsx
/** 基础使用 **/

function testDecorator(constructor: any) {
    console.log('decorator');
}

@testDecorator
class Test {}

// 运行如上代码也会打印 decorator
// 证明并不是新建实例执行
```

### 例二：两个装饰器的执行顺序

```tsx
/** 两个装饰器的执行顺序 **/

function testDecorator1(constructor: any) {
    console.log('decorator1');
}

function testDecorator2(constructor: any) {
    console.log('decorator2');
}

@testDecorator1
@testDecorator2
class Test {}

// 先后打印 decorator2 decorator1
// 收集是从上到下，执行是从下向上（或者从右向左）执行
```

### 例三：在类挂载属性和方法

```tsx
/** 装饰器作用：在类挂载属性和方法 **/

function testDecorator(constructor: any) {
    construct.prototype.getName = () => {
        console.log('get name');
    }
}

@testDecorator
class Test {}

const test = new Test();
(test as any).getName(); // "get name"
```

### 例四：添加判断

```tsx
/** 装饰器：添加判断 **/

// 工厂模式
function testDecorator(flag: boolean) {
    if (flag) {
        // 返回一个装饰器
        return function(constructor: any) {
            constructor.prototype.getName = () => {
                console.log('get name');
            }
        }
    } else {
        return function(constructor: any) {} // 装饰器啥也不做
    }
}

@testDecorator(true) // 返回一个函数作为装饰器
class Test {}
```

### 例五：标准装饰器写法

```tsx
/** 标准装饰器写法 **/

// (...args: any[]) => any 就是一个函数注解，...args 表示接收很多参数，any[] 表示所有参数都是 any 类型（... 对应 []），返回一个 any 类型
// new (...args: any[]) => any 就是表示构造函数
function testDecorator<T extends new (...args: any[]) => any>(constructor: T) {
    return class extends constructor {
        name = 'qq';
        getName() {
            return this.name;
        }
    }; // ！！！返回一个类（与之前的写法不同）
}

@testDecorator
class Test {
    name: string;
    constructor(name: string) {
        console.log(1);
        this.name = name;
        console.log(2);
    }
}

const test = new Test('yqb');
console.log(test.name);
// 1
// 2
// qq
// 说明装饰器后执行

console.log((test as any).getName()); // 此时还不能语法提示，或者识别类
```

### 例六：更标准装饰器写法

```tsx
// 用工厂包裹（虽然我觉得没有必要）
function testDecorator() {
     return function<T extends new (...args: any[]) => any>(constructor: T) {
        return class extends constructor {
            name = 'qq';
            getName() {
                return this.name;
            }
        }; // ！！！返回一个类（与之前的写法不同）
    }   
}

const Test = testDecorator()(class {
    name: string;
    constructor(name: string) {
        console.log(1);
        this.name = name;
        console.log(2);
    }
})

const test = new Test('yqb');
test.getName(); // 'qq' // 输入 test. 有语法提示
```

## 类中的方法装饰器

如果装饰的是：

- 普通方法，target 对应的是类的 prototype（是对象）
- 静态方法，target 对应的是类的构造函数（是函数）

```tsx
// 参数有点像 Object.defineProperty() 函数一样
function getNameDecorator(target: any, key: string, descriptor: PropertyDescriptor) {
  // console.log(target, key);
  // descriptor.writable = true;
  descriptor.value = function() {
    return 'decorator';
  };
}

class Test {
  name: string;
  constructor(name: string) {
    this.name = name;
  }
  @getNameDecorator
  getName() {
    return this.name;
  }
}

const test = new Test('dell'); // target：原型；key：'getName' 对应方法名 
console.log(test.getName());
```

## 类中的属性装饰器

```tsx
// 属性装饰器没有第三个参数 descriptor
function nameDecorator(target: any, key: string): any {
  const descriptor: PropertyDescriptor = {
    writable: false
  };
  return descriptor; // 替换老的 descriptor，使其不可写
}

// 修改的并不是实例上的 name， 而是原型上 __proto__ 的 name
function nameDecorator(target: any, key: string): any {
  target[key] = 'lee';
}

// name 放在实例上
class Test {
  @nameDecorator
  name = 'Dell';
}

const test = new Test();
console.log((test as any).__proto__.name);

```

## 类中的访问器装饰器

```tsx
function visitDecorator(target: any, key: string, descriptor: PropertyDescriptor) {
  descriptor.writable = false; // 不可更改
}

class Test {
  private _name: string;
  constructor(name: string) {
    this._name = name;
  }
  get name() {
    return this._name;
  }
  @visitDecorator
  set name(name: string) {
    this._name = name;
  }
}

const test = new Test('dell');
test.name = 'dell lee';
console.log(test.name); // 报错

```

## 方法中的参数装饰器

```tsx
// 原型，方法名，参数所在的位置
function paramDecorator(target: any, method: string, paramIndex: number) {
  console.log(target, method, paramIndex);
}

class Test {
  getInfo(name: string, @paramDecorator age: number) {
    console.log(name, age);
  }
}

const test = new Test();
test.getInfo('Dell', 30);

```

## 装饰器实际使用的例子

通用处理代码的复用（拒绝重复代码）

```tsx
const userInfo: any = undefined;

// 工厂模式（为了不同的错误使用对应的错误提示）
function catchError(msg: string) {
  // 返回一个装饰器，拒绝重复 try catch 代码书写
  return function(target: any, key: string, descriptor: PropertyDescriptor) {
    const fn = descriptor.value;
    descriptor.value = function() {
      try {
        fn();
      } catch (e) {
        console.log(msg);
      }
    };
  };
}

class Test {
  @catchError('userInfo.name 不存在')
  getName() {
    return userInfo.name;
  }
  @catchError('userInfo.age 不存在')
  getAge() {
    return userInfo.age;
  }
  @catchError('userInfo.gender 不存在')
  getGender() {
    return userInfo.gender;
  }
}

const test = new Test();
test.getName();
test.getAge();

```

