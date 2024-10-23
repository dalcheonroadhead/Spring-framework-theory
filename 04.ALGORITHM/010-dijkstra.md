# 1. 개념

## (1) What? 다익스트라 알고리즘이 무엇인가요? 💡

 다익스트라 알고리즘은 한 정점에서 다른 정점으로 가는 최단 거리 비용을 구하는 알고리즘이다. 
다익스트라를 사용하면 시작 정점 S에서 `직,간접적`으로 연결된 모든 정점으로 가는 최단 거리 비용을 한 번에 구할 수 있다.

## (2) Why? 왜 해당 알고리즘을 써야 하나요? 🤔💭

해당 알고리즘을 처음 접한다면 아마 지금까지 `가중치가 없는` 그래프에 대해서 정점 간의 최단 거리를 구하는 문제만 접해왔을 가능성이 높다. `가중치가 없는` 그래프에서의 정점 간의 최단 거리는 `BFS`를 활용하면 쉽게 풀렸다. 왜냐하면 정점 사이를 잇는 간선의 개수가 곧 비용임으로 BFS를 돌렸을 때, 만나기까지 사용하는 간선의 개수만 세면 되었기 때문이다. 
하지만 `가중치가 있는` 그래프에서 최단 거리를 풀려면 더 이상 `간선의 개수` = `비용`이 아니기 때문에 BFS로 문제를 풀 수 없고, 다익스트라 등 다른 알고리즘을 활용해야한다. 오늘은 `양의 가중치만 있는 그래프`에서 활용하기 제일 쉽고 직관적인 `다익스트라`를 써서 가중치가 있는 그래프에서 최단 거리 비용을 구하겠다.

## (3) How? 어떻게 구현 하나요? 🔎

구현에 앞서 알고 있어야할 개념이 있다. 바로 `최단 거리 배열 (dist)` 이다. 

- `최단 거리 배열 (dist)`: 출발 정점을 S라 할 때, `현재 시점에서` S에서 각 정점까지의 최단 거리 비용을 저장한 배열이다. 즉 `index`: 정점 번호, `value`: 현재 시점에서 S ➜ 해당 정점까지의 최단 거리 비용 이다. 
  여기서 **현재 시점**이 중요한 이유는 다익스트라를 진행하면서 최단 거리 배열 내용을 계속 갱신해나갈 것이기 때문이다. 다익스트라를 통해 `S ➜ A`까지의 최단 거리가 갱신되면 최단 거리 배열도 갱신된다.

구현은 다음과 같이 이루어진다. 

```java
★ 구현 과정 ★
a. 시작 정점 S를 기준으로 최단 거리 배열(dist)를 만들고 초기화한다.
b. 방문하지 않은 정점 중 시작 정점 S로부터의 거리가 최소인 정점을 선택한다.
c. 현 선택한 정점에서의 간선을 활용해 최단 거리 배열 dist를 갱신한다.
d. 모든 정점을 방문할 때까지 혹은 목적지까지 방문할 때까지 b,c 과정을 반복한다.
```

밑은 각 과정에 대한 상세 설명이다. 해당 설명의 이해가 아직 벅찬 것 같이 느껴지면 스킵하고 바로 예시를 보고와도 된다.



a. **시작 정점 S를 기준으로 최단 거리 배열(dist)를 만들고 초기화** 한다. 
그렇다면 index=S인 부분만 값이 0 이고, 나머지는 값이 INF가 될 것이다. 왜냐하면 아직 계산을 진행하지 않았음으로, 자기 자신으로 가는 최단 비용이 0인 것을 제외하면 다른 정점까지의 최단 거리를 아직 모르기 때문이다. 만약 S=1이라면 다음과 같은 최단 거리 배열이 나온다. 

![image-20241009151848850](../../../../Documents/GitHub/dalcheonroadhead-github-blog/dalcheonroadhead.github.io/images/010-dijkstra/image-20241009151848850-1729672088186-1.png)

b. **방문하지 않은 정점 중 시작 정점 S로부터의 거리가 최소인 정점을 선택**한다.
맨 첫 시작일 경우 당연히 시작 정점이 선택된다. 그 후는 방문하지 않았으면서 시작 정점과의 거리가 최소인 정점을 선택해야한다. 이것을 구현하는데는 2가지 방법이 있다. 

