# Arrays

### Prefix Sum

#### Problem Statement

Given an array of integers `nums` and two indices `i` and `j`, write a function `range_sum(nums, i, j)` that returns the sum of elements from index `i` to index `j`, inclusive. Assume `0 <= i <= j < len(nums)`.

**Example**:

```python
pythonCopy codenums = [1, 2, 3, 4, 5]
i = 1
j = 3
```

In this example, the subarray from index `1` to `3` is `[2, 3, 4]`, so the function should return `2 + 3 + 4 = 9`.

#### Solution Using Prefix Sum

To avoid recalculating the sum every time, we can use a prefix sum array. The prefix sum array stores the cumulative sum up to each index, allowing us to calculate the sum of any subarray in constant time.

#### Steps

1. Build a prefix sum array where `prefix[k]` holds the sum of elements from the start up to index `k-1`.
2. For a range sum from `i` to `j`, the result is given by `prefix[j + 1] - prefix[i]`.

#### Python Code

```python
def range_sum(nums, i, j):
    # Step 1: Compute the prefix sum array
    prefix = [0] * (len(nums) + 1)
    for k in range(1, len(nums) + 1):
        prefix[k] = prefix[k - 1] + nums[k - 1]
    
    # Step 2: Use the prefix sum array to get the sum from index i to j
    return prefix[j + 1] - prefix[i]

# Test the function
nums = [1, 2, 3, 4, 5]
i = 1
j = 3
print(range_sum(nums, i, j))  # Output should be 9
```

#### Explanation

1.  The `prefix` array will look like this for `nums = [1, 2, 3, 4, 5]`:

    ```python
    pythonCopy codeprefix = [0, 1, 3, 6, 10, 15]
    ```

    * Here, `prefix[k]` gives the sum of the first `k` elements in `nums`.
2.  For `i = 1` and `j = 3`, the result is:

    ```python
    pythonCopy codeprefix[4] - prefix[1] = 10 - 1 = 9
    ```

This approach improves efficiency when multiple range queries need to be handled on the same array. The prefix sum technique reduces the time complexity of each query to O(1)O(1)O(1) after an initial O(n)O(n)O(n) setup for the prefix array.

### Prefix Max

#### Problem Statement

Given an array of integers `nums` and an index `i`, write a function `max_up_to(nums, i)` that returns the maximum value from the start of the array up to index `i`, inclusive. Assume `0 <= i < len(nums)`.

**Example**:

```python
pythonCopy codenums = [3, 1, 4, 1, 5, 9, 2]
i = 4
```

In this example, the subarray from index `0` to `4` is `[3, 1, 4, 1, 5]`, so the function should return the maximum value in this range, which is `5`.

#### Solution Using Prefix Max

To efficiently retrieve the maximum up to any index, we can create a prefix max array. The prefix max array at each index will store the maximum value encountered from the start of the array up to that index.

#### Steps

1. Build a prefix max array where `prefix_max[k]` holds the maximum value from the start up to index `k`.
2. For any given index `i`, simply return `prefix_max[i]`.

#### Python Code

```python
def max_up_to(nums, i):
    # Step 1: Compute the prefix max array
    prefix_max = [0] * len(nums)
    prefix_max[0] = nums[0]
    
    for k in range(1, len(nums)):
        prefix_max[k] = max(prefix_max[k - 1], nums[k])
    
    # Step 2: Return the prefix max up to index i
    return prefix_max[i]

# Test the function
nums = [3, 1, 4, 1, 5, 9, 2]
i = 4
print(max_up_to(nums, i))  # Output should be 5
```

#### Explanation

1.  The `prefix_max` array will look like this for `nums = [3, 1, 4, 1, 5, 9, 2]`:

    ```python
    pythonCopy codeprefix_max = [3, 3, 4, 4, 5, 9, 9]
    ```

    * Each entry `prefix_max[k]` holds the maximum value from `nums[0]` up to `nums[k]`.
2. For `i = 4`, the result is `prefix_max[4]`, which is `5`.

#### Why Use Prefix Max?

The prefix max technique is efficient when we need to query the maximum up to various indices multiple times. After building the `prefix_max` array in O(n)O(n)O(n), each query can be answered in O(1)O(1)O(1). This is particularly useful for range maximum queries on a fixed array, such as tracking highest scores or temperatures over time up to a given day.

### Suffix Max

#### Problem Statement

Given an array of integers `nums` and an index `i`, write a function `max_from(nums, i)` that returns the maximum value from index `i` to the end of the array, inclusive. Assume `0 <= i < len(nums)`.

**Example**:

```python
pythonCopy codenums = [3, 1, 4, 1, 5, 9, 2]
i = 3
```

In this example, the subarray from index `3` to the end is `[1, 5, 9, 2]`, so the function should return the maximum value in this range, which is `9`.

#### Solution Using Suffix Max

To efficiently retrieve the maximum from any index to the end of the array, we can create a suffix max array. The suffix max array at each index will store the maximum value from that index up to the end of the array.

#### Steps

1. Build a suffix max array where `suffix_max[k]` holds the maximum value from index `k` to the end of the array.
2. For any given index `i`, simply return `suffix_max[i]`.

#### Python Code

```python
def max_from(nums, i):
    # Step 1: Compute the suffix max array
    n = len(nums)
    suffix_max = [0] * n
    suffix_max[-1] = nums[-1]
    
    for k in range(n - 2, -1, -1):
        suffix_max[k] = max(suffix_max[k + 1], nums[k])
    
    # Step 2: Return the suffix max from index i
    return suffix_max[i]

# Test the function
nums = [3, 1, 4, 1, 5, 9, 2]
i = 3
print(max_from(nums, i))  # Output should be 9
```

#### Explanation

1.  The `suffix_max` array will look like this for `nums = [3, 1, 4, 1, 5, 9, 2]`:

    ```python
    pythonCopy codesuffix_max = [9, 9, 9, 9, 9, 9, 2]
    ```

    * Each entry `suffix_max[k]` holds the maximum value from `nums[k]` to the end of the array.
2. For `i = 3`, the result is `suffix_max[3]`, which is `9`.

#### Why Use Suffix Max?

The suffix max technique is useful when you need to query the maximum from any given index to the end multiple times. After building the `suffix_max` array in O(n)O(n)O(n), each query can be answered in O(1)O(1)O(1). This technique is often applied in scenarios such as tracking future maximum values in financial data, or solving problems where decisions depend on future values in an array.



Above concepts can be used to solve Rain water trapping problem
