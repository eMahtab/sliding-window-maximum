# Sliding Window Maximum
## https://leetcode.com/problems/sliding-window-maximum

You are given an array of integers nums, there is a sliding window of size k which is moving from the very left of the array to the very right. You can only see the k numbers in the window. Each time the sliding window moves right by one position.

Return the max sliding window.
```java
Example 1:

Input: nums = [1,3,-1,-3,5,3,6,7], k = 3
Output: [3,3,5,5,6,7]
Explanation: 
Window position                Max
---------------               -----
[1  3  -1] -3  5  3  6  7       3
 1 [3  -1  -3] 5  3  6  7       3
 1  3 [-1  -3  5] 3  6  7       5
 1  3  -1 [-3  5  3] 6  7       5
 1  3  -1  -3 [5  3  6] 7       6
 1  3  -1  -3  5 [3  6  7]      7

Example 2:

Input: nums = [1], k = 1
Output: [1]
```

### Constraints:

1. 1 <= nums.length <= 105
2. -104 <= nums[i] <= 104
3. 1 <= k <= nums.length

# Implementation 1 : Naive : Calculate max for each window of length k (TLE)
```java
class Solution {
    public int[] maxSlidingWindow(int[] nums, int k) {
        int[] result = new int[nums.length - k + 1];

        int index = 0;
        
        for(int i = 0; i <= nums.length - k; i++) {
            result[index++] = getMax(nums,i, i+k-1);    
        }
        return result;
    }
    
    private int getMax(int[] nums, int start, int end) {
        int max = nums[start];
        for(int i = start + 1; i <= end; i++) {
           max = Math.max(max, nums[i]);   
        }
        return max;
    }
}
```
# Implementation 2 : Next Greater Element approach
```java
class Solution {
    public int[] maxSlidingWindow(int[] nums, int k) {
        int[] result = new int[nums.length - k + 1];
        // Next greater element
        int[] ngeI = new int[nums.length];
        ngeI[nums.length - 1] = nums.length;
        Stack<Integer> stack = new Stack<>();
        stack.push(nums.length - 1);
        for(int i = nums.length - 2; i >= 0; i--) {
            while(!stack.isEmpty() && nums[stack.peek()] <= nums[i]) {
                stack.pop();
            }
            if(stack.isEmpty()) {
                ngeI[i] = nums.length;
            } else {
                ngeI[i] = stack.peek();
            }
            stack.push(i);
        }
        int index = 0;
        for(int i = 0; i <= nums.length - k; i++) {
            int j = i;
            while(ngeI[j] < i + k) {
                j = ngeI[j];
            }
            result[index++] = nums[j];
        }
        return result;
    }
}
```

# Implementation 3 :

```java
class Solution {
    public int[] maxSlidingWindow(int[] nums, int k) {
        if(nums == null || nums.length == 0 || k > nums.length)
            return new int[0];
        
        int[] result = new int[nums.length - k + 1];
        Deque<Integer> dq = new ArrayDeque<>();
        int index = 0;
        while(index < nums.length) {
            if(!dq.isEmpty() && dq.peekFirst() == index - k)
                dq.pollFirst();
            
            while(!dq.isEmpty() && nums[index] > nums[dq.peekLast()])
                dq.pollLast();
            
            dq.offerLast(index);
            if(index >= k - 1) {
                result[index-k+1] = nums[dq.peekFirst()];
            }
            index++;
        }
        return result;
    }
}
```
# References :
https://www.youtube.com/watch?v=fbkvdWUS5Ic
