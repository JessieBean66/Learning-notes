!> 关于术语的一点说明: 请务必注意一点，TypeScript 1.5里术语名已经发生了变化。 “内部模块”现在称做“命名空间”。 “外部模块”现在则简称为“模块”，这是为了与 ECMAScript 2015里的术语保持一致，(也就是说 `module X { `相当于现在推荐的写法 `namespace X {`)。

### 介绍

从ECMAScript 2015开始，JavaScript引入了`模块`的概念。TypeScript也沿用这个概念。

模块在其自身的作用域里执行，而不是在全局作用域里；这意味着定义在一个模块里的变量，函数，类等等在模块外部是不可见的，除非你明确地使用`export`[形式之一](#export-type)导出它们。 相反，如果想使用其它模块导出的变量，函数，类，接口等的时候，你必须要导入它们，可以使用 `import`[形式之一](#import-type)。

模块是自声明的；两个模块之间的关系是通过在`文件级别上`使用imports和exports建立的。

模块使用模块加载器去导入其它的模块。 在运行时，模块加载器的作用是在执行此模块代码前去查找并执行这个模块的所有依赖。 大家最熟知的`JavaScript模块加载器`是服务于`Node.js`的 `CommonJS`和服务于`Web应用`的`Require.js`。

TypeScript与ECMAScript 2015一样，任何包含顶级import或者export的文件都被`当成一个模块`。相反地，如果一个文件不带有顶级的import或者export声明，那么它的内容被视为`全局可见`的（因此对模块也是可见的）。

### 导出 :id=export-type

> **导出声明**

任何声明（比如变量，函数，类，类型别名或接口）都能够通过添加`export`关键字来导出。
~~~ Validation.ts
export interface StringValidator {
    isAcceptable(s: string): boolean;
}
~~~

~~~ ZipCodeValidator.ts
export const numberRegexp = /^[0-9]+$/;

export class ZipCodeValidator implements StringValidator {
    isAcceptable(s: string) {
        return s.length === 5 && numberRegexp.test(s);
    }
}
~~~

> **导出语句**

导出语句很便利，因为我们可能需要对导出的部分`重命名`，所以上面的例子可以这样改写：

    class ZipCodeValidator implements StringValidator {
        isAcceptable(s: string) {
            return s.length === 5 && numberRegexp.test(s);
        }
    }
    export { ZipCodeValidator };
    export { ZipCodeValidator as mainValidator };

> **重新导出**

我们经常会去扩展其它模块，并且只导出那个模块的`部分内容`。 重新导出功能并不会在当前模块导入那个模块或定义一个新的局部变量。

~~~ ParseIntBasedZipCodeValidator.ts
export class ParseIntBasedZipCodeValidator {
    isAcceptable(s: string) {
        return s.length === 5 && parseInt(s).toString() === s;
    }
}

// 导出原先的验证器但做了重命名
export {ZipCodeValidator as RegExpBasedZipCodeValidator} from "./ZipCodeValidator";
~~~

或者一个模块可以包裹多个模块，并把他们导出的内容联合在一起通过语法：`export * from "module"`。

~~~ AllValidators.ts
export * from "./StringValidator"; // exports interface StringValidator
export * from "./LettersOnlyValidator"; // exports class LettersOnlyValidator
export * from "./ZipCodeValidator";  // exports class ZipCodeValidator
~~~


### 导入 :id=import-type

模块的导入操作与导出一样简单。 可以使用以下 `import`形式之一来导入其它模块中的导出内容。

> **导入一个模块中的某个导出内容**

    import { ZipCodeValidator } from "./ZipCodeValidator";
    let myValidator = new ZipCodeValidator();

可以对导入内容`重命名`

    import { ZipCodeValidator as ZCV } from "./ZipCodeValidator";
    let myValidator = new ZCV();

> **将整个模块导入到一个变量，并通过它来访问模块的导出部分**

    import * as validator from "./ZipCodeValidator";
    let myValidator = new validator.ZipCodeValidator();

> **具有副作用的导入模块**

尽管不推荐这么做，一些模块会设置一些全局状态供其它模块使用。 这些模块可能没有任何的导出或用户根本就不关注它的导出。 使用下面的方法来导入这类模块：

    import "./my-module.js";

### 默认导出

每个模块都可以有一个`default`导出。 `默认导出`使用 `default关键字`标记；并且`一个模块`只能够有`一个default`导出。 需要使用一种特殊的导入形式来导入 default导出。

default导出十分便利。 比如，像JQuery这样的类库可能有一个默认导出 jQuery或$，并且我们基本上也会使用同样的名字jQuery或$导出JQuery。
~~~ JQuery.d.ts
declare let $: JQuery;
export default $;
~~~


~~~ App.ts
import $ from "JQuery";
$("button.continue").html( "Next Step..." );
~~~

`类`和`函数声明`可以直接`被标记为默认导出`。 标记为默认导出的类和函数的名字是可以`省略`的。

~~~ ZipCodeValidator.ts
export default class ZipCodeValidator {
    static numberRegexp = /^[0-9]+$/;
    isAcceptable(s: string) {
        return s.length === 5 && ZipCodeValidator.numberRegexp.test(s);
    }
}
~~~

~~~ Test.ts
import validator from "./ZipCodeValidator";

let myValidator = new validator();
~~~

或者
~~~ StaticZipCodeValidator.ts
const numberRegexp = /^[0-9]+$/;

export default function (s: string) {
    return s.length === 5 && numberRegexp.test(s);
}
~~~

~~~ Test.ts
import validate from "./StaticZipCodeValidator";

let strings = ["Hello", "98052", "101"];

// Use function validate
strings.forEach(s => {
  console.log(`"${s}" ${validate(s) ? " matches" : " does not match"}`);
});
~~~

`default导出`也可以是一个值

    OneTwoThree.ts
    export default "123";

    Log.ts
    import num from "./OneTwoThree";
    console.log(num); // "123"


> **`export =` 和 `import = require()`**

`CommonJS`和`AMD`的环境里都有一个`exports`变量，这个变量包含了一个模块的所有导出内容。

CommonJS和AMD的exports都可以被赋值为一个对象, 这种情况下其作用就类似于 es6 语法里的默认导出，即 export default语法了。虽然作用相似，但是 `export default` 语法并不能兼容`CommonJS和AMD的exports`。

为了支持CommonJS和AMD的exports, TypeScript提供了`export =`语法。

export =语法定义一个模块的`导出对象`。 这里的对象一词指的是`类，接口，命名空间，函数或枚举`。

若使用`export =`导出一个模块，则必须使用TypeScript的特定语法`import module = require("module")`来导入此模块。

~~~ ZipCodeValidator.ts
let numberRegexp = /^[0-9]+$/;
class ZipCodeValidator {
    isAcceptable(s: string) {
        return s.length === 5 && numberRegexp.test(s);
    }
}
export = ZipCodeValidator;
~~~

~~~ Test.ts
import zip = require("./ZipCodeValidator");

// Some samples to try
let strings = ["Hello", "98052", "101"];

// Validators to use
let validator = new zip();

// Show whether each string passed each validator
strings.forEach(s => {
  console.log(`"${ s }" - ${ validator.isAcceptable(s) ? "matches" : "does not match" }`);
});
~~~

### 生成模块代码

根据编译时指定的模块目标参数，编译器会生成相应的供`Node.js (CommonJS)`，`Require.js (AMD)`，`UMD`，`SystemJS`或`ECMAScript 2015 native modules `(ES6)模块加载系统使用的代码。 想要了解生成代码中 define，require 和 register的意义，请参考相应模块加载器的文档。

下面的例子说明了导入导出语句里使用的名字是怎么转换为相应的模块加载器代码的。

~~~ SimpleModule.ts
import m = require("mod");
export let t = m.something + 1;
~~~

~~~ AMD / RequireJS SimpleModule.js
define(["require", "exports", "./mod"], function (require, exports, mod_1) {
    exports.t = mod_1.something + 1;
});
~~~

~~~ CommonJS / Node SimpleModule.js
let mod_1 = require("./mod");
exports.t = mod_1.something + 1;
~~~

~~~ UMD SimpleModule.js
(function (factory) {
    if (typeof module === "object" && typeof module.exports === "object") {
        let v = factory(require, exports); if (v !== undefined) module.exports = v;
    }
    else if (typeof define === "function" && define.amd) {
        define(["require", "exports", "./mod"], factory);
    }
})(function (require, exports) {
    let mod_1 = require("./mod");
    exports.t = mod_1.something + 1;
});
~~~

~~~ System SimpleModule.js
System.register(["./mod"], function(exports_1) {
    let mod_1;
    let t;
    return {
        setters:[
            function (mod_1_1) {
                mod_1 = mod_1_1;
            }],
        execute: function() {
            exports_1("t", t = mod_1.something + 1);
        }
    }
});
~~~


~~~ Native ECMAScript 2015 modules SimpleModule.js
import { something } from "./mod";
export let t = something + 1;
~~~

### 可选的模块加载和其它高级加载场景

某种条件下才加载某个模块。 在TypeScript里，使用下面的方式来实现它和其它的高级加载场景，我们可以直接调用`模块加载器`并且可以保证类型完全。

编译器会检测是否每个模块都会在生成的JavaScript中用到。 如果一个模块标识符只在类型注解部分使用，并且完全没有在表达式中使用时，就不会生成 `require`这个模块的代码。 省略掉没有用到的引用对性能提升是很有益的，并同时提供了选择性加载模块的能力。

这种模式的核心是`import id = require("...")`语句可以让我们访问模块导出的类型。 模块加载器会被动态调用（通过 require），就像下面if代码块里那样。 它利用了省略引用的优化，所以模块只在被需要时加载。 为了让这个模块工作，一定要注意 import定义的标识符只能在表示类型处使用（不能在会转换成JavaScript的地方）。

为了确保类型安全性，我们可以使用`typeof`关键字。 typeof关键字，当在表示类型的地方使用时，会得出一个类型值，这里就表示模块的类型。

~~~ js
// Node.js里的动态模块加载
declare function require(moduleName: string): any;

import { ZipCodeValidator as Zip } from "./ZipCodeValidator";

if (needZipValidation) {
    let ZipCodeValidator: typeof Zip = require("./ZipCodeValidator");
    let validator = new ZipCodeValidator();
    if (validator.isAcceptable("...")) { /* ... */ }
}
~~~

~~~ js
// 示例：require.js里的动态模块加载
declare function require(moduleNames: string[], onLoad: (...args: any[]) => void): void;

import * as Zip from "./ZipCodeValidator";

if (needZipValidation) {
    require(["./ZipCodeValidator"], (ZipCodeValidator: typeof Zip) => {
        let validator = new ZipCodeValidator.ZipCodeValidator();
        if (validator.isAcceptable("...")) { /* ... */ }
    });
}
~~~

~~~ js
// 示例：System.js里的动态模块加载
declare const System: any;

import { ZipCodeValidator as Zip } from "./ZipCodeValidator";

if (needZipValidation) {
    System.import("./ZipCodeValidator").then((ZipCodeValidator: typeof Zip) => {
        var x = new ZipCodeValidator();
        if (x.isAcceptable("...")) { /* ... */ }
    });
}
~~~

### 使用其它的JavaScript库

> **外部模块**

在Node.js里大部分工作是通过加载一个或多个模块实现的。 我们可以使用顶级的` export`声明来为每个模块都定义一个`.d.ts`文件，但最好还是写在一个大的.d.ts文件里。 我们使用与构造一个外部命名空间相似的方法，但是这里使用 module关键字并且把名字用引号括起来，方便之后import。 例如：

*模块声明通配符*

*UMD模块*

### 创建模块结构指导

> **尽可能地在顶层导出**

- 如果要导出多个对象，把它们放在顶层里导出
- 明确地列出导入的名字
- 使用命名空间导入模式当你要导出大量内容的时候

> **使用重新导出进行扩展**

> **模块里不要使用命名空间**

> **危险信号**
以下均为模块结构上的危险信号。重新检查以确保你没有在对模块使用命名空间：

- 文件的顶层声明是export namespace Foo { ... } （删除Foo并把所有内容向上层移动一层）
- 文件只有`一个`export class或export function （考虑使用export default）
- 多个文件的顶层具有同样的export namespace Foo { （不要以为这些会合并到一个Foo中！）


