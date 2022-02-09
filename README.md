# Sliding Window Maximum
## https://leetcode.com/problems/sliding-window-maximum

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
