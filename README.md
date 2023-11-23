# Gas Efficient Techniques for Auditing 

## Using `> 0` costs more gas than `!= 0` when used on a `uint` in a `require()` statement : 
"When writing Solidity code, especially when dealing with `require()` statements, it's important to consider gas optimization. If you're checking whether a `uint` (unsigned integer) is not zero, using the `!= 0` comparison operator is more gas-efficient than using `> 0`. This small change can lead to significant gas savings when the contract is executed on the Ethereum network."

##### Here's an example of how you might apply this in code:

```solidity
// Less gas efficient
require(someUint > 0, "Value must be greater than 0");

// More gas efficient
require(someUint != 0, "Value must not be zero");
```

In the first `require()` statement, we're checking if `someUint` is greater than 0, which is less gas efficient. In the second `require()` statement, we're checking if `someUint` is not equal to 0, which is more gas efficient. Both these statements serve the same purpose in most cases, but the second one uses less gas. This is a simple yet effective way to optimize your smart contracts for gas usage.

## It costs more gas to initialize variables to zero than to let the default of zero be applied : 
"In Solidity, the default value for uninitialized variables is zero. Therefore, explicitly initializing a variable to zero consumes more gas than leaving it uninitialized. This is because the Ethereum Virtual Machine (EVM) needs to execute additional operations to set the variable to zero, which increases the gas cost."

##### Here's an example to illustrate this:

```solidity
// More gas consumption
uint256 public myVar = 0;

// Less gas consumption
uint256 public myVar;
```

In the first line, `myVar` is explicitly initialized to 0, which consumes more gas. In the second line, `myVar` is left uninitialized and thus defaults to 0, which is more gas-efficient. This is a good practice to follow when writing smart contracts, as it helps to optimize gas usage.

## `++i` costs less gas than `i++`, especially when it's used in `for`-loops (`--i`/`i--` too) :
"In Solidity, using `++i` or `--i` (pre-increment or pre-decrement) is more gas-efficient than using `i++` or `i--` (post-increment or post-decrement), particularly in `for` loops. This is because `i++` and `i--` create a temporary copy of `i`, increment or decrement `i`, and then return the temporary copy, while `++i` and `--i` increment or decrement `i` directly and return the result. The additional operation in `i++` and `i--` leads to higher gas costs."

##### Here's an example to illustrate this:

```solidity
// More gas consumption
for (uint i = 0; i < n; i++) {
    // Some operations
}

// Less gas consumption
for (uint i = 0; i < n; ++i) {
    // Some operations
}
```

In the first loop, `i++` is used, which is less gas-efficient. In the second loop, `++i` is used, which is more gas-efficient. This is a good practice to follow when writing loops in Solidity, as it helps to optimize gas usage. The same applies to `--i` and `i--`.

## Using `private` rather than `public` for constants, saves gas : 
"In Solidity, declaring constants as `private` instead of `public` can help save gas. This is because `public` constants have an automatically generated getter function, which is not the case for `private` constants. Although you can't access `private` constants from derived contracts or via transactions, they're a good choice when you want to define constant values that are only used within the contract where they're declared."

##### Here's an example to illustrate this:

```solidity
// More gas consumption
public uint256 publicConstant = 123;

// Less gas consumption
private uint256 privateConstant = 123;
```

In the first line, `publicConstant` is declared as `public`, which consumes more gas due to the automatically generated getter function. In the second line, `privateConstant` is declared as `private`, which is more gas-efficient. This is a good practice to follow when writing Solidity contracts, as it helps to optimize gas usage. However, keep in mind that `private` constants are not accessible from derived contracts or via transactions. If you need to access a constant from outside the contract, you'll need to use `public`.

## Don't use `SafeMath` once the solidity version is 0.8.0 or greater : 
"In Solidity, the `SafeMath` library was commonly used to prevent integer overflow and underflow issues in versions prior to 0.8.0. However, starting from version 0.8.0, Solidity has built-in overflow and underflow checks. Therefore, using `SafeMath` is no longer necessary and can be avoided to save gas."

##### Here's an example to illustrate this:

```solidity
// Solidity 0.7.6 or earlier
import "@openzeppelin/contracts/math/SafeMath.sol";

contract MyContract {
    using SafeMath for uint256;

    function add(uint256 a, uint256 b) public pure returns (uint256) {
        return a.add(b);
    }
}

// Solidity 0.8.0 or later
contract MyContract {
    function add(uint256 a, uint256 b) public pure returns (uint256) {
        return a + b;
    }
}
```

In the first contract, `SafeMath` is used to prevent overflow when adding two `uint256` numbers. In the second contract, `SafeMath` is not used because overflow checks are built into the language starting from version 0.8.0. This makes the code simpler and more gas-efficient. However, it's important to note that you should always use the latest version of Solidity that's compatible with your codebase for the best security and efficiency.

