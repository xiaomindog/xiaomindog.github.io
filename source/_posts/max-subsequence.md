---
title: 动态规划--子序列和回文串问题
date: 2019-12-15 20:31:36
tags: [leetcode,动态规划,字符串]
categories: 'leetcode'
---

动态规划中关于子序列，回文串的几道题整理了一下。

<!--more-->

### 最长子序列

- 问题：给定字符串 s 和 t ，判断 s 是否为 t 的子序列。

- DP思路：（非最优解）

  记```s(i)```，```t(j)```分别为长度为 i，j 的字符串 s 和字符串 t 。

  如果```s[i-1]==t[j-1]```，最后一位相同，继续比较```s[i-2]```和```t[j-2]```，

  如果```s[i-1]!=t[j-1]```，最后一位不同，比较```s[i-1]```和```t[j-2]```，

  使用一个二维数组d保存子序列的状态，

  时间复杂度：```O(m*n)```

  空间复杂度：```O(m*n)```

- java代码：

  ```java
  class Solution {
  	public static boolean isSubsequence(String s, String t) {
          boolean[][] d = new boolean[s.length() + 1][t.length() + 1];
  
          if (s.length() > t.length()) return false;
          if (s.length() == 0) return true;
          for (int j = 0; j < t.length(); j++) { //s长度为0时，一定是t的子序列
              d[0][j] = true;
          }
  
          for (int i = 1; i <= s.length(); i++) {
              for (int j = 1; j <= t.length(); j++) {
  
                  if (s.charAt(i - 1) == t.charAt(j - 1)) {  //如果当前的最后一位相同 比较s的																前一位和t的前一位
                      d[i][j] = d[i - 1][j - 1];
  
                  } else {
                      d[i][j] = d[i][j - 1]; //否则比较s和t的前一位
                  }
              }
          }
          return d[s.length()][t.length()];
      }
}
  ```

  ![](./isSubsequence.png)
  
  其他思路参考： [isSubsequence_392](https://github.com/xiaomindog/leetcode/blob/master/java_leetcode/src/com/leetcode/easy/isSubsequence_392.java )
  
  空间复杂度可以继续优化，待更新。

###  最长回文子串

- 问题：给定一个字符串 s，找到 s 中最长的回文子串。

- DP思路：（目前想到的最优解）

  - 中心扩展法

    遍历字符串序列，遍历到第i个字符串，以i字符串为中心，向左右扩展回文串，每次保存最大长度的开始和结束坐标。需要注意的细节是，子串长度为1和2时要单独处理，具体看代码。

    这里中心的个数一般会错误认为是n，实际上是2n-1，因为2个字母中间的空格，在回文子串长度为偶数时是作为中心的。

     时间复杂度：```O(n^2)```

     空间复杂度：```O(1)```

- java代码：

  ```java
  class Solution {
      public String longestPalindrome(String s) {
          //按中心展开 共有2n-1个中心 如果字母数为偶数，每2个元素之间的空格也要算为1个中心
          
          if(s.length()==0) return "";
          
          int start = 0, end = 0;
          for (int i = 0; i < s.length(); i++) {
              int len1 = expend_from_center(s, i, i); // 考虑单个字母时候的len
              int len2 = expend_from_center(s, i, i + 1); //考虑2个字母为回文时候的len
              int len = Math.max(len1, len2);  //保存较长的
  
              if (len > end - start) {
                  start = i - (len - 1) / 2;
                  end = i + len / 2;
              }
          }
          return s.substring(start, end+1);
      }
  
      //以某个字母向两边扩展
      public int expend_from_center(String s, int left, int right) {
          while ((left >= 0 && right < s.length() && s.charAt(left) == s.charAt(right))) {
              left--;
              right++;
          }
          return right - left - 1;
      }
  }
  ```
  
  ![](./longestPalindrome.png)

###  回文子串个数

- 问题： 给定一个字符串，计算这个字符串中有多少个回文子串。 

- DP思路：

  中心扩展法，思路同最长回文子串，基本只要加个计数器即可。

  时间复杂度：```O(n^2)```

  空间复杂度：```O(1)```

- java代码：

  ```java
  class Solution {
      int num = 0;
      public int countSubstrings(String s) {
          for (int i=0; i < s.length(); i++){
              count(s, i, i);//单个字母肯定是回文，只做了一次num++
              count(s, i, i+1);
          }
          return num;
      }
      
      public void count(String s, int start, int end){
          while(start >= 0 && end < s.length() && s.charAt(start) == s.charAt(end)){
              num++;
              start--;
              end++;
          }
      }
  
  }
  ```

  ![](./countSubstrings.png)

###  最长回文子序列

-  问题：给定一个字符串 s，找到其中最长的回文子序列。 

- DP思路：

  用二维数组```dp[n][n]```表示长度为 n 的字符串 s 的状态矩阵，```dp[i][j]```表示 i 到 j 之间字符串最长  回文子序列长度，

  状态转移方程：

  如果 ```s[i]==s[j]```：```dp[i][j]=dp[i+1][j-1]+2```

  如果 ```s[i]!=s[j]```：```dp[i][j]=MAX(dp[i+1][j],dp[i][j+1])```

  注意：i 要从后往前遍历

  时间复杂度：```O(n^2)```

  空间复杂度：```O(n^2)```

- java代码：

  ```java
  class Solution {
      //动态规划 二维数组作为状态矩阵 下三角稀疏 可优化
      public static int longestPalindromeSubseq(String s) {
          if (s.length() == 0) return 0;
          int dp[][] = new int[s.length()][s.length()];
          //想清楚为什么从后往前遍历 保证了i,j之间的子序列的状态的更新
          for (int i = s.length() - 1; i >= 0; i--) {
              dp[i][i] = 1;//单个字母的最长回文子序列一定是1
              for (int j = i + 1; j < s.length(); j++) {
                  if (s.charAt(i) == s.charAt(j)) {
                      dp[i][j] = dp[i + 1][j - 1] + 2;
                  } else {
                      dp[i][j] = Math.max(dp[i + 1][j], dp[i][j - 1]);
                  }
              }
          }
          //输出dp数据测试
  //        for (int i = 0; i < s.length(); i++) {
  //            for (int j = 0; j < s.length(); j++) {
  //                System.out.print(dp[i][j]);
  //            }
  //            System.out.println();
  //        }
  
          return dp[0][s.length() - 1];
      }
  }
  ```

  ![](./longestPalindromeSubseq1.png)

- 压缩二维矩阵为一维矩阵，空间复杂度为```O(n)``` 代码：

  ```java
  class Solution {
      //二维数据降为一维数组
      public int longestPalindromeSubseq(String s) {
          if (s.length() == 0) return 0;
  
          int[] pre = new int[s.length()]; // 前一步状态矩阵
          int[] cur = new int[s.length()]; //当前状态矩阵 最后一个元素永远保存当前的最大回文子序列																					长度
          for (int i = s.length() - 1; i >= 0; i--) {
              cur[i] = 1;
              for (int j = i + 1; j < s.length(); j++) {
                  if (s.charAt(i) == s.charAt(j)) {
                      cur[j] = pre[j - 1] + 2;
                  } else {
                      cur[j] = Math.max(pre[j], cur[j - 1]);
                  }
              }
              int[] temp = pre;
              pre = cur;
              cur = temp;
          }
          return pre[s.length() - 1];
      }
  }
  ```

  ![](./longestPalindromeSubseq2.png)

  

  

  

  

  

  

  

  