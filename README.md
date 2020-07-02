# Sliding Window Maximum
## https://leetcode.com/problems/sliding-window-maximum

# Implementation :
```java
class Solution {
    public int[] maxSlidingWindow(int[] nums, int k) {
        if(nums == null || nums.length == 0 || k > nums.length)
            return new int[0];
        
        int[] result = new int[nums.length - k + 1];
        Deque<Integer> dq = new ArrayDeque<>();
        int i = 0;
        
        while(i < nums.length) {
            if(!dq.isEmpty() && dq.peekFirst() == i - k)
                dq.pollFirst();
            
            while(!dq.isEmpty() && nums[dq.peekLast()] < nums[i])
                dq.pollLast();
            
            dq.offerLast(i);
            
            if(i >= k - 1) {
                result[i-k+1] = nums[dq.peekFirst()];
            }
            i++;
        }
        return result;
    }
}
```
# References :
https://www.youtube.com/watch?v=fbkvdWUS5Ic
