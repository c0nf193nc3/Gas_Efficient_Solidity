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

## statement - Don't use `SafeMath` once the solidity version is 0.8.0 or greater : 
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
