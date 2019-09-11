# JavaScript 写题注意事项

## 1 Map 标准统计方式

使用 `has()` `set()` `get()` 方法

```js
const map = new Map();
for(let num of deck) {
    if(!map.has(num)) {     // has
        map.set(num, 0);    // set
    }
    map.set(num, map.get(num) + 1);     // get 后 set
}
```

## 2 gcd 辗转相除

Greatest Common Divisor 最大公约数。

很早以前都是背着写，很久不写就忘了。哎。

（a 和 b 的最大公约数） 等于 （b 和 （a 除以 b 所得余数）的最大公约数）。 
```
A = B * q + R   // 很普通的 被除数 除数 商 余数 四者关系
(A, B) = (B, R) // (被除数，除数) = (除数，余数)

// 举例 
// (12921, 4234) = 73

(12921, 4234)   // 直接把除数往前写，做被除数
= (4234, 219)
= (219, 73)
= (73, 0)       // 当 b 等于 0 时， a 即为最大公约数
                // 可以进一步， 任何数都是 0 的约数。又要最大，那就是这次的结果。否则(0, 0)
```

```js
// 基本写法
const gcd = (a, b) => {
    if (b === 0) {
        return a;
    }
    return gcd(b, a % b);
}
```

[漫画算法：辗转相除法是什么鬼？（小灰深入了解）](https://zhuanlan.zhihu.com/p/31824895)
[辗转相除法（YouTube 解说）](https://www.youtube.com/watch?v=fGesPF3QA1U&list=PLg7Seqwc_Znvzr1H69PTVgBpnMYmnjn1W&index=3)


## 易忘小方法

### 字符串 转化为 数字

```js
// 将 二进制字符串 转化为 十进制数字
parseInt('0110', 2);            // 6
typeof parseInt('0110', 2);     // "number"
```

### JavaScript 字符与 ASCII 编码的转换

字符 → ASCII 码：`StringValue.charCodeAt()`

ASCII 码 → 字符：`String.fromCharCode(asciiValue)`



## 特殊字符

### 最大正整数表示

```js
Number.MAX_SAFE_INTEGER     // 9007199254740991 16位     // Math.pow(2, 53) - 1;

0x7fffffff                  // 最大 int
```

### 256 个 ASCII

