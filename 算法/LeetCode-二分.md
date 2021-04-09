[TOC]

## 搜索旋转排序数组（0033 && 0081）

> *升序排列的整数数组 nums 在预先未知的某个点上进行了旋转（例如， [0,1,2,4,5,6,7] 经旋转后可能变为 [4,5,6,7,0,1,2] ）。*
>
> *请你在数组中搜索 target ，如果数组中存在这个目标值，则返回它的索引，否则返回 -1 。*
>
> *输入：nums = [4,5,6,7,0,1,2], target = 0*
>
> *输出：4*

```java
class Solution {
    public int search(int[] nums, int target) {
        int n = nums.length;
        if (n == 0) {return -1;}
        if (n == 1) {return nums[0] == target ? 0 : -1;}
        int left = 0, right = n - 1;

        while (left <= right) {
            int mid = left + (right - left) / 2;
            if (nums[mid] == target) {return mid;}
            if (nums[left] <= nums[mid]) {
                if (nums[left] <= target && nums[mid] > target) {
                    right = mid - 1;
                }
                else {
                    left = mid + 1;
                }
            }
            else {
                if (nums[mid] < target && nums[right] >= target) {
                    left = mid + 1;
                } 
                else {
                    right = mid - 1;
                }
            }
        }
        return -1;
    }
}
```

> *假设按照升序排序的数组在预先未知的某个点上进行了旋转。*
>
> *( 例如，数组 [0,0,1,2,2,5,6] 可能变为 [2,5,6,0,0,1,2] )。*
>
> *编写一个函数来判断给定的目标值是否存在于数组中。若存在返回 true，否则返回 false。*

```java
class Solution {
    public boolean search(int[] nums, int target) {
        int n = nums.length;
        if (n == 0) {return false;}
        if (n == 1) {return nums[0] == target;}
        int left = 0, right = n - 1;

        while (left <= right) {
            int mid = left + (right - left) / 2;
            if (nums[mid] == target) {return true;}
            if (nums[left] == nums[mid]) {
                left++;
                continue;
            }
            if (nums[left] <= nums[mid]) {
                if (nums[left] <= target && target < nums[mid]) {
                    right = mid - 1;
                }
                else {
                    left = mid + 1;
                }
            }
            else {
                if (nums[mid] < target && target <= nums[right]) {
                    left = mid + 1;
                }
                else {
                    right = mid - 1;
                }
            }
        }

        return false;
    }
}
```

## 在排序数组中查找元素的第一个和最后一个位置（0034）

> *给定一个按照升序排列的整数数组 nums，和一个目标值 target。找出给定目标值在数组中的开始位置和结束位置。*
>
> *如果数组中不存在目标值 target，返回 [-1, -1]。*
>
> *输入：nums = [5,7,7,8,8,10], target = 8*
>
> *输出：[3,4]*

```java
class Solution {
    public int[] searchRange(int[] nums, int target) {
        int leftIndex = binarySearch(nums, target, true);
        int rightIndex = binarySearch(nums, target, false);
        if (leftIndex <= rightIndex && rightIndex < nums.length && nums[leftIndex] == target && nums[rightIndex] == target) {
            return new int[]{leftIndex, rightIndex};
        }
        return new int[]{-1, -1};
    }

    public int binarySearch(int[] nums, int target, boolean lower) {
        int left = 0, right = nums.length - 1;
        while (left <= right) {
            int mid = (left + right) / 2;
            if (nums[mid] > target || (lower && nums[mid] >= target)) {
                right = mid - 1;
            }
            else {
                left = mid + 1;
            }
        }
        return lower ? left : right;
    }
}
```

## 寻找旋转排序数组中的最小值（0153）

> *假设按照升序排序的数组在预先未知的某个点上进行了旋转。例如，数组 [0,1,2,4,5,6,7] 可能变为 [4,5,6,7,0,1,2] 。*
>
> *请找出其中最小的元素。*

```java
class Solution {
    public int findMin(int[] nums) {
        int left = 0;
        int right = nums.length - 1;
        while (left < right) {
            int mid = left + (right - left) / 2;
            if (nums[mid] < nums[right]) {
                right = mid;
            } else if (nums[mid] > nums[right]) {
                left = mid + 1;
            } else {
                right -= 1;
            }
        }
        return nums[left];
    }
}
```

## x的平方根（0069）

> *实现 int sqrt(int x) 函数。*

```java
class Solution {
    public int mySqrt(int x) {
        int left = 0, right = x;
        int ans = -1;
        while (left <= right) {
            int mid = left + (right - left) / 2;
            if ((long)mid * mid <= x) {
                ans = mid;
                left = mid + 1;
            }
            else {
                right = mid - 1;
            }
        }
        return ans;
    }
}
```

## 第一个错误的版本（0278）

> *假设你有 n 个版本 [1, 2, ..., n]，你想找出导致之后所有版本出错的第一个错误的版本。*

```java
public class Solution extends VersionControl {
    public int firstBadVersion(int n) {
        int left = 1, right = n;
        while (left < right) {
            int mid = left + (right - left) / 2;
            if (isBadVersion(mid)) {
                right = mid;
            }
            else {
                left = mid + 1;
            }
        }
        return left;
    }
}
```