##  Use custom errors rather than `revert()`/`require()` strings to save deployment gas :
"In Solidity, using custom errors instead of `revert()` or `require()` with string messages can help save gas during contract deployment. This is because string messages consume a significant amount of gas. Starting from Solidity 0.8.4, you can define custom errors with parameters, which are more gas-efficient than string messages."

##### Here's an example to illustrate this:

```solidity
// Solidity 0.8.4 or later
contract MyContract {
    error InsufficientBalance(uint256 available, uint256 required);

    function withdraw(uint256 amount) public {
        if (amount > msg.sender.balance)
            revert InsufficientBalance(msg.sender.balance, amount);
        // ...
    }
}
```

In this contract, a custom error `InsufficientBalance` is defined with two parameters. When the `withdraw` function is called with an amount greater than the sender's balance, the `InsufficientBalance` error is reverted with the available and required balances as parameters. This is more gas-efficient than using a `revert()` or `require()` with a string message. However, it's important to note that custom errors are only available starting from Solidity 0.8.4. If you're using an earlier version, you'll need to use `revert()` or `require()` with string messages. Always use the latest version of Solidity that's compatible with your codebase for the best security and efficiency.

## State variables should be cached in stack variables rather than re-reading them from storage : 
"In Solidity, reading data from storage is more gas-intensive than reading data from memory or stack. Therefore, if you're using a state variable multiple times in a function, it's more gas-efficient to cache it in a local (stack) variable at the start of the function, and then use this local variable instead of the state variable. This way, you're reading from storage only once, which can lead to significant gas savings."

##### Here's an example to illustrate this:

```solidity
// Less gas efficient
contract MyContract {
    uint256 public myVar;

    function calculate() public view returns (uint256) {
        return myVar * 2 + myVar * 3 + myVar * 4;
    }
}

// More gas efficient
contract MyContract {
    uint256 public myVar;

    function calculate() public view returns (uint256) {
        uint256 localMyVar = myVar;
        return localMyVar * 2 + localMyVar * 3 + localMyVar * 4;
    }
}
```

In the first contract, `myVar` is read from storage three times in the `calculate` function, which is less gas-efficient. In the second contract, `myVar` is cached in a local variable `localMyVar` at the start of the function, and `localMyVar` is used instead of `myVar` in the calculations. This way, `myVar` is read from storage only once, which is more gas-efficient. This is a good practice to follow when writing Solidity contracts, as it helps to optimize gas usage. However, keep in mind that this only applies when you're using a state variable multiple times in a function. If you're using a state variable only once in a function, there's no need to cache it in a local variable. Always use the latest version of Solidity that's compatible with your codebase for the best security and efficiency.

## State variables only set in the constructor should be declared `immutable` :
"In Solidity, if a state variable is only set in the constructor and does not change afterwards, it should be declared as `immutable`. This is because `immutable` variables are assigned once during contract creation and their values are stored directly in the contract code, which leads to cheaper gas costs when they're accessed, compared to regular state variables."

##### Here's an example to illustrate this:

```solidity
// Less gas efficient
contract MyContract {
    uint256 public myVar;

    constructor(uint256 _myVar) {
        myVar = _myVar;
    }
}

// More gas efficient
contract MyContract {
    uint256 public immutable myVar;

    constructor(uint256 _myVar) {
        myVar = _myVar;
    }
}
```

In the first contract, `myVar` is a regular state variable that's set in the constructor. In the second contract, `myVar` is declared as `immutable` and set in the constructor. The second contract is more gas-efficient because `myVar` is stored directly in the contract code. This is a good practice to follow when writing Solidity contracts, as it helps to optimize gas usage. However, keep in mind that `immutable` variables can only be used if the variable is set once in the constructor and does not change afterwards. Always use the latest version of Solidity that's compatible with your codebase for the best security and efficiency.

## Using `calldata` instead of `memory` for read-only arguments in `external` functions saves gas : 
"In Solidity, function parameters are usually stored in `memory`, which is a temporary storage area that's erased between external function calls. However, for `external` functions, you can specify function parameters to be `calldata`, which is a special data location that contains the function arguments. `calldata` is read-only and cheaper to use than `memory` because it doesn't require copying data to memory. Therefore, using `calldata` instead of `memory` for read-only arguments in `external` functions can save gas."

##### Here's an example to illustrate this:

```solidity
// Less gas efficient
contract MyContract {
    function myFunction(uint256[] memory _myArray) external {
        // Some operations
    }
}

// More gas efficient
contract MyContract {
    function myFunction(uint256[] calldata _myArray) external {
        // Some operations
    }
}
```

