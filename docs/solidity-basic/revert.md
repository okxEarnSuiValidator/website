---
title: Solidity revert
last_update:

    date: 2023-01-21

---

# Solidity revert

合约执行过程中往往会出现一些异常状况，比如输入参数不合法，算术运算时除以0，整型溢出等等。如果出现这些情况，我们就需要进行异常处理。Solidity 异常处理的统一原则是**状态回滚**（*state reverting*），也就是恢复执行前的状态，就好像什么都没有发生一样。目前 Solidity 提供了三个异常处理的函数：

* [require](require)
* [assert](assert)
* [revert](revert)

本节我们将会介绍 `revert` 。

`revert` 与 `require` 有点相似，不同的是它**不进行任何检查**，直接就抛出异常。它可以被使用在任何你想要马上抛出异常，状态变量恢复原样的场景。

:::info 权衡
当达到同一目的有多个不同可选方案时，往往意味着需要权衡取舍
:::

绝大多数 `require` 用到的场景，我们都可以使用 `revert + if-else` 达到同样的效果。所以如果是在一些复杂场景下不方便使用 `require` 时，你就可以使用 `revert + if-else` 来实现。因为它更加灵活，表达能力更高。不过这也不全是好处，在带来更灵活表达能力的同时，也降低了可读性。这是需要进行权衡的。

## revert 语法

`revert` 的语法如下所示：

```solidity
// 使用方式一
revert CustomError(arg1, arg2);

// 使用方式二
revert("My Error string");
```

其中 `CustomError` 是自定义的 Error

例如下面的示例中，把传入的 Ether 分为两半，一半转入地址 `addr1` ，另一边转到地址 `addr2` 。在实际分账之前，使用 `revert` 先检查传入的 Ether 是不是偶数。

:::tip Ether对半分账

```solidity
function splitEther(address payable addr1, address payable addr2) public payable {
    if (msg.value % 2 == 0) { // 检查传入的ether是不是偶数
        revert("Even value revertd.");
    } 
    addr1.transfer(msg.value / 2);
    addr2.transfer(msg.value / 2);
}
```

:::

## revert 与 require 的区别

我们在上面有提到 `revert` 可以代替 `require` ，在某些复杂的情景下停止执行并抛出异常。例如，你可以认为下面两份代码的作用是等价的：

:::tip `require` 与 `revert` 的等价表达式
判断是否是偶数

```solidity
require(msg.value % 2 == 0, "Even value revertd.");
```

等价于：

```solidity
if (msg.value % 2 == 0) {
    revert("Even value revertd.");
}
```

:::

在一些复杂的逻辑嵌套中，使用 `revert` 会更加方便，清晰：

```solidity
if(condition1) {
    // 可能有更多的 if-else 嵌套
    if(condition2) {
        // do something
    } 
    revert("condition2 not fulfilled");
}
```

## 参考资料

https://www.evm.codes/?fork=merge
https://www.ethervm.io/
https://github.com/ethereum/solidity/issues/2586
https://medium.com/blockchannel/the-use-of-revert-assert-and-require-in-solidity-and-the-new-revert-opcode-in-the-evm-1a3a7990e06e
https://ethereum.stackexchange.com/questions/15166/difference-between-require-and-assert-and-the-difference-between-revert-and-thro