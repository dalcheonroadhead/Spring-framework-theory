# 1. 문제 설명 📌

[문제링크](https://school.programmers.co.kr/learn/courses/30/lessons/42746)

문제 설명이 직관적이라 설명 생략

# 2. 접근 방식 🗃️

`KEY WORD`: `custom Sorting`

정렬 문제 였지만, 직접 정렬 알고리즘을 구현하는 문제는 아니었고, **정렬의 기준을 직접 제작하는 것**이 중요한 문제였다. 필자는 문제를 처음부터 정렬 알고리즘 직접 구현으로 가닥을 잘못 잡고 들어가서 엄청 헤멨다. 해당 내용은 `배운 것들`에 더 자세히 기술 하고 여기서는 **어떻게 접근해야 하는지**에 초점을 맞추겠다.

## (1) 정렬 기준

**Numbers** 배열의 원소 중 임의의 하나를 `A`, A의 다음 원소를 `B`라고 하자. 이 숫자들을 **문자열**이라 가정했을 때, 다음과 같이 이어 붙일 수 있을 것이다. 

- `AB` 형태
- `BA` 형태

이 두 형태 중 무엇이 큰지를 대소 관계 비교하면 된다.

- `AB` > `BA` , A가 앞에 있을 때 값이 커진다는 것임으로, A가 원래 그대로 앞에 존재한다.
- `AB` < `BA` , B가 앞에 있을 때 값이 커진다는 것임으로, B와 A의 자리를 교체하여 A가 앞으로 온다.
- `AB` == `BA`, 둘이 값이 같으므로, 굳이 자리 교체를 할 필요가 없다.

## (2) 정렬 예시

입력값: `[3, 30, 34, 5, 9]`

### `9`의 경우

답이 나오려면 정렬 후 9가 **맨 앞으로** 와야 한다. 우리가 정한 정렬기준을 지키며 정렬해 나가면 다음과 같은 과정을 거친다.

-  (1) `59` < `95`, 9와 5 자리 교체
- (2) `349` < `934`, 9와 34 자리 교체
- (3) `309` < `930`, 9와 30 자리 교체
- (4) `39` < `93`, 9와 3 자리 교체



### `30`과 `3`의 경우

정렬을 진행하다보면 맨 마지막에 진행해야할 비교는 3과 30의 비교일 것이다. `303` 보다 `330` 이 더 큼으로, **3이 30보다 앞에 선다.**

## (3) 전체 코드 흐름

(1) 입력으로 받은 int는 원시 자료형으로, `Arrays.sort()` 사용 시 **Custom**이 불가하기 때문에, `Integer` 형으로 교체

(2) 위의 예시대로, Arrays.sort()의 정렬 기준 세우기

(3) 정렬 후 String으로 이어 붙이기

# 3. 코드 소개 🔎

```java
import java.util.*;


class Solution {
    public String solution(int[] numbers) {
        Integer [] arr = new Integer [numbers.length];
        
        for(int i = 0; i < numbers.length; i++){
            arr[i] = numbers[i];
        }
        
        Arrays.sort(arr, (a, b) -> {
            String sA = Integer.toString(a);
            String sB = Integer.toString(b);
            
            String temp1 = sA+sB;
            String temp2 = sB+sA;
            
            int ab = Integer.parseInt(temp1);
            int ba = Integer.parseInt(temp2);
            
            if(ab > ba) return -1;
            else if (ab == ba) return 0;
            else return 1;
        });
        
        return join(arr);
    }
    
    public String join (Integer [] arr) {
        StringBuilder sb = new StringBuilder();
        int zero_cnt = 0;
        
        for(Integer i : arr){
            if(i == 0) zero_cnt++;
            sb.append(i);
        }
        if(zero_cnt == arr.length) {
            sb.setLength(0);
            sb.append("0");
        }
        
        return sb.toString();
    }
}
```

# 4. 배운 것들 🎯

## (1) 접근 방식에 대한 시각을 넓힘

초반에 문제를 너무 어렵게 생각했다. 

나는 자릿수로 값을 비교하는 `Radix-sort`를 변환하여, <u>맨 앞 자리수 기준 내림차순</u>으로 값을 정렬했다. 만약 맨 앞 자릿수가 동등하다면 그 다음 자릿수로 비교하였다.

**3과 30**의 경우엔, 3이 이미 1의 자리라 다음 자릿수가 없음으로, 그대로 현 위치의 자릿수를 30의 0과 비교하면 된다고 생각했다. (이렇게 구현했는데 안 풀렸으니 반례가 존재하는 듯 하다.) 
예를 들어 3과 30은 맨 앞 자릿수가 동등하므로 다음 자릿수를 비교하는데, 3은 다음 자릿수가 없음으로 그대로 3과 30의 0을 비교하여 대소관계를 가린다.

이번 문제의 답지를 보면서 많이 허탈했다. 정렬 문제들을 경험하며 느낀 것은 '이미 언어 내부에 **최적화된 정렬 알고리즘 라이브러리가 존재**하므로, **직접 구현 하는 문제는 잘 나오지 않는다.**' 는 것이다. 
정렬을 직접 해야하는 경우는 **정렬하는 과정을 필요로 하는 문제**의 경우 밖에 없는 것 같다.

## 2.  Comparator 구현 부분 새 지식

처음 코드를 짤 때, 다음과 같이 `return 0`가 나오는 경우의 수 없이 Comparator를 구현하였다. 그러자 런타임 에러가 났다.

```java
Arrays.sort(arr, (a, b) -> {
            String sA = Integer.toString(a);
            String sB = Integer.toString(b);
            
            String temp1 = sA+sB;
            String temp2 = sB+sA;
            
            long ab = Long.parseLong(temp1);
            long ba = Long.parseLong(temp2);
            
            if(ab >= ba) return -1;
            else return 1;
        });
```

이유를 찾아보니, **Comparator는 3가지 조건(1,-1,0)을 필요**로 했다. 입력값이 (A,B)일 때, 각각 A가 B보다 크다, A가 B보다 작다, 둘은 동등하다의 뜻이라고 한다. 내 경우 서로 어떨 때 동등한지 0을 반환하는 경우를 적지 않아서 런타임 에러가 난 것이었다.

### 엥? -1 == (자리 교체 하지 않는다.), 1 == (자리 교체 한다) 만 되면 되는 거 아닌가? 🤔

**Comparator**는 **대칭성**을 요구한다.

**대칭성이란?**

a가 b보다 크다면, b는 a보다 작아야 한다. 이를 Comparator 반환값에 비추어 보면, Comparator에서는 입력값 (A,B)에 대하여 A>B이면 반환값이 양수가 나와야 한다. 반대로 **(B,A)를 입력으로 넣었다면 반환값은 음수**로 나와야 한다.

> **Compare(A,B)와 Compare(B,A)는 부호가 반대여야 한다.**

이 규칙을 볼 때, 나의 첫 코드는 대칭성의 규칙을 지키지 않았다. 그래서 런타임 에러가 난 것이다.
