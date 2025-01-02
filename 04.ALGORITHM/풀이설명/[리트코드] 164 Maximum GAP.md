# 1. 문제 설명 📌

[문제 링크](https://leetcode.com/problems/maximum-gap/?envType=problem-list-v2&envId=radix-sort)

배열을 정렬해서, 인접한 두 값의 차의 최대값을 반환해라

# 2. 접근 방식 🗃️

`KEY WORD`: `RADIX SORT`

해당 문제는 java의 native 정렬 알고리즘을 활용해도 되지만, `기수 정렬(Radix-sort)`를 활용해보고 싶어서, 해당 알고리즘을 활용해서 문제를 풀어보았다. 

그저 정의 그대로 구현한 것 뿐이라, 따로 접근 방식을 설명할 것이 없다. Radix-sort에 대해 모르시는 분들은 다음 링크에 내용이 적혀 있으니 한번 보고 오는 것을 추천 드린다.

https://dalcheonroadhead.tistory.com/558

# 3. 코드 소개 🔎

```java
import java.util.*;

class Solution {
    public int maximumGap(int[] nums) {
        
        if(nums.length < 2) return 0;
        int [] arr = new int [nums.length + 1];
        for(int i = 0; i < nums.length; i++){
            arr[i+1] = nums[i];
        }
        radix_sort(arr, 0);
        int max_GAP = 0;
        for(int i = 2; i < arr.length; i++){
            max_GAP = Math.max(max_GAP, arr[i]-arr[i-1]);
        }

        return max_GAP;
    }

    private void radix_sort (int [] arr, int digit) {
        int [] nums = new int [10];
        int [] result = new int [arr.length];
        // 1. 현 자릿수를 통해 자릿수 배열 만들기
        for (int i = 1; i < arr.length; i++){
            nums[(int) (arr[i] / (Math.pow(10, digit))) % 10]++;
        }
        // 0. 기저 조건
        if(digit == 9) return;

        // 2. 누적합 만들기
        for (int i = 1; i < nums.length; i++) {
            nums[i] += nums[i-1];
        }
        // 3. 자릿수 배열을 활용해 값 정렬
        for (int i = arr.length-1; i >= 0; i--) {
            int now_digit = (int)(arr[i]/Math.pow(10,digit))%10;
            result[nums[now_digit]] = arr[i];
            nums[now_digit]--;
        }

        for (int i = 1; i < result.length; i++) {
            arr[i] = result[i];
        }
        radix_sort(arr, digit+1);
    }

}
```

# 4. 배운 것들 🎯

## (1) 착각하고 있던 기저 조건

처음엔 기저 조건을

```java
if(nums[0] == arr.length -1) return;
```

으로 만들었다. 즉 **'모든 수가 현재 자릿수를 체크했을 때, 값이 0이면 종료한다.'** 의 뜻이다. 언뜻 보면 맞는 것 같다. 예를 들어 `[1,2,3,4,5,6]`이 있다고 치면 10의 자릿수를 확인할 때부터 모든 수의 자릿수가 0이 되므로 더 이상 계산할 필요가 없다는 것을 보여주기 때문이다. 하지만 **리트 코드를 통해 다음 반례를 찾을 수 있었다.**

`[1, 10000000]` 
해당 입력값은, 10의 자리에서 모든 원소의 자릿수가 0이 된다. 1의 자리에서 정렬 했을 시 `[10000000, 1]`이 되어버림으로, 정렬을 10의 자리에서 멈추면 오답이 되어버린다! 

**다시 생각해보니** 기저 조건에 골머리를 쌀 필요가 없었다. `기수 정렬`의 장점은 최대 자릿수 만큼 반복하면 계산이 끝난다는 것이다. 따라서 재귀는 **최고 자릿수 + 1** 까지만 해주면 된다. 따라서 문제에서 주어지는 최대값만 체크하면 되겠다.