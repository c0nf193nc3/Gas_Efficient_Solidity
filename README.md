# Gas_Efficient_Solidity

## An In-depth Look at += vs =+ in Solidity - 
#### `+=` vs `=+` :

* In Solidity, `+=` and `=+` are arithmetic operators used for addition. At first glance, they might seem interchangeable. However, when used with state variables, there's a subtle difference that can have a significant impact on the gas cost of your smart contracts. 
* The `+=` operator is a compound assignment operator that adds the right operand to the left operand and then assigns the result to the left operand. For example, if we have a state variable `x` and we write `x += y;`, it means "add `y` to `x` and then store the result in `x`". On the other hand, `=+` is actually two separate operators: `=` (assignment) and `+` (addition). When we write `x =+ y;`, it means "assign the result of `+y` to `x`". Since `+y` is just `y`, this is effectively the same as `x = y;`. So why does `+=` cost more gas than `=+` when used with state variables in Solidity?
* The answer lies in how Ethereum's EVM (Ethereum Virtual Machine) handles these operations. When you use `+=`, the EVM first loads the current value of the state variable from storage, adds the right operand to it, and then stores the result back into the state variable. Each of these steps costs a certain amount of gas. However, when you use `=+`, the EVM performs the addition operation first and then stores the result directly into the state variable, skipping the initial load operation. This results in less gas being consumed.

#### Code-Based Examples :

To better understand the difference in gas cost between `+=` and `=+`, let's look at some Solidity code examples.

##### Example 1: Using `+=`

```solidity
// Declare a state variable
uint256 public count;

// Function to increment count using +=
function increment(uint256 value) public {
    count += value;
}
```

In this example, the `increment` function uses the `+=` operator to add the input `value` to the `count` state variable. As discussed earlier, this involves loading the current value of `count` from storage, adding `value` to it, and then storing the result back into `count`. Each of these steps consumes gas. The exact gas cost can vary depending on the current state of the Ethereum network, but for the sake of this example, let's say it costs `X` gas.

##### Example 2: Using `=+`

```solidity
// Declare a state variable
uint256 public count;

// Function to increment count using =+
function increment(uint256 value) public {
    count =+ value;
}
```

In this example, the `increment` function uses the `=+` operator. This performs the addition operation first and then stores the result directly into the `count` state variable, skipping the initial load operation. As a result, it consumes less gas compared to the `+=` operator. Again, the exact gas cost can vary, but for the sake of this example, let's say it costs `Y` gas, where `Y < X`.
