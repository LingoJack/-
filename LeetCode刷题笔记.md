# LeetCode刷题笔记

​                                                                                                                                                                                          **by温俊杰**

## 3.无重复的最长子串问题

一般可以用滑动窗口来解决，滑动窗口也就是双指针。左指针，右指针，还有记录是否出现用的哈希表或者哈希集合。

双指针从字符串的左端开始，逐渐往右移动。

具体的移动条件以及执行逻辑可以参见下文。

**原题：**[无重复的最长子串问题](https://leetcode.cn/problems/longest-substring-without-repeating-characters/description/)

### 滑动窗口+哈希集合（HashSet）

~~~java
package LeetCodeExercise;

import java.util.HashSet;
import java.util.Set;

public class solution_Q3_1
{
    public int lengthOfLongestSubstring(String str)
    {
        //哈希集合记录出现的字符
        Set<Character> charOccurred=new HashSet<Character>();
        //声明左右指针
        int right=0,left=0;
        //声明最大长度，当前长度
        int maxLength=0,curLength=0;
        //len接收字符串的长度
        int len=str.length();

        //开始滑动窗口：
        //重复过程直到right指向null为止
        while(right<len)
        {
            if(!charOccurred.contains(str.charAt(right)))
            {
                //如果哈希集合中没有右指针指向的元素，则把右指针指向的元素加入哈希集合，并把右指针右移一格
                charOccurred.add(str.charAt(right));
                right++;
            }
            else
            {
                //如果哈希集合中有右指针指向的元素，则移除哈希集合中左指针指向的元素并把左指针右移一格
                charOccurred.remove(str.charAt(left));
                left++;
            }
            //计算curLength，更新maxLength
            curLength=right-left;
            maxLength=Math.max(curLength,maxLength);
        }

        return maxLength;
    }
}

~~~

**视频讲解双指针（滑动窗口）**：

[一道算法题理解滑动窗思想！【趣刷Leetcode】 No.3 无重复字符的最长子串](https://www.bilibili.com/video/BV113411v7Ak?vd_source=5f965d19d1880efe7da6e4e78abbaa30)

**解读**：

- 在这里，右边使用了`Set<Character>`而不是`HashSet<Character>`，这是一种良好的编程实践，使用接口类型（Set）而不是具体实现类型（HashSet）来声明变量。

~~~java
Set<Character> CharacterOccurred=new HashSet<Character>();
~~~

- 合理选择了数据结构

  **哈希集合（HashSet）** 是用来存储不重复元素的集合。它基于哈希表实现，但只存储集合中的元素，而不存储键-值对。**其作用是记录某元素是否在集合中**

  **哈希表（HashMap）** 是用来存储键-值对的数据结构。它也基于哈希表实现，每个键关联一个值。**哈希表中，键不可以重复，值可以重复**

- 最开始左右指针都指向第一个元素，这是双指针（滑动窗口）解法的内容



### 动态规划（dp）

~~~java
package LeetCodeExercise;

import java.util.HashMap;
import java.util.HashSet;
import java.util.Set;

public class solution_Q3_4
{
    public int lengthOfLongestSubstring(String str)
    {
        int len=str.length();
        if (len==0)
        {
            return 0;
        }
        int maxLength=0;
        int[] dp=new int[len];
        //用一个哈希表来记录已经含有的字符
        HashMap<Character,Integer> charOccurred=new HashMap<>();
        dp[0]=1;
        charOccurred.put(str.charAt(0),0);
        for(int i = 1; i < len; i++)
        {
            if(!charOccurred.containsKey(str.charAt(i)))
            {
                dp[i]=dp[i-1]+1;
                charOccurred.put(str.charAt(i),i);
            }
            else
            {
                int start=charOccurred.get(str.charAt(i))+1;
                dp[i]=i-start+1;
                charOccurred.clear();
                for(int j=start;j<=i;j++)
                {
                   charOccurred.put(str.charAt(j),j);
                }
            }
        }

        for(int i: dp)
        {
            maxLength= Math.max(maxLength, i);
        }

        return maxLength;
    }
}

~~~

**dp解法参考**

[最长不重复子串的dp解法](https://www.cnblogs.com/codershell/p/3322962.html#:~:text)



**解读**

- 定义dp数组的含义：**dp[i]是以str[i]结尾的最长不重复子串的长度**
- dp[0]=1；
- 寻找最优子结构：**如果str[i]和dp[i-1]所表示的以str[i-1]结尾的最长不重复子串的字符不相同，则有dp[i]=dp[i-1]+1;如果str[i]与dp[i-1]所表示的以str[i-1]结尾的最长不重复子串的字符有重复的，那么dp[i]等于从与str[i]相同的字符的下一个位置开始到str[i]的子串长度**
- 这里的**思考难点**是：==在遇到重复字符的时候采用start来标记重复字符的上一次出现位置的下一个位置，并且清空哈希表，从重复字符上一次出现位置的下一个位置到当前重复字符的位置，重新添加哈希表键值对==
- 用maxLength接收dp数组里的最大值即可



### 滑动窗口+哈希表（HashMap）

~~~java
package LeetCodeExercise;

import java.util.HashMap;
import java.util.HashSet;
import java.util.Set;

public class solution_Q3_2
{
    public int lengthOfLongestSubstring(String str)
    {

        int len=str.length();
        if (len==0)
        {
            return 0;
        }
       int maxLength=0,curLength=0,right=0,left=0;
       HashMap<Character,Integer> charOccurred=new HashMap<>();
       while(right<len)
       {
           if(charOccurred.get(str.charAt(right))!=null&&charOccurred.get(str.charAt(right))>=left)
           {
               left=charOccurred.get(str.charAt(right))+1;
           }
           charOccurred.put(str.charAt(right),right);
           curLength=right-left+1;
           maxLength=Math.max(maxLength,curLength);
           right++;
       }
       return maxLength;
    }
}

~~~

- 用哈希表代替哈希集合的好处就是当右指针指向的值已经在表中的时候，可以直接将左指针移动到该值上一次出现的位置的下一个位置然后重新开始计算子串，因为哈希表在右指针每指向一个未被记录的值的时候就会把该值的索引作为值，该值作为键记录进表。相反，哈希集合则是告知元素是否已经被访问过，然后左指针一个一个移动直到移动到已被访问元素的上一次出现位置的下一个位置。



### 滑动窗口+哈希表（ASCII数组）

~~~java

class Solution 
{
    public int lengthOfLongestSubstring(String s)
    {
        //用ACSII数组作为哈希表，存储字符在字符串中的索引
        int[] arr = new int[128];
        //左指针
        int i = 0;
        //右指针
        int j = 0;
        //结果
        int res = 0;
        //start用于跟踪当前考虑的子字符串的起始索引。
        int start = 0;
        while (j < s.length()) 
        {
            //获取字符串中j索引处的字符c
            char c = s.charAt(j);
            //如果字符c在前面的字符串中已经出现，而且出现在当前考虑的字串中
            if (arr[c] > 0 && arr[c] >= start)
            {
                //则让左指针等于字符c出现位置的索引+1
                i = arr[c];
                //并且从字符c出现位置的索引+1开始考虑新的子串
                start = i;
            }
            //把字符c出现位置的索引+1赋值给arr[c]
            arr[c] = j + 1;
            //更新最大值
            res = Math.max(res, j - i + 1);
            //移动右指针
            j++;
        }
        return res;
    }
}

~~~

**以下是代码的工作原理**：

1. 创建一个大小为128的整数数组`arr`。这个数组用于存储每个字符在输入字符串中最后出现的索引。
2. 使用两个指针`i`和`j`来表示当前考虑的子字符串。`i`是左指针，`j`是右指针。
3. 整数变量`res`用于存储最长的不重复字符子字符串的长度。
4. 另一个变量`start`被初始化为0。这个变量用于跟踪当前考虑的子字符串的起始索引。
5. 使用一个while循环来遍历输入字符串`s`的字符（`j`从0开始）。循环会一直执行，直到`j`达到字符串的末尾。
6. 在循环内部，从输入字符串中获取索引`j`处的字符。
7. 代码检查`arr[c]`是否大于0（即，字符`c`之前已经出现过），并且`arr[c]`是否大于或等于`start`索引。如果这个条件为真，这意味着字符`c`在当前考虑的子字符串中已经出现过。在这种情况下，代码会更新左指针`i`为字符`c`上一次出现的索引，并相应地更新`start`变量。
8. 代码更新`arr`数组，将字符`c`的当前索引`j + 1`存储其中。
9. 代码计算当前子字符串的长度（`j - i + 1`），并使用`Math.max`更新`res`变量，以记录到目前为止找到的最大长度。
10. 右指针`j`递增，以处理字符串中的下一个字符。
11. 循环会一直执行，直到处理完字符串中的所有字符，最大不重复字符子字符串的长度存储在`res`变量中。
12. 最后，方法返回`res`的值，它代表了输入字符串`s`中最长的没有重复字符的子字符串的长度。



## 4.寻找两个升序数组的中位数

**原题**：[寻找两个升序数组的中位数](https://leetcode.cn/problems/median-of-two-sorted-arrays/description/)

### 归并排序+奇偶情况讨论

~~~java
package LeetCodeExercise;

public class solution_Q4_1
{
    public double findMedianSortedArrays(int[] nums1, int[] nums2)
    {
        //接收数组的长度
        int m=nums1.length;
        int n=nums2.length;
        //存储目标位置，声明计数器
        int target = 0;
        int counter = 0;

        int[] newArr=new int[m+n];
        //如果m+n是奇数，则中位数等于合并之后升序排列的第（m+n+1）/2个元素，如果是数组下标则为（m+n-1）/2
        //如果m+n是偶数，则中位数等于（double）第（m+n）/2与第（m+n）/2+1个元素之和除2
        //如果是数组下标则为（m+n）/2-1与（m+n）/2
        if((m+n)%2==1)
        {
            target=(m+n-1)/2;
            mergeArr(nums1,nums2,counter,newArr);
            return newArr[target];
        }
        else if((m+n)%2==0)
        {
            target=(m+n)/2;
            mergeArr(nums1,nums2,counter,newArr);
            return (double) (newArr[((m+n)-2)/2]+newArr[(m+n)/2])/2;
        }
        return -1;
    }

    public void mergeArr(int[] nums1,int[] nums2,int counter,int[] newArr)
    {
        //接收数组的长度
        int m=nums1.length;
        int n=nums2.length;
        //创建两个指针遍历数组
        int ptr1=0;
        int ptr2=0;
        while(ptr1<m&&ptr2<n)
        {
            if(nums1[ptr1]>nums2[ptr2])
            {
                newArr[counter]=nums2[ptr2];
                ptr2++;
            }
            else
            {
                newArr[counter]=nums1[ptr1];
                ptr1++;
            }
            counter++;
        }


        while(ptr1!=m)
        {
            newArr[counter]=nums1[ptr1];
            counter++;
            ptr1++;
        }

        while(ptr2!=n)
        {
            newArr[counter]=nums2[ptr2];
            counter++;
            ptr2++;
        }

    }
}

~~~

- 时间复杂度：O（m+n）
- 空间复杂度：O（m+n）

思考：

​	怎么才能再降低时间复杂度？



### 二分查找*

~~~java

~~~





## 260.只出现一次的数字 III

**原题**：[只出现一次的数字III](https://leetcode.cn/problems/single-number-iii/submissions/475187080/?envType=daily-question&envId=2023-10-16)

### 双指针

~~~java
package LeetCodeExercise;

public class solution_Q260_1 {
    public int[] singleNumber(int[] nums) {
        // 定义左右指针
        int left = 0, right = left + 1;
        int len = nums.length; // 数组长度

        // 处理特殊情况，如果输入数组为空，返回null
        if (len == 0) {
            return null;
        }

        // 找到数组中的最大值，并将其加一，用于标记不可达的数
        int unreachableNum = 0;
        for (int num : nums) {
            unreachableNum = Math.max(unreachableNum, num);
        }
        unreachableNum++;

        // 创建临时答案数组和索引
        int[] tempAns = new int[len];
        int index = 0;

        // 循环处理数组元素
        loop: while (right < len || left == len - 1) {
            if (left == len - 1) {
                // 处理左指针到达数组末尾的情况
                tempAns[index] = nums[left];
                index++;
                left++;
                break;
            }

            if (nums[right] == nums[left]) {
                // 如果左右指针指向相同的数，将它们标记为不可达的数
                nums[right] = unreachableNum;
                nums[left] = unreachableNum;
                if (left < len - 1)
                    left++;
                while (nums[left] == unreachableNum) {
                    if (left < len - 1)
                        left++;
                    else {
                        break loop;
                    }
                }
                right = left + 1;
            } else if (nums[right] != nums[left]) {
                // 如果左右指针指向不同的数，移动右指针
                right++;
                if (right == len) {
                    // 处理右指针到达数组末尾的情况
                    tempAns[index] = nums[left];
                    index++;
                    left++;
                    while (nums[left] == unreachableNum) {
                        if (left < len - 1)
                            left++;
                    }
                    right = left + 1;
                }
            }
        }

        // 创建最终答案数组，长度为index，将临时答案复制到最终答案中
        int[] finalAns = new int[index];
        System.arraycopy(tempAns, 0, finalAns, 0, index);
        return finalAns;
    }
}

~~~

- 这种解法超出了时间限制
- 先计算数组的最大值，用一个变量等于最大值加一
- 指针left指向一个元素，指针right向右移动直到找到相同



### 位运算

~~~java
class Solution {
    public int[] singleNumber(int[] nums) 
    {
        int xorsum = 0;
        for (int num : nums) 
        {
            xorsum ^= num;
        }
  		//lsb意为"Least Significant Bit",最低有效位
        int lsb = xorsum & (-xorsum);
        int type1 = 0, type2 = 0;
        for (int num : nums) 
        {
            //根据最低有效位来分组
            if ((num & lsb) != 0) 
            {
                type1 ^= num;
            } 
            else
            {
                type2 ^= num;
            }
        }
        return new int[]{type1, type2};
    }
}

~~~

- 用一系列0和1存储两种状态的数据结构，就是**位图**。

- 关于位运算的细节，请见[位运算.md](D:\markdown notebook\Data Structure and Algorithm\位运算.md)

**解析**：

1. 假设数组中仅出现一次的数为$x_1$,$x_2$
2. 对数组中所有的元素进行按位异或运算，得到$x_1\oplus x_2$**(因为相同的元素异或运算后为0，任何数与零异或等于其本身)**
3. **用位运算`x&-x`取出x的二进制表示中最低位的1，假设其为第n位**
4. 那么$x_1$,$x_2$的第n位分别为0，1
5. 如此可以把数组里的元素分为二进制表示第n位为0和不为0的两组，且$x_1$,$x_2$不会在同一组
6. 分别对两组的所有元素进行按位异或运算，可以得到$x_1$,$x_2$



###  哈希表

~~~java
package LeetCodeExercise;


import java.util.HashMap;
import java.util.Map;

public class solution_Q260_2
{
    public int[] singleNumber(int[] nums)
    {
        HashMap<Integer,Integer> timesNumOccurred=new HashMap<>();
        for(int num:nums)
        {
            timesNumOccurred.put(num,timesNumOccurred.getOrDefault(num, 0)+1);
        }
        int[] ans=new int[2];
        int index=0;
        for(Map.Entry<Integer,Integer>entry:timesNumOccurred.entrySet())
        {
            if(entry.getValue()==1)
            {
                ans[index]=entry.getKey();
                index++;
            }
        }
        return ans;
    }

}

~~~

- 方法`getOrDefault(key, defaultVal)`接收两个参数，`key`是找查找的键，`defaultValue`是键不存在时默认的返回值

- 要遍历一个HashMap可以采用一下方法：

  - 使用 `keySet()` 遍历：

  ~~~java
  for (Object key : hashMap.keySet()) {
      int value = hashMap.get(key);
      System.out.println(key + ": " + value);
  }
  ~~~

  - 使用 `entrySet()` 遍历：

  ~~~java
  for (Map.Entry<String, Integer> entry : hashMap.entrySet()) {
      String key = entry.getKey();
      int value = entry.getValue();
      System.out.println(key + ": " + value);
  }
  ~~~

  - 使用 Java 8 的 `forEach` 方法：

  ~~~java
  hashMap.forEach(
      //箭头 -> 是Java 8中引入的lambda表达式的语法。它用于定义一个匿名函数，通常用于函数式编程
      //(key, value) -> 表示一个lambda表达式，它接受两个参数 key 和 value，然后执行大括号 {} 中的代码。
      //在这种情况下，它将键和值打印到控制台。
      (key, value) -> 
      {
      	System.out.println(key + ": " + value);
  	}
  );
  ~~~

  

