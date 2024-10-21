---
layout: post
title: "#47 1106. Parsing A Boolean Expression 🧠🚀"
categories: [LeetCode, Programming]
---

This problem revolves around evaluating complex boolean expressions that include logical operators like AND (`&`), OR (`|`), and NOT (`!`). Given a valid string representing a boolean expression, our goal is to parse it and determine whether it evaluates to **true** or **false**. This problem involves string parsing, recursion, and logical operations, making it a fun and challenging puzzle!

By the end of this post, you'll understand how to recursively evaluate nested boolean expressions and how to manage operator precedence efficiently. Let's dive in! 💡

### 📜 Problem Statement
A boolean expression is either **true** or **false**. It can take the following forms:

1. `'t'` — evaluates to `True`.
2. `'f'` — evaluates to `False`.
3. `'!(subExpr)'` — evaluates to the logical **NOT** of the inner expression `subExpr`.
4. `'&(subExpr1, subExpr2, ..., subExprn)'` — evaluates to the logical **AND** of inner expressions `subExpr1`, `subExpr2`, ..., `subExprn` where `n ≥ 1`.
5. `'|(subExpr1, subExpr2, ..., subExprn)'` — evaluates to the logical **OR** of inner expressions `subExpr1`, `subExpr2`, ..., `subExprn` where `n ≥ 1`.

Given a string `expression` that follows these rules, return the evaluation of that expression.

#### Examples

**Example 1:**
```
Input: expression = "&(|(f))"
Output: false
Explanation: 
1. Evaluate |(f) --> f.
2. The expression becomes "&(f)".
3. Evaluate &(f) --> f, which evaluates to false.
```

**Example 2:**
```
Input: expression = "|(f,f,f,t)"
Output: true
Explanation: 
1. The OR operation is performed on (false, false, false, true), which results in true.
```

**Example 3:**
```
Input: expression = "!(&(f,t))"
Output: true
Explanation: 
1. Evaluate &(f,t) --> (false AND true) --> false.
2. The expression becomes "!(f)".
3. Evaluate NOT false --> true.
```

### 🚶 Step-by-Step Explanation

Boolean expressions can be nested and involve different operators, which need to be evaluated in a specific order. Here’s how we approach this problem:

- The **OR (`|`)** operator returns `true` if any of the inner expressions are `true`.
- The **AND (`&`)** operator returns `true` if all inner expressions are `true`.
- The **NOT (`!`)** operator negates the value of its inner expression.
- Parentheses `()` are used to group sub-expressions, ensuring the correct order of evaluation.
  
### 🛠️ Basic Solution

One straightforward solution to this problem is to parse the string and evaluate each part using recursion. The idea is to process each operator (`&`, `|`, or `!`) and its sub-expressions, and return the result based on the operation.

The algorithm will:
1. Find the operator (`&`, `|`, or `!`).
2. Parse the sub-expressions inside parentheses.
3. Recursively evaluate those sub-expressions.
4. Combine the results based on the operator.

#### Python Code (Basic Solution)

```python
class Solution:
    def parseBoolExpr(self, expression: str) -> bool:
        def evaluate(expr: str) -> bool:
            if expr == 't':
                return True
            if expr == 'f':
                return False
            
            # Parse and evaluate NOT expression
            if expr[0] == '!':
                inner_expr = expr[2:-1]  # Remove '!(' and ')'
                return not evaluate(inner_expr)
            
            # Parse AND expression
            if expr[0] == '&':
                inner_exprs = parse_inner_expressions(expr[2:-1])  # Extract inner expressions
                return all(evaluate(inner_expr) for inner_expr in inner_exprs)
            
            # Parse OR expression
            if expr[0] == '|':
                inner_exprs = parse_inner_expressions(expr[2:-1])  # Extract inner expressions
                return any(evaluate(inner_expr) for inner_expr in inner_exprs)
        
        def parse_inner_expressions(expr: str) -> list:
            # This function splits inner expressions inside parentheses by commas
            result = []
            balance = 0
            start = 0
            for i, char in enumerate(expr):
                if char == '(':
                    balance += 1
                elif char == ')':
                    balance -= 1
                elif char == ',' and balance == 0:
                    result.append(expr[start:i])
                    start = i + 1
            result.append(expr[start:])
            return result
        
        return evaluate(expression)
```

### ⏳ Time Complexity of Basic Solution

The time complexity of this basic approach is **O(n)**, where `n` is the length of the input string. The algorithm traverses the expression string and processes each part exactly once, making it efficient for the problem's constraints.

### ⚡ Optimized Solution

Although the basic solution is fairly efficient, there’s an optimized version that simplifies some of the parsing logic. Instead of explicitly splitting the string inside the parentheses, we can rely on the structure of the boolean expressions and process the string in a single pass, maintaining a stack to track operators and their corresponding sub-expressions.

This approach uses a stack to handle nested boolean expressions. Whenever we encounter a closing parenthesis, we pop elements off the stack, evaluate the result, and push the result back onto the stack.

#### Python Code (Optimized Solution)

```python
class Solution:
    def parseBoolExpr(self, expression: str) -> bool:
        stack = []
        
        for char in expression:
            if char == ',':
                continue
            elif char == ')':
                # Evaluate the current expression
                seen = []
                while stack[-1] != '(':
                    seen.append(stack.pop())
                stack.pop()  # Remove '('
                
                operator = stack.pop()  # Get the operator
                if operator == '&':
                    stack.append(all(seen))
                elif operator == '|':
                    stack.append(any(seen))
                elif operator == '!':
                    stack.append(not seen[0])
            else:
                # Push everything else onto the stack
                stack.append(char == 't' if char in 'tf' else char)
        
        return stack[0]
```

### ⏳ Time Complexity of Optimized Solution

The time complexity of the optimized solution is also **O(n)** because each character in the input string is processed once. The stack-based approach ensures that the operations are performed efficiently and in the correct order.

### 🧮 Example Walkthrough

Let’s walk through **Example 3** step by step (`expression = "!(&(f,t))"`):

1. We start by processing `!(...)`. Inside, we find `&(f,t)`.
2. The AND operation evaluates the inner expressions `f` and `t`, which gives us `(false AND true)`. This results in `false`.
3. Now the expression is `!(false)`, which simplifies to `true`.
4. Finally, we return `true`.

This approach efficiently handles nested boolean expressions using recursion and a stack to evaluate operators in the correct order.

### 🏁 Conclusion

In this problem, we learned how to evaluate boolean expressions using recursive parsing and stack-based approaches. The key challenge was handling the nested structure and operator precedence, which we solved by carefully managing the order of operations. 

We explored two approaches—one that used recursion to break down the expressions and another that used a stack to evaluate the expressions in a single pass. Both approaches have a linear time complexity, making them efficient enough to handle the input constraints.

This problem showcases how powerful recursion and stack-based evaluation techniques can be when parsing and evaluating complex expressions!
