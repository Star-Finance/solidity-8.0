# SOLIDITY入门教程



## 值和类型

```
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.7;

// Data type
contract Valuetypes {
    bool public b = true;
    uint public u = 123; // [0, 2^256-1] 默认256位
    int public i = -123; // int = int256 -2**255 to 2**255-1
    int public minInt = type(int).min;
    int public maxInt = type(int).max;

    address public addr = 0x6EB72C67086D5487Dc1C27800afa6d63a3E14c75;  // 16进制数字
    // bytes32 public b32 = 
}
```

## 函数
```
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.7;
contract FunctionIntro {
    function add(uint x, uint y) external pure returns (uint){
        return x + y;
    }

    function sub(uint x, uint y) external pure returns (uint){
        return x - y;
    }
}
```

## 变量
可以大致分为状态变量，局部变量、全局变量
```
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.7;

contract StateVariables {
    uint public myuint = 123;  // 状态变量，永远记录在区块链上

    // 变量默认值
    bool public b; // false
    uint public u; // 0
    int public i; // 0
    address public a; // 0x00000000000000000000000 20位地址
    bytes32 public b32; // 64位0

    function foo() external pure returns(uint) { 
        uint notStateVariable = 345; // 局部变量，只有在执行这个函数的时候，在虚拟机内存中创建，不会记录在区块链
        return notStateVariable ;
    }
}
```
全局变量：不用自己定义，下面是常用的一些全局变量
```
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.7;

contract GlobalVeriables {
    function globalVers() external view returns(address, uint, uint) {
        address sender = msg.sender; // 上一次的方法调用者（可能是一个人，也可能是另一个合约）
        uint timestamp = block.timestamp; // 区块时间戳（只读方法中：当前的时间戳，写入方法中：出块的时间戳）
        uint blockNum = block.number; // 全局变量，区块编号（uint类型）
        return (sender, timestamp, blockNum);
    }
}

```

## 只读函数

可以使用view、pure关键词修饰
区别是view 是访问了状态变量，pure只和局部变量相关，表示纯函数

```
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.7;

contract ViewAndPureFunctions {
    uint public num;

    function viewFunc() external view returns (uint) {

    }
    function pureFunc() external pure returns (uint) {
        
    }

    function addToNum() external view returns(uint) {
        return num + 1;
    }

    function add(uint a, uint b) external pure returns(uint) {
        return a + b;
    }
}
// external外部可视
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.7;

contract Counter {
    uint public count; // public 内部和外部都可以读取

    // 两个写入方法
    function inc() external {
        count += 1;
    }

    function dec() external {
        count -= 1;
    }
}
```

## if else 和三元运算符
```
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.7;

contract IfElse {
    function example(uint _x) external pure returns(uint) {
        if(_x < 10) {
            return 1;
        } else if(_x < 20) {
            return 2;
        } else {
            return 3;
        }
    }

    function ternary(uint _x) external pure returns (uint) {
        return _x > 10 ? 10 : 1;
    }
}
```

## 循环控制
和javascript一致


## 报错控制
revert、require、assert

```
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.7;

contract Error {
    function testRequire(uint _i) public pure {
        require(_i <=10, "i>10");
    }

    function testRevert(uint _i) public pure {
        if(_i > 10) {
            revert("i>10");
        }
    }

    uint public num =123;
    function testAssert() public view {
        assert(num == 123); // 断言判断，不会  包括错误信息
    }

    // 自定义的错误8.0新增，会节约gas
    error MyError(address caller, uint i);
    function testCustomError(uint _i) public view {
        if(_i > 10 ) {
            revert MyError(msg.sender, _i);
        }
    }
}
```

// 报错之后，gas的退还，数据的回滚


## 函数修改器
类似注释和装饰器
```
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.7;

// 修改器，类似注释和装饰器
contract FunctionModifier {
    bool public paused;
    uint public count;

    function setPause(bool _paused) external {
        paused = _paused;
    }

    modifier whenNoPaused () {
        require(!paused, "paused");
        _;
    }
    
    function inc () external whenNoPaused{
        count += 1;
    }

    function dec() external whenNoPaused {
        count -= 1;
    }

}
```

## 函数返回的写法

```
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.7;

// 函数的三种返回方式
contract FunctionOutputs {
    function returnMany() public pure returns(uint, bool) {
        return (1, true);
    }

    function named() public pure returns(uint x, bool b) {
        return (1, true);
    }

//  隐式返回
    function assigned() public pure returns(uint x, bool b) {
        x = 1;
        b = true;
    }

    function destructingAssigments() public pure returns(bool) {
        // (uint x, bool b) = returnMany();
        ( ,bool b) = returnMany();
        return b;
    }
}
```

## 数组

在内存中**局部变量**只能定义**定长数组**；**动态数组**只能定义在**状态变量**中；

