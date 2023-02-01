---
title: Solidity 整型
last_update:

    date: 2023-01-28

---

# Solidity 整型

整型（*integer*）是不包含小数部分的数值型数据，正整数，负整数和0等都属于整型的范畴。比如账户余额，Gas，持有的 Token 数等都是用整型表示。Solidity 的整型有两种：

* `intM`   有符号整型
* `uintM` 无符号整型

其中 `M` 为8到256，步长为8。例如有 `int8, int16, int32 … int256` 。也有 `uint8, uint16, uint32 … uint256` 等等。其中， `int8/uint8` 占8bits， `int16/uint16` 占16bits，依此类推。

## 运算符

### 算术运算符

算术运算符可以用来进行四则运算，得到的结果是整型。

* `+`（加）
* `-`（减）
* `*`（乘）
* `/`（除）
* `%`（取模）
* `**`（幂）
* `<<`（左移）
* `>>`（右移）

:::tip 算术运算

```solidity
假设a=5 , b=2  类型均为uint8

a+b  : 7
a-b  : 3
a*b  : 10
a/b  : 2
a%b  : 1
a**b : 25
a<<b : 20
a>>b : 1
```

:::

### 比较运算符

通过比较运算符，我们可以比较两个变量的数量大小关系，以及变量是否相等。比较运算符得到的结果是布尔值。

* `<=`（小于等于）
* `<`  （小于）
* `==`（等于）
* `!=`（不等于）
* `>=`（大于等于）
* `>`  （大于）

:::tip 比较运算

```solidity
假设a=5 , b=2  类型均为uint8

a<=b : false
a<b  : false
a==b : false
a!=b : true
a>=b : true
a>b  : true
```

:::

### 位运算符

位运算符用来对二进制位进行操作，其执行结果是整型。

* `&`（按位与）
* `|`（按位或）
* `^`（按位异或）
* `~`（按位取反）

:::tip 位运算

```solidity
假设a=5 , b=2  类型均为uint8

a&b : 0
a|b : 7
a^b : 7
~a  : 250
```

:::

## 整型溢出

Solidity 整型运算结果可能出现溢出的情况，导致合约运行出现不符合预期的行为。在旧版本的 Solidity 中你可能会使用Openzeppelin 的 [SafeMath](https://docs.openzeppelin.com/contracts/2.x/api/math) 来防止整型溢出。不过在版本 `>= 0.8.0` 的 Solidity，一旦出现整型溢出，整个交易都会被终止。所以 Solidity 在整型溢出方面是安全的。（当然，前提是你使用了版本` >= 0.8.0` 的 Solidity ）

如下面的例子所示， `uint8` 能够储存的最大正整数为255，如果再加1，那么就会产生整型溢出。如果你执行下面的函数，Remix Terminal会输出： `call to Integer.overflow errored: VM error: revert` 。这意味着整个 transaction 已经被 revert。

:::tip 整型溢出

```solidity
// SPDX-License-Identifier: GPL-3.0

pragma solidity ^0.8.17;

contract Integer {
	function overflow() public view {
	    uint8 a = 255;
	    a = a+1; //整型溢出，整个transaction revert
	    console.log("a=%s", a);
	}
}
```

:::

我们这里说的整型溢出是安全的，说的是一旦发生整型溢出，这个交易都会被终止，就好像什么事没发生一样。但是这并不意味着你的合约没有问题，需要注意其他的潜在风险。比如说你在遍历数组的时候， `uint` 类型的下标不小心变成了-1。这可能会导致你的合约拒绝服务（*denial of service*）。

## 小结

1. Solidity有两种整型：有符号整型(`intM`)和无符号整型(`uintM`)，`M`的取值范围为8到256，步长为8
2. 我们介绍了整型可以使用的算数运算符，比较运算符和位运算符
3. Solidity 的整型运算可能会出现溢出的情况
4. 在版本 `<0.8.0` 的 Solidity 中，可以使用 Openzeppelin 的 SafeMath 库来防止整型溢出
5. 在版本` >=0.8.0` 的 Solidity 中，如果整型溢出，交易会被终止
6. 虽然 Solidity 在整型溢出方面是安全的，但是这并不意味着你的合约没有问题，需要注意其他的潜在风险
