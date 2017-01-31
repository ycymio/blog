---
title: LeetCode 15 3Sum
date: 2016-03-17 13:48:35
tags: [LeetCode]
categories: [OJ, LeetCode, Java]

---

<blockquote class="blockquote-center">
<div style = "padding-left: 50px; text-align: left">
 **Given an array S of n integers, are there elements a, b, c in S such that a + b + c = 0? Find all unique triplets in the array which gives the sum of zero.
Note:
Elements in a triplet (a,b,c) must be in non-descending order. (ie, a ≤ b ≤ c)
The solution set must not contain duplicate triplets.
 **
    For example, given array S = {-1 0 1 2 -1 -4},
    A solution set is:
    (-1, 0, 1)
    (-1, -1, 2)
</div>
  
</blockquote>

给定n个整型组成的数组，判断其中是否存在元素a,b,c，其和为0？找出所有的非重复的解。

<!--more-->
#### 思路：
有关 Two Pointers 的题。
通过元素a在数组nums的遍历，可转化为求两者之和等于特定值。
1. 数组排序
2. 用a对数组nums进行遍历，每一次遍历时，寻找a以后的可能出现的和为-a的两个值。 
3. 设这两个位置为low, high，分别指向数组nums中a后的位置和数组最后一个元素的位置。(two pointers的应用)
4. 如果nums[a]+nums[low]+nums[high]的值大于0，则说明high指向的值比需要的大，所以使high指向更低的值(high--)；
   如果nums[a]+nums[low]+nums[high]的值小于0，则说明low指向的值比需要的小，所以使low指向更大的值(low++)；
   如果正好为0，则记录该结果，再增加low&减小high，找寻不同的情况。直到low>=high，所有情况都遍历了。

这样时间复杂度为O(n<sup>2</sup>)，不会错过任何情况。而按照穷举法，时间复杂度为O(n<sup>3</sup>)。

#### 优化:
针对特定情况的优化效果不同:
* 如果a也大于0，则由于a ≤ b ≤ c，搜索终止。(但由于每次循环都要判断，在数组里的负数远多于正数的情况下可能使得效率降低)
* 由于要找到非重复的解，如果a重复，则跳过。如果b(low)重复则low继续加1，如果c(high)重复则high继续减1(增加了判断，可能使得效率变低)。
* Arrays.asList(...); 比新建一个List再一个一个add更简洁方便。
  
#### 代码：
```java
    public class Solution {
    	public List<List<Integer>> threeSum(int[] nums) {
    	    Arrays.sort(nums);
        	List<List<Integer>> set = new ArrayList();
        	for( int i = 0; i < nums.length -2; i++){
        	    if(nums[i] > 0) break; 
        	    if( i == 0 || nums[i] != nums[i-1]){
        	        int low = i+1;
        	        int high = nums.length-1;
                    while( low < high ){
                        int sum = nums[i] + nums[low] + nums[high];
                        if( sum > 0 ){
                            high--;
                            while( high > low && nums[high] == nums[high+1] ){
                                high--;
                            }
                        }
                        else if(sum < 0){
                            low++;
                            while( low < nums.length && nums[low] == nums[low-1] ){
                                low++;
                            }
                            if( low == nums.length ){
                                break;
                            }
                        }
                        else{
                            List<Integer> tmp = new ArrayList(3);
                            set.add(Arrays.asList(nums[i], nums[low], nums[high]));
                            low++;
                            while( low < nums.length && nums[low] == nums[low-1] ){
                                low++;
                            }
                            high--;
                            while( high > low && nums[high] == nums[high+1] ){
                                high--;
                            }
                            if( low == nums.length ){
                                break;
                            }
                        }
                    } 
                }
            }
            return set;
        }
    }
```
                