- 첫 번째로, `방문배열을 활용`하는 방법이다. 클래식하게, 특정 정점을 사용했을 때마다, 방문 처리를 하면 된다.
-  두 번째로, `최단 거리 기준 오름차순으로 정렬된 우선순위 큐를 활용`하는 방법이다. 
  해당 방법은 최단 거리를 갱신할 때마다, 갱신된 정점의 <번호, 시작 정점에서부터의 거리>를 우선순위큐에 넣는다. 그러면 우리가 b번 단계를 할 때마다 그저 우선순위큐에서 값을 꺼내기만 하면 되므로, 첫 번째 방법보다 간편하고 속도적으로도 빠르다.(속도 비교는 뒤에 설명) 다만 몇 가지 `조건으로 거르기` 작업이 필요한데 왜냐하면 `우선순위큐에서 꺼낸 값에 대해서 이미 더 짧은 거리로 갱신된 경우`, `이미 방문해버린 정점의 경우`를 거르지 않고 계산하면 이미 처리한 부분에 대해서 중복 계산이 이루어지기 때문이다.  알고리즘 효율을 위해서 꼭 필요하다.

#### 왜 방문하지 않은 정점 중 최단 거리 정점만 고르는가? 

- 방문한 정점은 더 이상 최단 거리 갱신이 이루어지지 않는다. 
- 따라서 방문한 정점을 한 번 더 가는 것은 계산이 중복되고 무한 루프에 빠진다. 

왜 방문한 정점의 갱신이 이루어지지 않는지는 예시에서 더 설명하겠다. 



c. **현 선택한 정점에서의 간선을 활용해 최단 거리 배열 dist를 갱신한다.**
귀성길에 삼촌과 아버지의 대화를 들어본 적이 있는가? 우리 집은 새로 생긴 도로에 대해서 빠삭한 삼촌이 더 빠른 루트를 매번 알려준다. 삼촌의 말 덕분에 출발지에서 할머니집까지 가는 **최단 거리**가 갱신 되었다.

![image-20241023175014945](../../../../Documents/GitHub/dalcheonroadhead-github-blog/dalcheonroadhead.github.io/images/010-dijkstra/image-20241023175014945.png)
이 처럼 현재 방문 정점이 A이고, A에서 인접한 정점이 B라고 할 때, 
`dist[B] = Math.min(dist[B], dist[A] + A ➜ B 간선 비용)` 으로 결정된다. 원래 목적지까지 가던길이 빠르냐, 시작 정점 ➜ 현재 방문정점까지 최단거리로 찍고, 현재 정점에서 다시 목적지로 가는 게 빠르냐 저울질 한다는 소리다.

d. **모든 정점을 방문할 때까지 혹은 목적지까지 방문할 때까지 b,c 과정을 반복한다.**

 

# 2. 예시

![image-20241009151614095](../../../../Documents/GitHub/dalcheonroadhead-github-blog/dalcheonroadhead.github.io/images/다익스트라알고리즘에대하여/image-20241009151614095.png)

다음과 같은 그래프에서 다익스트라 알고리즘을 쓴다고 가정해보자. 시작 노드는 1이고, 우리가 값을 얻고 싶은 목표 노드는 6이다. 

![image-20241009151830596](../../../../Documents/GitHub/dalcheonroadhead-github-blog/dalcheonroadhead.github.io/images/다익스트라알고리즘에대하여/image-20241009151830596.png)

![image-20241009151848850](../../../../Documents/GitHub/dalcheonroadhead-github-blog/dalcheonroadhead.github.io/images/다익스트라알고리즘에대하여/image-20241009151848850.png)

시작 노드에 대해서 거리 방문 처리 및 거리 배열의 값을 0으로 세팅한다. (예시에서는 노란색으로 색칠을 방문으로 간주한다.)

![image-20241009152026768](../../../../Documents/GitHub/dalcheonroadhead-github-blog/dalcheonroadhead.github.io/images/다익스트라알고리즘에대하여/image-20241009152026768.png)

![image-20241009152203098](../../../../Documents/GitHub/dalcheonroadhead-github-blog/dalcheonroadhead.github.io/images/다익스트라알고리즘에대하여/image-20241009152203098.png)