In the first contract, `_myArray` is declared as `memory`, which is less gas-efficient. In the second contract, `_myArray` is declared as `calldata`, which is more gas-efficient. This is a good practice to follow when writing `external` functions in Solidity, as it helps to optimize gas usage. However, keep in mind that `calldata` is read-only, so you can't modify the data. Always use the latest version of Solidity that's compatible with your codebase for the best security and efficiency.

## Splitting `require()` statements that use `&&` saves gas : 
"In Solidity, using multiple `require()` statements instead of a single `require()` statement with the `&&` operator can help save gas. This is because if the first condition in a `require()` statement with `&&` fails, the EVM still checks the rest of the conditions. However, if you split the conditions into separate `require()` statements, the EVM will stop checking as soon as it encounters a failed condition."

##### Here's an example to illustrate this:

```solidity
// Less gas efficient
contract MyContract {
    function myFunction(uint256 _myVar) external {
        require(_myVar > 0 && _myVar < 100, "Invalid value");
        // Some operations
    }
}

// More gas efficient
contract MyContract {
    function myFunction(uint256 _myVar) external {
        require(_myVar > 0, "Value must be greater than 0");
        require(_myVar < 100, "Value must be less than 100");
        // Some operations
    }
}
```

In the first contract, a single `require()` statement is used with the `&&` operator, which is less gas-efficient. In the second contract, the conditions are split into separate `require()` statements, which is more gas-efficient. This is a good practice to follow when writing Solidity contracts, as it helps to optimize gas usage. Always use the latest version of Solidity that's compatible with your codebase for the best security and efficiency.

## Bytes constants are more efficient than string constants : 
"In Solidity, using `bytes` constants can be more gas-efficient than using `string` constants. This is because `string` is an array of `bytes1` (each character is a byte), while `bytes` is an array of `bytes32`. Therefore, `bytes` can store more data in a single slot of storage, making it more gas-efficient."

##### Here's an example to illustrate this:

```solidity
// Less gas efficient
string constant public myString = "Hello, World!";

// More gas efficient
bytes32 constant public myBytes = "Hello, World!";
```

In the first line, `myString` is declared as a `string` constant, which is less gas-efficient. In the second line, `myBytes` is declared as a `bytes32` constant, which is more gas-efficient. This is a good practice to follow when writing Solidity contracts, as it helps to optimize gas usage. However, keep in mind that `bytes` and `string` have different use cases and you should choose the one that best fits your needs. Always use the latest version of Solidity that's compatible with your codebase for the best security and efficiency.

## Empty blocks should be removed or emit something : 
"In Solidity, empty blocks in your code should ideally be avoided as they can lead to confusion and potential bugs. If a block of code is intentionally left empty, it's good practice to include a comment or emit an event to make it clear that the emptiness is intentional and not a mistake."

##### Here's an example to illustrate this:

```solidity
// Less clear
function myFunction() public {
    // Empty block
}

// More clear
function myFunction() public {
    // Intentionally left empty
}

// Even better
event NothingHappened();

function myFunction() public {
    emit NothingHappened();
}
```

In the first function, the block is left empty without any explanation, which can be confusing. In the second function, a comment is added to clarify that the block is intentionally left empty. In the third function, an event is emitted in the empty block to make it clear that the emptiness is intentional. This is a good practice to follow when writing Solidity contracts, as it helps to make your code more readable and understandable. Always use the latest version of Solidity that's compatible with your codebase for the best security and efficiency.

## `internal` functions not called by the contract should be removed to save deployment gas : 
"In Solidity, `internal` functions that are not called anywhere within the contract unnecessarily increase the size of the contract bytecode, leading to higher gas costs during contract deployment. Therefore, to optimize gas usage, any `internal` functions that are not used should be removed from the contract."

##### Here's an example to illustrate this:

```solidity
// Less gas efficient
contract MyContract {
    function unusedInternalFunction() internal {
        // Some operations
    }

    function myFunction() public {
        // Some operations
    }
}

// More gas efficient
contract MyContract {
    function myFunction() public {
        // Some operations
    }
}
```

In the first contract, `unusedInternalFunction` is an `internal` function that is not called anywhere within the contract, which increases the size of the contract bytecode and the gas cost of deployment. In the second contract, `unusedInternalFunction` has been removed, making the contract more gas-efficient. This is a good practice to follow when writing Solidity contracts, as it helps to optimize gas usage. Always use the latest version of Solidity that's compatible with your codebase for the best security and efficiency. However, keep in mind that you should only remove `internal` functions that are not used anywhere within the contract. If an `internal` function is used in the contract, removing it could lead to errors. Always thoroughly test your contract after making any changes to ensure it still works as expected.

## State variables only set in the constructor should be declared `immutable` - 
"In Solidity, if a state variable is only set in the constructor and does not change afterwards, it should be declared as `immutable`. This is because `immutable` variables are assigned once during contract creation and their values are stored directly in the contract code, which leads to cheaper gas costs when they're accessed, compared to regular state variables."

