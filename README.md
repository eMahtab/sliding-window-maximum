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
### Sliding Window Approach with Monotonic Queue
The idea is to keep the maximum number in the sliding window at the front of the Deque and the `possible maximum` numbers at the rear end (last) of the queue. The Deque would have numbers sorted in descending order, front having the maximum number and rear would have the smallest in the current window.

1. As we iterate over the array if `num[i]` is greater than the smallest number of current window than we start removing the numbers as long as `num[i]` is greater than number in the window.

2. Also we add the index of current number in Deque as this number could be `possible maximum` as the window slides to the right.

We could have tried to use Stack or PriorityQueue to keep track of maximum in the current window, but we don't only need to track maximum number in the current window but also other numbers `(possible maximum)` which are less than maximum number in the current window. Because as we move to the next window, these `possible maximum` numbers might become the maximum number in that window.

#### Choosing the right Data Structure : Double Ended Queue (Deque)
If we use Stack, we can only get access to the top element, for accessing other elements we would have to pop the elements from the stack and would have to push it back to stack.

Similarly if we use PriorityQueue we can only get access to the min or max element which is at the top of heap, for accessing other elements we would have to remove elements from the heap and would have to push it back to heap.

A Double ended Queue allows us to perform operation on both the ends of the data structure and we can easily access the elements in Deque. 

**We could have used Doubly Linked List as well, in Java LinkedList class implements the Deque interface and we can easily perform operations at both the end of LinkedList.**


https://docs.oracle.com/javase/8/docs/api/java/util/LinkedList.html

https://docs.oracle.com/javase/8/docs/api/java/util/Deque.html


In Java LinkedList and Deque gives us methods e.g. addFirst(), addLast(), removeFirst(), removeLast(), peekFirst(), peekLast() to easily access the front/rear element and also start traversal from the front or rear.

## Notes :
1. We store the index `i` in Deque and not `num[i]`, this is because we also have to remove numbers from the Deque as window slides to the right. We can check if index is out of current window we remove the number.

2. For given window of K, remember we don't try to store k elements in the Deque, rather we just need to keep the maximum number at the front of Deque and add the current number at the rear end of Deque. And when we remove element from Deque we start from the rear end of Deque.

# Implementation 3a : Using Deque, O(n), putting index into dequeue

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

## Implementation 3aa : Using LinkedList, O(n), putting index into dequeue

```java
class Solution {
    public int[] maxSlidingWindow(int[] nums, int k) {
        if(nums == null || nums.length < k)
           return new int[0];

        int[] result = new int[nums.length - k + 1]; 
        Deque<Integer> deque = new LinkedList<>();
        int index = 0;
        for(int i = 0; i < nums.length; i++) {
            if(!deque.isEmpty() && deque.peekFirst() == i -k)
                deque.pollFirst();
            
            while(!deque.isEmpty() && nums[i] > nums[deque.peekLast()])
                 deque.pollLast(); 

            deque.offerLast(i);       
            if(i >= k - 1) {
                result[index++] = nums[deque.peekFirst()];
            }       
        }
        return result;
    }
}
```
# Implementation 3b : Deque , O(n), putting number in Deque
```java
class Solution {
    public int[] maxSlidingWindow(int[] nums, int k) {
        Deque<Integer> deque = new ArrayDeque<>();
        int n = nums.length;
        int[] result = new int[n-k+1];
        int index = 0;
        int i = 0;
        while(index < n) {
            if(!deque.isEmpty() && (index > k-1 && deque.peekFirst() == nums[index-k]))
                deque.pollFirst();

            while(!deque.isEmpty() && nums[index] > deque.peekLast())
                deque.pollLast();

            deque.offerLast(nums[index]);

            if(index >= k-1){
                result[i++] = deque.peekFirst();
            }
            index++;        
        }
        return result;
    }
}
```
# References :
https://docs.oracle.com/javase/8/docs/api/java/util/Deque.html

https://docs.oracle.com/javase/8/docs/api/java/util/LinkedList.html

https://www.youtube.com/watch?v=fbkvdWUS5Ic
