# Wiggle Subsequence


I was solving a problem called **[Wiggle Subsequence](https://leetcode.com/problems/wiggle-subsequence)** on leetcode today.

I spent sometime thinking of the solution to the problem before I realized that there was a similar *named* problem I had solved recently. **[Longest Increasing Subsequence](https://leetcode.com/problems/longest-increasing-subsequence/description/)**.

One of the solutions to the problem runs in ***O(n<sup>2</sup>)*** time. That solution calculates the longest increasing subsequence of a given index `i` by checking the longest increasing subsequence for all indices `j` < `i` for which `nums[i] > nums[j]`. Check **[here](http://www.geeksforgeeks.org/longest-increasing-subsequence/)**.

Based on that, I devised a similar solution that ran in ***O(n<sup>2</sup>)*** time using ***O(2n)*** space.

```
public int wiggleMaxLength(int[] nums) {
    int len = nums.length;
    if (len < 2) {
        return len;
    }
    int[] parent = new int[len];
    int[] count = new int[len];
    parent[0] = -1;
    count[0] = 1;
    for (int i = 1; i < len; ++i) {
        for(int j = 0; j<i; ++j) {
        	int currentDiff = nums[i] - nums[j];

        	// If currentDiff is zero, it means that you can either
        	// include i or j but not both.
        	if (currentDiff == 0) {
        		if (count[j] > count[i]) {
        			parent[i] = parent[j];
        			count[i] = count[j];
        		}
        		continue;
        	}

        	// If there is no previous number to j then assume
        	// this is the first pair in the wiggle subsequenence.
        	if (parent[j] == -1) {
    			parent[i] = j;
    			count[i] = count[j] + 1;
        		continue;
        	}
        	int parentDiff = nums[j] - nums[parent[j]];
        	if (((currentDiff > 0) && (parentDiff < 0)) ||
        	 ((currentDiff < 0) && (parentDiff > 0))) {
        		if (count[j] + 1 > count[i]) {
        			parent[i] = j;
        			count[i] = count[j] + 1;
        		}
        	}
        }
    }

    int ans = count[0];
    for (int i = 1; i < len; ++i) {
    	ans = Math.max(ans, count[i]);
    }
    return ans;
}
```

On optimizing the above solution, one could argue that instead of looking at all the `j` elements before `i`, we could just look at the value of `i-1`.

This is how that would work:


```
public int wiggleMaxLength(int[] nums) {
    int len = nums.length;
    if (len < 2) {
        return len;
    }
    int parent = -1;
    int count = 1;
    for (int i = 1; i < len; ++i) {
        int currentDiff = nums[i] - nums[i-1];

        // If currentDiff is zero, it means that you can either
        // include i or i-1 but not both.
        if (currentDiff == 0) {
            continue;
        }

        // If there is no previous number to i-1 then assume
    	// this is the first pair in the wiggle subsequenence.
        if (parent == -1) {
            parent = i-1;
            count = count + 1;
            continue;
        }
        int parentDiff = nums[i-1] - nums[parent];
        if (((currentDiff > 0) && (parentDiff < 0)) ||
         ((currentDiff < 0) && (parentDiff > 0))) {
			parent = i - 1;
			count = count + 1;
        }
    }
    return count;
}

```

If you notice the above solution, you can see that there are ***3*** states that the `currentDiff` can be in:

1. `nums[i] > nums[i-1]`
2. `nums[i] < nums[i-1]`
3. `nums[i] = nums[i-1]`

In state **3**, we do nothing as the difference is neither positive nor negative and hence both `i` and `i-1` could not be counted towards the final count.

In state **1**, if `parentDiff`, which is nothing but `nums[i-1] - nums[parent]`, is negative, it means that element `i` adheres to the requirements and hence we increase the count. If `parentDiff` is positive, it means that you can either have `i-1` or `i` as part of the final count, but not both. This would mean that the **parent of `i` would be the same as parent of `i-1`.**

In state **2**, we follow the similar logic as in state **1** mentioned above.

The above solution has ***O(n)*** time complexity and ***O(1)*** space complexity.