다음과 같이 1번의 인접 노드인 2,3이 값을 최신화 한다. 방문하지 않은 노드 중에 가장 거리 배열 값이 작은 (시작 노드에서 갈 수 있는 최단 경로인) 값은 무엇인가? 답은 노드 `3`이다. 

![image-20241009153406316](../../../../Documents/GitHub/dalcheonroadhead-github-blog/dalcheonroadhead.github.io/images/다익스트라알고리즘에대하여/image-20241009153406316.png)

그럼 이제 3의 입장에서 거리배열을 갱신해보자. 방문 노드 제외하고 3에서 갈 수 있는 노드들은 2와 4번 노드이다. 
2번의 경우를 생각해보면, 1 ➜2 와 1➜3➜2 중 최단 거리가 더 짧은 비용을 택하게 될 것이다.

![image-20241009152809557](../../../../Documents/GitHub/dalcheonroadhead-github-blog/dalcheonroadhead.github.io/images/다익스트라알고리즘에대하여/image-20241009152809557.png)

2번 노드는 원래의 3이란 값이 더 최소값이라서 현행유지 할 것이다. 다음으로 1,3을 제외한 노드 중 시작 노드에서 가장 최단으로 갈 수 있는 노드는 2번임으로 2번을 선택한다.

![image-20241009153539110](../../../../Documents/GitHub/dalcheonroadhead-github-blog/dalcheonroadhead.github.io/images/다익스트라알고리즘에대하여/image-20241009153539110.png)

![image-20241009153656386](../../../../Documents/GitHub/dalcheonroadhead-github-blog/dalcheonroadhead.github.io/images/다익스트라알고리즘에대하여/image-20241009153656386.png)

방문하지 않는 노드들에 대한 갱신을 해보면, 4번 노드는 이전의 경로보다 현재 2번 노드를 통한 경로가 최단 경로라 갱신되는 것을 볼 수 있다. (2에서 3번 노드를 갱신하려고 해도, 최단 경로가 안되므로 갱신할 필요가 없다. - 이미 방문한 노드들은 방문 후 절대 값이 갱신 되지 않는다! 그 이유는 다음 장에서 설명하겠다.)

![image-20241009154050112](../../../../Documents/GitHub/dalcheonroadhead-github-blog/dalcheonroadhead.github.io/images/다익스트라알고리즘에대하여/image-20241009154050112.png)

![image-20241009154204851](../../../../Documents/GitHub/dalcheonroadhead-github-blog/dalcheonroadhead.github.io/images/다익스트라알고리즘에대하여/image-20241009154204851.png)

이제 그림으로만 진행하겠다.

![image-20241009154354715](../../../../Documents/GitHub/dalcheonroadhead-github-blog/dalcheonroadhead.github.io/images/다익스트라알고리즘에대하여/image-20241009154354715.png)

![image-20241009154447040](../../../../Documents/GitHub/dalcheonroadhead-github-blog/dalcheonroadhead.github.io/images/다익스트라알고리즘에대하여/image-20241009154447040.png)

![image-20241009154616011](../../../../Documents/GitHub/dalcheonroadhead-github-blog/dalcheonroadhead.github.io/images/다익스트라알고리즘에대하여/image-20241009154616011.png)

![image-20241009154632090](../../../../Documents/GitHub/dalcheonroadhead-github-blog/dalcheonroadhead.github.io/images/다익스트라알고리즘에대하여/image-20241009154632090.png)

도착 정점 방문을 마치는 순간 다익스트라 알고리즘은 끝난다. 1➜6 최단 경로 비용은 8이다. 

# 4. 특징

### (1) 방문한 노드의 값은 그 뒤로 갱신하려 해도 절대 갱신되지 않는다.

현 노드가 A일 때, 인접 노드를 B,C라고 하자. 이때 A에서 갈 수 있는 최단 경로의 노드가 B라서 B를 방문했다. 그렇다면 
`A ➜ B 의 비용` <= `A -> C 의 비용` 이다. 이미 방문한 노드인 B에 대한 거리 배열 값이 바뀌려면, `A ➜ C ➜ B`의 값이 `A ➜ B` 의 값보다 작아야 하는데, 가능한가? 
이미 `A ➜ C`의 비용이 `A ➜ B`보다 크거나 같은 상황인데, 거기에 양수 값이 추가된다면 절대 불가능하다.