## 寻找重复数（0287）

> *给定一个包含 n + 1 个整数的数组 nums ，其数字都在 1 到 n 之间（包括 1 和 n），可知至少存在一个重复的整数。*
>
> *假设 nums 只有 一个重复的整数 ，找出 这个重复的数 。*

```java
class Solution {
    public int findDuplicate(int[] nums) {
        int n = nums.length;
        int left = 1, right = n - 1;

        while (left <= right) {
            int mid = left + (right - left) / 2;
            int count = 0;
            for (int i = 0; i < n; i++) {
                if (nums[i] <= mid) {
                    count++;
                }
            }

            // 等价于找右边界
            if (count <= mid) {
                left = mid + 1;
            }
            else {
                right = mid - 1;
            }
        }
        return right + 1;
    }
}
```

## 有序数组中第K小的元素（0378）

> *给你一个 n x n 矩阵 matrix ，其中每行和每列元素均按升序排序，找到矩阵中第 k 小的元素。*

```java
class Solution {
    public int kthSmallest(int[][] matrix, int k) {
        int n = matrix.length;
        int left = matrix[0][0];
        int right = matrix[n - 1][n - 1];
        while (left < right) {
            int mid = left + ((right - left) >> 1);
            if (check(matrix, mid, k, n)) {
                right = mid;
            } else {
                left = mid + 1;
            }
        }
        return left;
    }

    public boolean check(int[][] matrix, int mid, int k, int n) {
        int i = n - 1;
        int j = 0;
        int num = 0;
        while (i >= 0 && j < n) {
            if (matrix[i][j] <= mid) {
                num += i + 1;
                j++;
            } else {
                i--;
            }
        }
        return num >= k;
    }
}
```

## 反转字符串中的元音字母（0345）

> *编写一个函数，以字符串作为输入，反转该字符串中的元音字母。*

```java
class Solution {
    private final static HashSet<Character> vowels = new HashSet<>(Arrays.asList('a', 'e', 'i', 'o', 'u', 'A', 'E', 'I', 'O', 'U'));

    public String reverseVowels(String s) {
        if (s.length() == 0) {return s;}
        int left = 0, right = s.length() - 1;
        char[] c = new char[s.length()];

        while (left <= right) {
            char c1 = s.charAt(left);
            char c2 = s.charAt(right);
            if (!vowels.contains(c1)) {
                c[left++] = c1;
            }
            else if (!vowels.contains(c2)) {
                c[right--] = c2;
            }
            else {
                c[left++] = c2;
                c[right--] = c1;
            }
        }

        return String.valueOf(c);
    }
}
```

## 有序数组中第K小的元素（0378）

> *给你一个 n x n 矩阵 matrix ，其中每行和每列元素均按升序排序，找到矩阵中第 k 小的元素。*

```java
class Solution {
    public int kthSmallest(int[][] matrix, int k) {
        int n = matrix.length;
        int left = matrix[0][0];
        int right = matrix[n - 1][n - 1];
        while (left < right) {
            int mid = left + ((right - left) >> 1);
            if (check(matrix, mid, k, n)) {
                right = mid;
            } else {
                left = mid + 1;
            }
        }
        return left;
    }

    private boolean check(int[][] matrix, int mid, int k, int n) {
        int i = n - 1, j = 0;
        int count = 0;
        while (i >= 0 && j < n) {
            if (matrix[i][j] <= mid) {
                count += (i + 1);
                j++;
            }
            else {
                i--;
            }
        }
        return count >= k;
    }
}
```

## 翻转对（0493）

> 给定一个数组 nums ，如果 i < j 且 nums[i] > 2*nums[j] 我们就将 (i, j) 称作一个重要翻转对。
>
> 你需要返回给定数组中的重要翻转对的数量。
>

```java
class Solution {
    public int reversePairs(int[] nums) {
        if (nums.length == 0) {return 0;}
        return myMethod(nums, 0, nums.length - 1);
    }

    private int myMethod(int[] nums, int left, int right) {
        if (left == right) {return 0;}
        else {
            int mid = left + (right - left) / 2;
            int countLeft = myMethod(nums, left, mid);
            int countRight = myMethod(nums, mid + 1, right);
            int ans = countLeft + countRight;

            int i = left, j = mid + 1;
            while (i <= mid) {
                while (j <= right && (long)nums[i] > 2 * (long)nums[j]) {j++;}
                ans += j - mid - 1;
                i++;
            }

            int[] sorted = new int[right - left + 1];
            int p1 = left, p2 = mid + 1;
            int p = 0;
            while (p1 <= mid || p2 <= right) {
                if (p1 > mid) {
                    sorted[p++] = nums[p2++];
                }
                else if (p2 > right) {
                    sorted[p++] = nums[p1++];
                }
                else {
                    if (nums[p1] < nums[p2]) {
                        sorted[p++] = nums[p1++];
                    }
                    else {
                        sorted[p++] = nums[p2++];
                    }
                }
            }

            for (int k = 0; k < sorted.length; k++) {
                nums[left + k] = sorted[k];
            }
            return ans;
        }
    }
}
```

