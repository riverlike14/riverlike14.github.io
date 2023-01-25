---
title: "[Problem Solving] CP/PS 기록"
---

개인적인 CP/PS 과정을 기록하는 포스팅.

# 정보

ID: riverlike14

## 현재 정보

Last Updated: 2023년 1월 25일.

||Rating|Rank|Level|etc.|Last Updated|
|:-|-:|-:|:-|:-:|:-|
|Codeforces|536|108798|Newbie||Jan/10/2023|
|AtCoder|1154|8984|5 Kyu||Jan/21/2023|
|solved.ac|2239|667|Diamond V|Class 6|Jan/25/2023|
|Backjoon||||solved 1254|Jan/25/2023|

## 초기 정보

2023년 1월 17일 기록.

||Rating|Rank|Level|etc.|Last Updated|
|:-|-:|-:|:-|:-:|:-|
|Codeforces|536|108798|Newbie||Jan/10/2023|
|AtCoder|1169|8747|5 Kyu||Jan/15/2023|
|solved.ac|2209|757|Diamond V|Class 6|Jan/17/2023|
|Backjoon||||solved 1230|Jan/17/2023|

# History

## 2023년 1월

### 넷째 주

1월 25일.
- [가장 긴 증가하는 부분 수열 6](https://www.acmicpc.net/problem/17411)
  - 난이도: Platinum 2
  - 결과: 성공
  - 시도: 2
  - LIS를 세그먼트 트리로 푸는 아이디어를 활용. 그리고 세그먼트 트리에 LIS의 개수도 같이 저장하고 업데이트.
- [증가 수열의 개수](https://www.acmicpc.net/problem/17409)
  - 난이도: Platinum 4
  - 결과: 성공
  - 시도: 1
  - 마찬가지로 LIS를 세그먼트 트리로 푸는 아이디어를 활용. 수열의 길이마다 세그먼트 트리를 만들어 주면 됨. 세그먼트 트리와 DP의 조화를 느낄 수 있는 문제라 했지만 나는 고생만 한 기억밖에...
- [서로 다른 수와 쿼리 1](https://www.acmicpc.net/problem/14897)
  - 난이도: Platinum 2
  - 결과: 10min, 성공
  - 시도: 1
  - 좌표만 압축해주면 [수열과 쿼리 5](https://www.acmicpc.net/problem/13547)랑 똑같은 문제.

1월 24일.
- [수열과 쿼리 5](https://www.acmicpc.net/problem/13547)
  - 난이도: Platinum 2
  - 결과: 성공
  - 시도: 1
  - 새로 배운 개념. 모스 알고리즘을 바로 적용하면 됨.
- [수열과 쿼리 6](https://www.acmicpc.net/problem/13548)
  - 난이도: Platinum 1
  - 결과: 성공
  - 시도: 2
  - 모스 알고리즘에 세그먼트 트리로 풀었음. 다른 풀이를 보니 단순히 원소별 등장 횟수가 아니라 등장 횟수를 누적시켜서 기록함. 예를 들어, 5가 3번 등장하면 `count[1]`, `count[2]`, `count[3]` 모두 1씩 증가. 이렇게 하면 `count[]` 배열의 원소들은 자동으로 감소하는 수열이 됨. 이때 `count[]`의 인덱스 중 원소의 값이 0이 아닌 가장 큰 인덱스를 찾으면 됨.
    - 가장 큰 인덱스는 아래와 같이 빠르게 찾을 수 있음. 생각 안나면 그냥 이분탐색.
    - ```c++
      for (int base = 65536; base > 0; base >>= 1) {
        if (num + base <= 100000 && count[num + base])
          num += base;
      }
      ```

1월 23일.
- [특정한 최단 경로](https://www.acmicpc.net/problem/1504)
  - 난이도: Gold 4
  - 결과: 성공
  - 시도: 1
  - 다익스트라. 다익스트라 총 3번 돌림.
- [트리의 독립집합](https://www.acmicpc.net/problem/2213)
  - 난이도: Gold 1
  - 결과: 성공
  - 시도: 1
  - 트리에서 DP.
- [트리와 쿼리](https://www.acmicpc.net/problem/15681)
  - 난이도: Gold 5
  - 결과: 성공
  - 시도: 1
  - 트리에서 DP.
- [One More Problem About DFT](https://www.acmicpc.net/problem/21106)
  - 난이도: Ruby 5
  - 결과: 실패
  - 시도: 1
  - 푸리에 변환은 4번마다 하나의 주기를 형성. 일반적인 크기에서 DFT를 빠르게 하기만 하면 해결될 것으로 예상.

### 셋째 주

1월 22일.
- [키 순서](https://www.acmicpc.net/problem/2458)
  - 난이도: Gold 4
  - 결과: 성공
  - 시도: 1
  - 플로이드-워셜. 전날 앳코더에서 플로이드 워셜 못푼게 너무 자괴감이 들어서 푼 문제.
- [운동](https://www.acmicpc.net/problem/1956)
  - 난이도: Gold 4
  - 결과: 성공
  - 시도: 1
  - 플로이드-워셜. 
- [텔레포트](https://www.acmicpc.net/problem/16958)
  - 난이도: Gold 4
  - 결과: 성공
  - 시도: 1
  - 플로이드-워셜.
- [저울](https://www.acmicpc.net/problem/10159)
  - 난이도: Gold 3
  - 결과: 성공
  - 시도: 1
  - 플로이드-워셜.

1월 21일.
- [ABC 286](https://atcoder.jp/contests/abc286)
  - 점수: 1000
  - 등수: 1740
  - 퍼포먼스: 1107
  - 레이팅 변화: 1169 &rarr; 1154
  - A부터 D는 빨리 풀었음. E는 플로이드 워셜 생각 못하고 괴랄한 DP 짜다가 개같이 멸망. F는 중국인의 나머지 정리인데 시간 없어서 포기. E에서 말리는 바람에 F는 고민도 못함... 10분 고민하고 안되면 다음 문제로 넘어가는 태도를 갖추도록 노력. 그리고 모든 노드 사이의 거리, 혹은 연결 관계 등을 물어보면 플로이드 워셜을 떠올리는 연습을 할 것.
- [부분수열의 합 2](https://www.acmicpc.net/problem/1208)
  - 난이도: Gold 1
  - 결과: 성공
  - 시도: 3
  - 중복 고려할 것
- [트리](https://www.acmicpc.net/problem/13306)
  - 난이도: Platinum 4
  - 결과: 성공
  - 시도: 1
  - 새로 배운 개념. 오프라인 쿼리. 쿼리를 역순으로 풀면 유니온-파인드가 됨.
- [단절선](https://www.acmicpc.net/problem/11400)
  - 난이도: Platinum 4
  - 결과: 성공
  - 시도: 1
  - 부모노드와의 관계가 아니라 자식노드와의 관계를 설정. 단절점과는 달리 루트 노드에 대한 처리가 필요없음.
- [Parcel](https://www.acmicpc.net/problem/16287)
  - 난이도: Platinum 5
  - 결과: 실패
  - 시도: 1
  - 4개의 원소를 2개씩 잘 뽑은 다음 meet in the middle을 하면 될 것 같음.

1월 20일.
- [증가 수열의 개수](https://www.acmicpc.net/problem/17409)
  - 난이도: Platinum 4
  - 결과: 실패.
  - 시도: 1
  - 못 풀어서 빡침.
- [접두사 배열](https://www.acmicpc.net/problem/13322)
  - 난이도: Bronze 1
  - 결과: 성공.
  - 시도: 1
  - [증가 수열의 개수](https://www.acmicpc.net/problem/17409)를 못풀어서 빡쳐서 푼 문제.
- [Cut the Cake](https://www.acmicpc.net/problem/9487)
  - 결과: 성공
  - 시도: 1
  - 선분이 원 또는 다른 선분과 교차할 때 새로운 영역이 생기는 것을 관찰. 첫 기여.
- [2-SAT - 4](https://www.acmicpc.net/problem/11281)
  - 난이도: Platinum 3
  - 결과: 성공
  - 시도: 2
  - 타잔 알고리즘을 쓰면 강한 연결 요소들이 역순으로 위상정렬 된다는 사실 이용.
- [Dominos](https://www.acmicpc.net/problem/4196)
  - 난이도: Platinum 4
  - 결과: 성공
  - 시도: 1
  - 강한 결합 요소들을 위상정렬하고 dfs.

1월 19일.
- [Suffix Array](https://www.acmicpc.net/problem/9248)
  - 난이도: Platinum 3
  - 결과: 1h 30min, 성공
  - 시도: 1
  - 새로 배운 개념.
- [교차하지 않는 원의 현들의 최대집합](https://www.acmicpc.net/problem/2673)
  - 난이도: Platinum 4
  - 결과: 20min, 성공.
  - 시도: 1
  - DP. 쉬움.
- [달려라 홍준](https://www.acmicpc.net/problem/1306)
  - 난이도: Platinum 5
  - 결과: 20min, 성공.
  - 시도: 2
  - Deque. 덱에 구간의 최댓값 후보를 저장.

1월 18일.
- [하늘에서 떨어지는 1, 2, ..., R-L+1개의 별](https://www.acmicpc.net/problem/17353)
  - 난이도: Platinum 2
  - 결과: 15min, 성공.
  - 시도: 2
  - Lazy propagation. 앞 원소와의 차이를 저장.
- [단순 다각형](https://www.acmicpc.net/problem/3679)
  - 난이도: Platinum 4
  - 결과: 1hr, 실패.
  - 시도: 2
  - Convex Hull. 컨벡스 헐을 찾을 때 점들을 정렬하는 아이디어만 필요. 사소한 마무리.

1월 17일. 
- [Mars Maps](https://www.acmicpc.net/problem/3392)
  - 난이도: Platinum 2
  - 결과: 1hr, 실패.
  - 시도: 1
  - 스위핑, 세그먼트 트리. x좌표를 따라 움직이면서 y좌표에 지도가 있는지 없는지 확인. 테크닉 필요없는 깡세그.