```
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.7;

// 智能合约的数组
contract Array{
    uint[] public nums = [1,2,3]; // 动态长度的数组
    uint[3] public numsFixed = [4,5,6]; // 固定长度的

    function example() external returns(uint, uint) {
        nums.push(4);
        uint x = nums[1];
        nums[2] = 777;
        delete nums[1]; // 不能减少数组的长度，只是把数组对应下标的改为初始化的值
        nums.pop();
        uint len = nums.length;

        // create array in memory
        uint[] memory a = new uint[](5);
        // a.pop()
        a[1] = 123;
        return (x, len);
    }
    
		// 要删除位置的右边元素整体向左边移动，然后使用pop弹出最后一个元素实现
		这样做的好处是思路比较清晰，数组的顺序不会发生变化，但是这种操作在数组长度很长的情况下是很浪费gas的
    function remove(uint _index) public {
        require(_index < nums.length);
        for(uint i = _index; i < nums.length - 1; i ++) {
            nums[i] = nums[i+1];
        }
        nums.pop();
    }
    
    // 如果不在乎数组的顺序，下面是另外一中删除数组的方式
    - 将数组最后一位赋值给index位置；最后pop掉最后一个；
    这种方式更加节约gas费
    function removeMull(uint _index) public {
    	 require(_index < nums.length);
    	 num[_index] = num[nums.length - 1];
    	 num.pop();
    }

    function test() external {
        nums = [1,2,43,56,6];
        remove(2);
    }
}
```



## 映射

```
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.7;

// 映射
contract Mapping {
    mapping(address => uint) public balances;
    mapping(address => mapping(address => bool)) public isFriend;

    function examples() external returns(mapping) {
        balances[msg.sender] = 123;
        uint bal = balances[msg.sender];
        uint bal2 = balances[address(1)]; // 0

        balances[msg.sender] += 456;

        delete balances[msg.sender]; // 0

        isFriend[msg.sender][address(this)] = true; // this: 当前合约的地址
        return balances;
    }
}
```



## 可迭代映射

```
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.7;

contract IterableMapping {
    mapping(address => uint) public balances;
    mapping(address => bool) public inserted;

    address[] public keys;

    function set(address _key, uint _val) external {
        balances[_key] = _val;

        if(!inserted[_key]) {
            inserted[_key] = true;
            keys.push(_key);
        }
    }

    function getSize() external view returns(uint) {
        return keys.length;
    }

    function getFirst() external view returns(uint) {
        return balances[keys[0]];
    }

    function getlast() external view returns(uint) {
        return balances[keys[keys.length-1]];
    }

    function getindex(uint _i) external view returns(uint) {
        return balances[keys[_i]];
    }
}
```



## 结构体

```
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.7;

contract Structs {
    struct Car { // 结构体,有点像TS中的接口
        string model;
        uint year;
        address owner;
    }

    Car public car;
    Car[] public cars;

    mapping(address => Car[]) public carsByOwner;

    function examples() external {
        Car memory toyota = Car("Toyota", 1990, msg.sender);
        Car memory lambo = Car({year: 1980, model: "Lamborghini", owner: msg.sender});
        cars.push(toyota);
        cars.push(lambo);
    }
}
```



## 枚举

```
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.7;

contract Enum {
    enum Status {
        Node,
        Pedding,
        Shipped,
        Completed,
        Rejected,
        Canceled
    }

    Status public status;

    struct Order {
        address buyer;
        Status status
    }

    Order[] public orders;

    function get() view returns (Status) {
        return status;
    }

    // 设置枚举类型
    function set(Status _status) external {
        status = _status;
    }

    // 指定特定的枚举类型
    function ship() external {
        status = Status.Shipped;
    }

    // 恢复为默认值
    function reset() external {
        delete status;
    }
 }
```



## 合约的部署

- 通过代理合约部署合约

下面的这个例子，Proxy合约负责发布合约， 但是内部是写死的，如果以后要发布新的合约必须将代理合约重新部署一遍，这其实是没有意义的；

```
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.7;

contract TestContract1 {
    address public owner = msg.sender;

    function setOwner(address _owner) public {
        require(msg.sender == owner, "not owner");
        owner = _owner;
    }
}

contract Proxy {
    function deploy() external payable {
        new TestContract1();
    }
}
```



新的代理合约方案

```
// TODO
待续...
```



## 数据的存储位置

智能合约中数据可以存储在storage、memory、calldata中

- storage：状态变量
- memory：局部变量
- calldata：函数的输入参数

calldata和memory比较类似。下面这种情况可以使用calldata

```
function a(uint calldata x) pure external returns(uint){
	b(x);
	return a + 1;
}

function b(uint calldata x) pure external returns(uint) {
	return x + 1;
}
```

解释：在函数a中调用了函数b，并把函数a中的变量局部变量作为参数传递给函数b，这种情况，如果b的参数不用calldata约定，a中的局部变量会先拷贝成memory类型，然后传递给b函数，这就会消耗过多gas。所以参数位置推荐使用calldata



## Todolist-小案例

**注意**：在 Solidity 0.8.0 中，只有数组、结构和映射类型可以指定数据位置。

```
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.7;

contract TodoList {
    struct Todo {
        string text;
        bool completed;
    }

    Todo[] public todos;

    function create(string calldata _text) external {
        todos.push(Todo({
            text: _text,
            completed: false
        }));
    }

    function updateText(uint _index, string calldata _text) external {
        require(_index < todos.length, "undefind");
        todos[_index].text = _text;
    }

    function get(uint _index) external view returns(string memory, bool)  {
        Todo storage todo = todos[_index];
        return (todo.text, todo.completed);
    }
}
```



## 事件

记录是记录当前智能合约运行状态的方法，他不会记录在区块链上（状态变量）；会体现在区块链浏览器上面或者log中；

栗子：

```
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.7;

contract Event {

    event Log(string message, address addr);
		// 带索引的方式
    event IndexLog (address indexed sender, uint val, uint val2, uint val3, uint val4);

    function example() external {
        emit Log("address", msg.sender);
        emit IndexLog(msg.sender, 123, 2,3, 8);
    }
}
```
