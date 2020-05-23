# Largest Divisible Subset


I was solving the problem **[Largest Divisible Subset](https://leetcode.com/problems/largest-divisible-subset/description/)** on Leetcode a while back.

The problem is as follows:

Given a set of distinct positive integers, we need to find the largest subset of this set such that every pair(`E1`, `E2`) of elements in that set satisfy one of the following conditions:

- `E1` % `E2` == 0
- `E2` % `E1` == 0

If there are multiple such subsets, we can return any one of them.

The brute force solution to this problem would be to generate all subsets of a given input set and then return a largest set that satisfies the above criteria. The time complexity would be **O(2<sup>n</sup>)** + the time taken to check if the required criteria is satisfied.

We can do better with **dynamic programming.**

The crux of the problem is noticing the following *transitive* property emitted by numbers that are divisible.
**If A % B == 0 && B % C == 0 then A % C == 0 for any A<B<C.**

For us to work with the above property, we need to first sort the input array.

The recursive formula then would be:

**Dp<sub>i</sub> 
= Max of 1 + Dp<sub>j</sub> over all j in the range (0...i-1) which satisfy nums<sub>i</sub> % nums<sub>j</sub> == 0.**

We can solve this in the bottom-up fashion while keeping track of the `largest` and `parent` values for each index.
The `parent` value array would help us recreate the `largest` set.

```
public List<Integer> largestDivisibleSubset(int[] nums) {
    int len = nums.length;
    List<Integer> ans = new ArrayList<>();
    if (len == 0) {
        return ans;
    }
    Arrays.sort(nums);
    // This array holds the index of previous member of the largest set ending at i.
    int[] parent = new int[len];
    // This array holds the size of largest divisible set ending at index i including i.
    int[] largest = new int[len];
    int maxSubset = 1;
    int maxSubsetIndex = 0;
    for (int i = 0; i<len; ++i) {
        largest[i] = 1;
        parent[i] = -1;
        for (int j = 0; j < i; ++j) {
            if (nums[i] % nums[j] == 0 || nums[j] % nums[i] == 0) {
                if (largest[j] + 1 > largest[i]) {
                    largest[i] = largest[j] + 1;
                    parent[i] = j;
                }
            }
            if (largest[i] >= maxSubset) {
                maxSubset = largest[i];
                maxSubsetIndex = i;
            }
        }
    }
    
    // Create the answer subset.
    int index = maxSubsetIndex;
    while (index != -1) {
        ans.add(0, nums[index]);
        index = parent[index];
    }
    return ans;
}
```

The above solution has ***O(n<sup>2</sup>)*** time complexity and ***O(n)*** space complexity.