![image-20241009155128048](../../../../Documents/GitHub/dalcheonroadhead-github-blog/dalcheonroadhead.github.io/images/다익스트라알고리즘에대하여/image-20241009155128048.png)

1 ➜ 2 (비용 1)
1 ➜ 3 ➜ 2 (비용 3+K)

만약 해당 그래프에서 이미 방문한 2번 노드에 대한 최단 경로 값이 바뀌려면, `3+k < 1` 즉 `k < -2` 여야 한다. k가 음수 값이 아닌 이상 방문한 노드에 대한 최단 경로 갱신은 이루어지지 않는다. 

### (2) 1번의 특징 때문에, 다익스트라 알고리즘은 가중치가 양의 정수인 경우만 사용할 수 있다. 

만약 음의 정수가 허용되면, 이미 방문한 노드에 대한 최단 경로 갱신이 다시 일어날 가능성이 있고, 이는 그 전에 해왔더 계산과 비교 과정을 무의미하게 만들어, 처음부터 다시 최단 경로를 계산해야 한다. 이는 무한 루프에 빠지게 한다.

# 3. 구현

## (1) 순차 탐색

저번 게시글에서 설명한 `작동원리` 그대로 구현한 방식이다.
방문한 정점과 그렇지 않은 정점 구분을 위해서, 방문배열을 사용한다. 
현재 정점에서 갈 수 있는 간선을 통해 dist[] 배열을 최신화하고, 방문 배열을 통해, 현재까지 방문하지 않은 정점 중 시작 정점에서 최단 거리로 갈 수 있는 정점을 택해야 한다. 
여기서 최악의 경우,  정점의 수가 N개라면, 매번 N번의 정점을 탐색해 다음 방문해야할 정점을 찾아야 하고, 이 행위를 모든 정점을 방문할 때까지 반복해야 하므로 최악의 시간복잡도는 `O(N^2)`이 든다. 

```java
import java.io.*;
import java.util.*;

public class 순차탐색_다익스트라 {
    static int [] dist = new int [N]; 	// N이 변수가 아니라 상수라 치자 
    static boolean [] visited = new boolean [N];
    static ArrayList<Node> [] lists;
    
     public static void main(String[] args) throws IOException {
         // 배열의 원소가 시작노드, 배열 안의 나열된 값들은 <도착노드, 가중치> 묶음
         lists = new ArrayList [N+1]; 
        
         for(int i = 1; i < N+1; i++){
             lists[i] = new ArrayList<>();
         }
         
         // 값 입력 받아서 인접 리스트 채우기 
         // ...
         Dijkstra(1);
    }
    
    public int 최단거리노드찾기() {
        int min_dist = Integer.MAX_VALUE;
        int min_idx = -1;
        
        for(int i = 0; i < N; i++){
            if(visited[i]) continue; 
            if(dist[i] < min_dist){
                min_dist = dist[i];
                min_idx = i;
            }
        }
        return min_idx;
    }
    
    // 시작노드를 인수로 받아서, 해당 노드로부터 모든 노드의 최단 거리를 dist에 입력
    public void Dijkstra(int start) {
        // 1. dist 배열 초기화 
        Arrays.fill(dist, Integer.MAX_VALUE);
        dist[start] = 0;
		visited[start] = true;
        // 2. 본 다익스트라 시작 -> 모든 노드 방문할 때까지만 반복하면 됨. 
        for(int i = 0; i < N-1; i++){
            int now_node = 최단거리노드찾기();
            visited[now_node] = true;
            
            for(int j = 0; j < lists[now_node].size(); i++){
                // 만약 현재까지 j로 가는 최단거리보다 now_node를 거쳐서 j로 가는 최단거리가 더 짧을 경우 갱신
                if(dist[j] > dist[now_node] + lists[now_node].get(j).w){
                    dist[j] = dist[now_node] + lists[now_node].get(j).w;
                }
            }
        }
    }
        
}

class Node {
    int v; // 도착 노드
    int w; // 도착 노드까지 가는 간선의 가중치 
    
    public Node(int v, int w) {
        this.v = v;
        this.w = w;
    }   
}
```

## (2) 우선순위 큐 활용