##### Here's an example to illustrate this:

```solidity
// Less gas efficient
contract MyContract {
    uint256 public myVar;

    constructor(uint256 _myVar) {
        myVar = _myVar;
    }
}

// More gas efficient
contract MyContract {
    uint256 public immutable myVar;

    constructor(uint256 _myVar) {
        myVar = _myVar;
    }
}
```

In the first line, `myVar` is declared as a regular state variable that's set in the constructor. In the second line, `myVar` is declared as `immutable` and set in the constructor. The second contract is more gas-efficient because `myVar` is stored directly in the contract code. This is a good practice to follow when writing Solidity contracts, as it helps to optimize gas usage. However, keep in mind that `immutable` variables can only be used if the variable is set once in the constructor and does not change afterwards. Always use the latest version of Solidity that's compatible with your codebase for the best security and efficiency.

## `public` functions not called by the contract should be declared `external` instead : 
"In Solidity, if a `public` function is not called from within the contract, it should be declared as `external`. This is because `external` functions are more gas-efficient than `public` functions when they're called from outside the contract. `External` functions can only be called from other contracts or transactions, while `public` functions can be called both internally and externally. However, `external` functions require less gas because their arguments are not copied to memory."

##### Here's an example to illustrate this:

```solidity
// Less gas efficient
contract MyContract {
    function myPublicFunction(uint256 _myVar) public {
        // Some operations
    }
}

// More gas efficient
contract MyContract {
    function myExternalFunction(uint256 _myVar) external {
        // Some operations
    }
}
```

In the first contract, `myPublicFunction` is declared as `public`, which is less gas-efficient when called externally. In the second contract, `myExternalFunction` is declared as `external`, which is more gas-efficient when called externally. This is a good practice to follow when writing Solidity contracts, as it helps to optimize gas usage. However, keep in mind that `external` functions can only be called from other contracts or transactions. If a function needs to be called from within the contract, it should be declared as `public` or `internal`. Always use the latest version of Solidity that's compatible with your codebase for the best security and efficiency.

## `<x> += <y>` costs more gas than `<x> = <x> + <y>` for state variables : 
"In Solidity, when dealing with state variables, using the `+=` operator can consume more gas than using the `=` operator with an addition operation. This is because the `+=` operator first loads the current value of the state variable from storage, adds the right-hand side value, and then stores the result back into the state variable. On the other hand, the `=` operator with an addition operation performs the addition first and then stores the result into the state variable, which can be more gas-efficient."

##### Here's an example to illustrate this:

```solidity
// More gas consumption
contract MyContract {
    uint256 public myVar;

    function increase(uint256 _value) public {
        myVar += _value;
    }
}

// Less gas consumption
contract MyContract {
    uint256 public myVar;

    function increase(uint256 _value) public {
        myVar = myVar + _value;
    }
}
```

In the first contract, the `+=` operator is used, which is less gas-efficient. In the second contract, the `=` operator with an addition operation is used, which is more gas-efficient. This is a good practice to follow when writing Solidity contracts, as it helps to optimize gas usage. Always use the latest version of Solidity that's compatible with your codebase for the best security and efficiency. However, keep in mind that the difference in gas consumption between these two methods can be minimal and may not significantly impact the overall gas cost of your contract.

## Usage of assert() instead of require() : 
"In Solidity, `assert()` and `require()` are both used for error handling, but they serve different purposes and have different gas implications. `assert()` is used for internal errors and checks invariants. If an `assert()` statement fails, all gas is consumed, which can be quite costly. On the other hand, `require()` is used to validate inputs and conditions before execution. If a `require()` statement fails, it reverts all changes and returns the remaining gas. Therefore, `require()` is generally more gas-efficient and should be used for input validation and to check conditions that might cause errors."

##### Here's an example to illustrate this:

```solidity
// Less gas efficient
contract MyContract {
    function setVar(uint256 _myVar) public {
        assert(_myVar > 0);
        // Some operations
    }
}

// More gas efficient
contract MyContract {
    function setVar(uint256 _myVar) public {
        require(_myVar > 0, "Input must be greater than 0");
        // Some operations
    }
}
```

In the first contract, `assert()` is used, which consumes all gas if the condition fails. In the second contract, `require()` is used, which reverts all changes and returns the remaining gas if the condition fails. This is a good practice to follow when writing Solidity contracts, as it helps to optimize gas usage. Always use the latest version of Solidity that's compatible with your codebase for the best security and efficiency. However, keep in mind that `assert()` and `require()` serve different purposes and should be used in appropriate situations. Always thoroughly test your contract after making any changes to ensure it still works as expected.
