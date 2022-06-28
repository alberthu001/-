# 合约之间的交互
1
2
3
4
5
6
7
8
9
10
11
 假设在区块链上有这么一个合约：

contract LuckyNumber {
  mapping(address => uint) numbers;

  function setNum(uint _num) public {
    numbers[msg.sender] = _num;
  }

  function getNum(address _myAddress) public view returns (uint) {
    return numbers[_myAddress];
  }
}
这是个很简单的合约，您可以用它存储自己的幸运号码，并将其与您的以太坊地址关联。 这样其他人就可以通过您的地址查找您的幸运号码了。

现在假设我们有一个外部合约，使用 getNum 函数可读取其中的数据。

首先，我们定义 LuckyNumber 合约的 interface ：：

1
2
3
contract NumberInterface {
  function getNum(address _myAddress) public view returns (uint);
}
请注意，这个过程虽然看起来像在定义一个合约，但其实内里不同：
首先，我们只声明了要与之交互的函数 —— 在本例中为 getNum —— 在其中我们没有使用到任何其他的函数或状态变量。
其次，我们并没有使用大括号（{ 和 >
}）定义函数体，我们单单用分号（;）结束了函数声明。这使它看起来像一个合约框架。
编译器就是靠这些特征认出它是一个接口的。
在我们的 app 代码中使用这个接口，合约就知道其他合约的函数是怎样的，应该如何调用，以及可期待什么类型的返回
值 。
继续前面 NumberInterface 的例子，我们既然将接口定义为：

1
2
3
contract NumberInterface {
  function getNum(address _myAddress) public view returns (uint);
}
我们可以在合约中这样使用：

1
2
3
4
5
6
7
8
9
10
11
12
contract MyContract {
  address NumberInterfaceAddress = 0xab38...;
  // ^ 这是FavoriteNumber合约在以太坊上的地址
  NumberInterface numberContract = NumberInterface(NumberInterfaceAddress);
  // 现在变量 `numberContract` 指向另一个合约对象

  function someFunction() public {
    // 现在我们可以调用在那个合约中声明的 `getNum`函数:
    uint num = numberContract.getNum(msg.sender);
    // ...在这儿使用 `num`变量做些什么
  }
}
通过这种方式，只要将您合约的可见性设置为 public(公共) 或 external(外部)，它们就可以与以太坊区块链上的任何其他合约进行交互。