```java
★ 구현 방법 ★ 

/*
	0. 인접 리스트, 최단 거리 배열 최신화 
	
	1. 정점 객체의 시작노드로부터의 거리가 짧은 순으로 정렬되는 우선순위 큐 생성
	1-1 해당 우선순위 큐에 시작 정점을 넣고 '우선순위 큐'가 빌 때까지 반복문 시작
	
	2. Node curNode = queue.poll() 하여 현재 방문하려는 정점 꺼내기
	2-1. if(dist[curNode.idx] < curNode.cost) continue; 를 통해 유효성 확인
	2-2. 현재 방문 중인 정점이 유효하다면 이를 이용해 최단 거리 배열 갱신
	2-3. 갱신된 정점만 다시 queue에 넣기. 
*/
```

### a. 우선순위 큐의 역할

우선순위 큐는 `방문배열 대용`역할이다. 
한 번 최단 거리 배열(dist)을 갱신할 때마다 다음 방문할 노드를 O(N^2)의 시간 복잡도로 방문해야 했던 것에 비해 `시작노드로 부터 최단 거리인 Node 순으로 정렬`한 우선순위 큐를 쓴다면, 그저 queue.poll()한 값을 꺼내 쓰면 되므로 시간 복잡도면에서 크게 효율이 상승한다. (얼마나 상승했는지는 후술하겠다.) 

### b. Node 객체 속 cost의 2가지 의미

일단 Node라는 클래스를 정의하겠다.

```java
class Node {
    int idx; // 정점 번호
    int cost; // 인접리스트에서는 간선의 가중치, 다익스트라 내부에서는 현재 갱신된 dist 값
}
```

위와 같이 2가지 의미로 구분되어 쓰인다.

1. 인접리스트 구현 시 해당 Node 속 cost란 멤버변수는 간선의 가중치로 쓰임.
2. 다익스트라 내부에서 사용되는 Node 객체의 cost는 갱신된 dist 값을 가르킴. 

1번은 당연하니까 넘어가자. 2번은 무슨 의미인가?  먼저 우선순위큐 활용한 다익스트라의 예시를 보여주겠다.

 ```java
     // 시작노드를 인수로 받아서, 해당 노드로부터 모든 노드의 최단 거리를 dist에 입력
     public void Dijkstra(int start) {
         PriorityQueue<Node> pq = new PriorityQueue<>((o1,o2) -> o1.fast_cost - o2.fast_cost);
         pq.add(new Node(start, 0));
         
         while(!pq.isEmpty()){
             Node now = pq.poll();
             if(now.cost > dist[now.idx]) continue;
             for(int i = 0; i < list[now.idx].size(); i++){
                 int dest_idx = list[now.idx].get(i).idx;
                 int compare_dist = list[now.idx].get(i).weight + dist[now.idx];
                 
                 if(compare_dist < dist[dest_idx]){
                     dist[dest_idx] = compare_dist;
                     pq.add(new Node(dest_idx, compare_dist));
                 }
             }
         }
     }
 ```

여기서 맨 마지막 if문을 보면, `현재 확인 중인 정점을 활용한 거리 비용`이 `기존 비용`보다 작으면 갱신하는 모습을 볼 수 있다. 또한 새로운 Node 객체를 만들어 다시 queue에 삽입한다. 위와 같이 `다익스트라 내부 우선순위큐의 정렬기준에 맞춰 다음 정점을 찾기 위해` 여기서의 Node.cost는 현재 갱신된 최단 거리 비용을 뜻한다. 

### c. `if(dist[curNode.idx] < curNode.cost) continue` 하는 이유? 

하지만 위와 같이 최단 거리를 cost로 가진 객체를 우선순위 큐에 넣는다고 해서, 방문 배열 활용 다익스트라보다 속도면에서 효율적이라 볼 수 없다. 왜냐하면 `queue.poll()한 객체 A의 cost` > `dist[A.idx]의 cost` 일 경우 즉, 현재 방문하려는 정점이 주장하는 dist 값보다 dist 배열의 갱신된 값이 더 작다면, 
**이미 해당 정점은 이전 poll()한 객체에 의해 더 단거리 최단비용**으로 갱신되었다는 소리이다. 이미 더 작은 값으로 갱신되었는데, 현재 정점 비용으로 다시 계산하는 것은 무의미하다. 따라서 해당 경우는 넘어가도록 조치를 취해준다.

### d. 시간복잡도는? 🤔💭

