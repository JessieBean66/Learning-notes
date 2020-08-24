### 介绍

自从ES6起，symbol成为了一种新的原生类型，就像number和string一样。symbol类型的值是通过`Symbol构造函数`创建的。

    let sym1 = Symbol();

    let sym2 = Symbol("key"); // 可选的字符串key

`Symbols是不可改变且唯一的。`

    let sym2 = Symbol("key");
    let sym3 = Symbol("key");

    sym2 === sym3; // false, symbols是唯一的


































