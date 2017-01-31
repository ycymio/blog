---
title: LeetCode 31 Next Permutation
tags:
  - LeetCode
categories:
  - OJ
  - LeetCode
  - Java
date: 2016-03-18 16:17:38

---


<blockquote class="blockquote-center">
<div style = "padding-left: 50px; text-align: left">
**Implement next permutation, which rearranges numbers into the lexicographically next greater permutation of numbers.
If such arrangement is not possible, it must rearrange it as the lowest possible order (ie, sorted in ascending order).
The replacement must be in-place, do not allocate extra memory.
**
Here are some examples. Inputs are in the left-hand column and its corresponding outputs are in the right-hand column.
1,2,3 → 1,3,2
3,2,1 → 1,2,3
1,1,5 → 1,5,1
</div>
</blockquote>

找到按数字大小排列的全排列中在给定排列以后的全排列，如果该全排列最大，那就返回最小的全排列。

<!--more-->

#### 思路:
初级版: // 未考虑不能用额外空间
假设数组是这样的 [...(A), a(pos1), ...(B)]
1. 找到数组中的某位置pos1，该位置以后都需要重新排列。(即[a(pos1), ..(B)])
2. 重新升序排列该位置以后的值。(假设[a(pos1), ..(B)]变为[...(C), a(pos1), b(posb), ...(D)], 此时a(pos1)位于该数组的pos2位置) 
3. 找到比该位置pos1的值刚好大一点(大&&最接近)的值的位置posb，把值赋予原数组的pos1。
4. pos1后复制数组C，接着再复制数组D。([a(pos1), ..(B)]变为[b(posb),...(C), a(pos1),...(D)])。

升级版:
* 这些排序和数组移动都直接在原数组进行。
* 二分查找不一定优，因为这里有重复的元素，而二分查找在查到重复的元素以后向后遍历才能找到最后一个所找元素的位置。
* 该全序列是所有全序列中的最大情况时，要返回的是最小情况，可以不进行排序，直接翻转数组即可。
* 第二个升序排序也不需要，因为找到数组中某位置pos1的方法就是这个位置之后(不包括这个位置的元素)的元素都是降序的，所以再从尾到头搜索一遍即可。就能找到刚好比该位置大的值了。
  这样的话，方法可以变换一下: 找到这个刚好比该位置大的值后，把这两个值交换位置，交换后pos1以后的值还是降序排列的(因为两个值接近连续)，所以再翻转pos1以后的数组即可。
* nums长度为0或1，也可以用该方法了。不需要多加判断。
* 有时候少一个赋值语句或者判断语句也能提高1ms，可能日积月累就提高了，或者测试数据不同吧。

#### 特殊情况:
* 数组长度为0或1
* 该全序列是所有全序列中的最大情况时
* 有重复的数字时(判断需要改变的数组的索引&&选取新的部分全序列) 

#### 代码:
```java
    // 初级版：第一次AC的
    public class Solution {
        public void nextPermutation(int[] nums) {
            int tmp = nums.length-2;
            if(nums.length < 2){
                return;
            }
            while( tmp != -1 && nums[tmp+1] <= nums[tmp]){
                tmp--;
            }
            if( tmp == -1 ){
                Arrays.sort(nums);
                return;
            }
            // which number tmp represents is the position after which will be changed.
            int[] tmpnums = new int[nums.length-tmp];
            System.arraycopy(nums, tmp, tmpnums, 0, nums.length-tmp);
            Arrays.sort(tmpnums);
            int pos = Arrays.binarySearch(tmpnums, nums[tmp]);
            if( tmpnums[pos+1] == tmpnums[pos]){
                pos++;
            }
            int nextNum = tmpnums[pos+1]; // 按理而言不会越界
            // pos+1 -> 放置的值的位置 [...(pos+1), pos+1, ...(length-pos-1)] 
            nums[tmp] = nextNum;
            System.arraycopy(tmpnums, 0, nums, tmp+1, pos+1);
            System.arraycopy(tmpnums, pos+2, nums, tmp+pos+2, nums.length-tmp-pos-2);
            // System.arraycopy(tmpnums, 0, nums, tmp, nums.length-tmp);
            // ... nums.length-tmp = pos+1+1+(nums.length-tmp-pos-1); -1
        }
    }
```

```java
    // 升级版: 1ms
    public class Solution {
        public void nextPermutation(int[] nums) {
            int tmp = nums.length-2;
            while( tmp >= 0 && nums[tmp+1] <= nums[tmp]){
                tmp--;
            }
            if( tmp < 0 ){
                // [3,2,1]: To reverse the array is enough.
                reverse(nums, 0, nums.length-1);
                return;
            }
            // which number tmp represents is the position after which will be changed.
            int i = nums.length-1;
            for( ; nums[i] <= nums[tmp]; i--){
            }
            int pos = i;
            int nextNum = nums[pos];
            nums[pos] = nums[tmp];
            nums[tmp] = nextNum;
            reverse(nums, tmp+1, nums.length-1);
            return;
        }
    
        private void reverse(int[] a, int from, int to){
            for(; from < to; from++, to--){
                int tmp = a[from];
                a[from] = a[to];
                a[to] = tmp;
            }
        }
    }
```