우선순위 큐 활용 다익스트라는 우선순위 큐 활용 부분만 뺀다면 1단 반복문과 조건문으로만 이루어져 있기 때문에, 우선순위 큐 활용이 시간이 제일 많이 든다. 따라서 우선순위 큐 활용부분만 생각해주면 된다. N개의 값을 우선순위큐에 삽입한다면 `O(logN)`의 시간이 든다. 추출 시에도 마찬가지로 `O(logN)`의 시간이 든다.
 정점의 수를 V, 간선의 수를 E라 할 때, 삽입은 얼마나 이루어질까? 우선순위 큐 삽입은 최단 거리 배열이 갱신될 때만 이루어진다. 최악의 경우, 현재 방문한 정점에서 갈 수 있는 정점을 고려할 때마다 갱신될 것이므로 간선의 수만큼 삽입 될 것이다. 따라서 `O(ElogE)`의 시간 복잡도가 필요하다. 밀집 그래프까지 가정한다면 E <= V^2 개까지 가능하므로 O(ElogV^2), O(2*ElogV) 즉 `O(ElogV)`로도 표현이 가능하다.  
 추출은 당연히 Queue가 빌때까지 계속 되어야 한다. Queue에는 최소 정점의 개수, 최대로 많아도 간선의 최대값인 정점의 개수의 제곱을 넘지 않을 것이다.  따라서 시간복잡도는 `O(VlogV)`이다. 둘을 합치면 최종적으로 

**`O((V+E)logV)`**의 시간복잡도를 필요로 한다.

### a. 예시

1.

![image-20241009151830596](../../../../Documents/GitHub/dalcheonroadhead-github-blog/dalcheonroadhead.github.io/images/010-dijkstra/image-20241009151830596.png)

![image-20241009151848850](../../../../Documents/GitHub/dalcheonroadhead-github-blog/dalcheonroadhead.github.io/images/010-dijkstra/image-20241009151848850.png)

![image-20241014235746919](../../../../Documents/GitHub/dalcheonroadhead-github-blog/dalcheonroadhead.github.io/images/010-dijkstra/image-20241014235746919.png)

2.

![image-20241009152026768](../../../../Documents/GitHub/dalcheonroadhead-github-blog/dalcheonroadhead.github.io/images/010-dijkstra/image-20241009152026768.png)

![image-20241009152203098](../../../../Documents/GitHub/dalcheonroadhead-github-blog/dalcheonroadhead.github.io/images/010-dijkstra/image-20241009152203098.png)

![image-20241015000111965](../../../../Documents/GitHub/dalcheonroadhead-github-blog/dalcheonroadhead.github.io/images/010-dijkstra/image-20241015000111965.png)

3.

![image-20241009153028305](../../../../Documents/GitHub/dalcheonroadhead-github-blog/dalcheonroadhead.github.io/images/010-dijkstra/image-20241009153028305.png)

![image-20241009152809557](../../../../Documents/GitHub/dalcheonroadhead-github-blog/dalcheonroadhead.github.io/images/010-dijkstra/image-20241009152809557.png)

![image-20241015000324639](../../../../Documents/GitHub/dalcheonroadhead-github-blog/dalcheonroadhead.github.io/images/010-dijkstra/image-20241015000324639.png)

4.

![image-20241009153539110](../../../../Documents/GitHub/dalcheonroadhead-github-blog/dalcheonroadhead.github.io/images/010-dijkstra/image-20241009153539110.png)

![image-20241009153656386](../../../../Documents/GitHub/dalcheonroadhead-github-blog/dalcheonroadhead.github.io/images/010-dijkstra/image-20241009153656386.png)

![image-20241018224212444](../../../../Documents/GitHub/dalcheonroadhead-github-blog/dalcheonroadhead.github.io/images/010-dijkstra/image-20241018224212444.png)

5.![image-20241009154050112](../../../../Documents/GitHub/dalcheonroadhead-github-blog/dalcheonroadhead.github.io/images/010-dijkstra/image-20241009154050112.png)

![image-20241009154204851](../../../../Documents/GitHub/dalcheonroadhead-github-blog/dalcheonroadhead.github.io/images/010-dijkstra/image-20241009154204851.png)

![image-20241018224414632](../../../../Documents/GitHub/dalcheonroadhead-github-blog/dalcheonroadhead.github.io/images/010-dijkstra/image-20241018224414632.png)

