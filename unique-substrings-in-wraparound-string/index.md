# Unique Substrings in Wraparound String


Solving **[Unqiue Substrings in Wraparound String](https://leetcode.com/problems/unique-substrings-in-wraparound-string/description/)** problem on Leetcode.

The overall gist of the problem is as follows:
Given a string `p`, you need to find the total count of all the non-empty unique substrings of `p`, present in a infinite wraparound string of *abcdefghijklmnopqrstuvwxyz*, where length of `p` could be over **10000** characters.


The brute force solution to this problem would be to go over all the substrings of `p` and check if they are present in the above wraparound string. The time complexity of this solution would be of the order of **O(n<sup>3</sup>)**.

We can do better with **dynamic programming.**

To avoid duplicates in our final count, we would need to somehow keep track of all the substrings we have already added to our count. Doing this would require a lot of memory. Instead, what if we kept track of the length of the maximum substring ending in a particular character and update it as and when we find a new substring ending in the same character. While doing so, we also substract the previous maximum from the total and add the new maximum to the total to avoid the duplicate count.

```
public int findSubstringInWraproundString(String p) {
    if (p.length() < 1) {
        return p.length();
    }
    Map<Character, Integer> charToSubstringCount = new HashMap<>();
    int prevCount = 1;
    int total = 1;
    charToSubstringCount.put(p.charAt(0), 1);
    for (int i = 1; i<p.length(); ++i) {
        char currentChar = p.charAt(i);
        char prevChar = p.charAt(i-1);
        int currentCount = 1;
        if (currentChar == 'a') {
            if (prevChar == 'z') {
                currentCount = prevCount + 1;
            }
        } else if (prevChar + 1 == currentChar) {
            currentCount = prevCount + 1;
        }
        if (!charToSubstringCount.containsKey(currentChar)) {
            charToSubstringCount.put(currentChar, currentCount);
            total += currentCount;
        } else {
            if (currentCount > charToSubstringCount.get(currentChar)) {
                total = total - charToSubstringCount.get(currentChar) + currentCount;
                charToSubstringCount.put(currentChar, currentCount);
            }
        }
        prevCount = currentCount;
    }
    return total;
}
```

***For example***

Consider `p` to be ***abzabc***.

**a** in itself is a valid substring so we start with the `total` count to be 1 and we use the `prevCount` to count the length of the substring which is valid, in this case, the consecutive elements are part of the above wraparound string, till the previous character.
Starting at index 1, we check if the previous character and current character form a valid substring. If they do, we increase the length of the valid substring to include the current character.
Once we have the length of the substring ending at the current character, we check if we already have seen a substring ending in the current character previously. If we do, and its length was smaller than the current substring length, we substract the previous length and add the new length.

Index. | Maximum Length ending in character. | Total|
----- | ---------------------------------- | -----|
0     | a->1                               | 1    |
1     | a->1, b->2                         | 3    |
2     | a->1, b->2, z->1                   | 4    |
3     | b->2, z->1, a->2                   | 5    |
4     | z->1, a->2, b->3                   | 6    |
5     | z->1, a->2, b->3, c->4             | 10   |

The above solution has ***O(n)*** time complexity and ***O(1)*** space complexity.
