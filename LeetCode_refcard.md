## LeetCode refcards

### Linked Lists

Tips:
* Fast and slow pointers
* Reversing a list

### Backtracking 

Backtracking is a general type of algorithm that is an optimization on exhaustive search.\
The most common type of problem that can be solved with backtracking is "find all possible ways to do something".

Instead of generating all candidates and checking them, backtracking abandons a path once it can no longer lead to answers.
Abandoning a path is also sometimes called "pruning".

Backtracking algorithms typically have a exponential time complexity or worse.

Example:
```
public List permutations(int[] nums) {
    List<List<Integer>> ans = new ArrayList<>();
    List<List<Integer>> current_state = new ArrayList<>();
    backtrack(current_state, ans, nums);
    return ans;
}

public void backtrack(List<Integer> curr, List<List<Integer>> ans, int[] nums) {
    // Check for criteris when to accept the leaf or abandon the branch
    if (curr.size() == nums.length) {
        ans.add(new ArrayList<>(curr)); // mind the cope constructor
        return;
    }
    
    // Iterate over possible options. Dont forget to prune as much as possible!
    for (int num: nums) {
        if (!curr.contains(num)) {
            curr.add(num);
            backtrack(curr, ans, nums);
            curr.remove(curr.size() - 1);
        }
    }
}
```
Tip:\
To avoid duplicates, for example [2, 2, 3] and [2, 3, 2], pass an integer variable that indicates where to start iterating (this is a very common trick used in backtracking).

### Dynamic programming

DP is optimized recursion.

We define some recursive function `dp`, that returns the answer to the original problem as if the arguments passed to it were the input.
The arguments that a recursive function takes is called a `state`.

To avoid repeating computation, we use something called `memoization`.

#### When should I consider using DP?
There are two main characteristics that problems which can be solved with DP have:

1. The problem will be asking for an optimal value (max or min), or the number of ways to do something.
* What is the minimum cost of doing ...
* What is the maximum profit of ...
* How many ways are there to ...
* What is the longest possible ...
2. At each step, you need to make a "decision", and **decisions affect future decisions**.
* A decision could be picking between two elements
* Decisions affecting future decisions could be something like "if you take this element, then you can't take that element in the future"

#### Top down vs. bottom up
Top down is implemented using recursion and a hash map/array for memoization.\
Bottom up is done iteratively and using an array. 

#### Framework for DP
1. A function `dp` that will compute/contain the answer to the problem for any given state
2. A recurrence relation to transition between states, e.g. `dp(i) = min(dp(i - 1) , dp(i - 2))`
3. Base cases

"House robber" example top-down:
```
    Map<Integer, Integer> memo = new HashMap<>();
    
    public int rob(int[] nums) {
        return dp(nums.length - 1, nums);
    }
    
    public int dp(int i, int[] nums) {
        // Base cases
        if (i == 0) {
            return nums[0];
        }
        if (i == 1) {
            return Math.max(nums[0], nums[1]);
        }
        
        if (memo.containsKey(i)) {
            return memo.get(i);
        }
        
        // Recurrence relation
        memo.put(i, Math.max(dp(i - 1, nums), dp(i - 2, nums) + nums[i]));
        return memo.get(i);
    }
```
"House robber" example bottom-up:
```
    public int rob(int[] nums) {
        int n = nums.length;
        int[] dp = new int[n];
        
        // Base cases
        dp[0] = nums[0];
        dp[1] = Math.max(nums[0], nums[1]);
        
        for (int i = 2; i < n; i++) {
            // Recurrence relation
            dp[i] = Math.max(dp[i - 1], dp[i - 2] + nums[i]);
        }
        
        return dp[n - 1];
    }
```
#### Improving the space complexity
Often there is no need to keep all states during memoization - only last 1 or 2 states are enough.

### Stack and Queues
Tips:
* **Monotonic** stacks or queues are useful in problems that, for each element, involves finding the "next" element based on some criteria, for example, the next greater element.
```
    while (stack.length > 0 && stack[top] >= num) {
        stack.pop()
    }
    stack.push(num)
```
* String problems with stacks. Stacks are useful for string matching because it saves a "history" of the previous characters. 