이제 다음이 중요한데, 이미 방문한 정점`4`가 우선순위 큐의 front에 와있다. 하지만 잘보면 정점4까지의 최단거리라 저장해놓은 것이 `5`로 이미 최신화된 최단 거리 배열 속 dist[4]의 값보다 크다. 그래서 queue.poll() 하자마자 버린다. 

![image-20241018224638853](../../../../Documents/GitHub/dalcheonroadhead-github-blog/dalcheonroadhead.github.io/images/010-dijkstra/image-20241018224638853.png)

6.![image-20241009154354715](../../../../Documents/GitHub/dalcheonroadhead-github-blog/dalcheonroadhead.github.io/images/010-dijkstra/image-20241009154354715.png)

![image-20241009154447040](../../../../Documents/GitHub/dalcheonroadhead-github-blog/dalcheonroadhead.github.io/images/010-dijkstra/image-20241009154447040.png)

![image-20241018224845886](../../../../Documents/GitHub/dalcheonroadhead-github-blog/dalcheonroadhead.github.io/images/010-dijkstra/image-20241018224845886.png)

7.

![image-20241009154616011](../../../../Documents/GitHub/dalcheonroadhead-github-blog/dalcheonroadhead.github.io/images/010-dijkstra/image-20241009154616011.png)

![image-20241009154632090](../../../../Documents/GitHub/dalcheonroadhead-github-blog/dalcheonroadhead.github.io/images/010-dijkstra/image-20241009154632090.png)

![image-20241018224941894](../../../../Documents/GitHub/dalcheonroadhead-github-blog/dalcheonroadhead.github.io/images/010-dijkstra/image-20241018224941894.png)

이후 남은 정점 속 dist 값도 최신화된 dist 배열(최단 거리 배열) 속 dist[6]보다 값이 크므로 그냥 PASS 한다.

![image-20241018225103900](../../../../Documents/GitHub/dalcheonroadhead-github-blog/dalcheonroadhead.github.io/images/010-dijkstra/image-20241018225103900.png)

#### b. 구현

```java
import java.io.*;
import java.util.*;

public class 순차탐색_다익스트라 {
    static int N = 9;
    static int [] dist = new int [N];
    static ArrayList<Vertex> [] lists;
    
     public static void main(String[] args) throws IOException {
         // 0. initialize
         BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
         
         lists = new ArrayList [N+1]; 
         Arrays.fill(dist, Integer.MAX_VALUE);
         for(int i = 1; i < N+1; i++){
             lists[i] = new ArrayList<>();
         }
         
         int edge_cnt = Integer.parseInt(br.readLin());
         for(int i = 0; i < edge_cnt; i++){
             StringTokenizer st = new StringTokenizer(br.readLine());
             int start 	= Integer.parseInt(st.nextToken());
             int end 	= Integer.parseInt(st.nextToken());
             int cost = Integer.parseInt(st.nextToken());
             lists[start].add(new Vertex(end, cost));
         }
         
         // 1부터 모든 정점까지의 최단거리를 찾겠다.
         Dijkstra(1);
    }

    
    // 시작노드를 인수로 받아서, 해당 노드로부터 모든 노드의 최단 거리를 dist에 입력
    public void Dijkstra(int start) {
        PriorityQueue<Vertex> pq = new PriorityQueue<>((o1,o2) -> o1.fast_cost - o2.fast_cost);
        pq.add(new Vertex(start, 0));
        
        while(!pq.isEmpty()){
            Vertex now = pq.poll();
            if(now.cost > dist[now.idx]) continue;
            for(int i = 0; i < list[now.idx].size(); i++){
                int dest_idx = list[now.idx].get(i).idx;
                int compare_dist = list[now.idx].get(i).weight + dist[now.idx];
                
                if(compare_dist < dist[dest_idx]){
                    dist[dest_idx] = compare_dist;
                    pq.add(new Vertex(dest_idx, compare_dist));
                }
            }
        }
    }
    
}

class Vertex {
    int idx;		// 현 정점의 번호
    int cost; 	// 시작 정점에서 현 정점까지의 최단 거리 (큐에 집어넣을 당시의 최단거리라서 최신화된 dist 배열과 차이가 날 수 있음)
    public Vertex (int idx, int fast_cost){
        this.idx = idx;
        this.fast_cost = fast_cost;
    }
}
```

