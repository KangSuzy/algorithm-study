## 9주 공통 문제 1
* 3️⃣ [프로그래머스 77486 다단계 칫솔 판매](https://programmers.co.kr/learn/courses/30/lessons/77486)

* 접근 방법
	* 트리의 조상까지 쭉 찾아가는 문제
  * 특이한 점은 부모가 모든 자식의 정보를 갖고 있을 필요가 없으며 모든 노드(판매원)는 자신의 부모 정보만 갖고 있으면 된다는 점
  * people이라는 딕셔너리의 key는 판매원 이름, value는 판매원의 번호(트리에 추가된 순서), atomy라는 딕셔너리의 key는 판매원 이름, value는 해당 판매원을 조직에 넣은 사람 이름(부모)
  * seller 수만큼 for문을 돌리고, 부모가 '-'이거나 돈이 0이 될 때까지 계속 while문을 돌리는 식임
  * 이 while문에서는 자기가 갖는 돈에서 추천인에게 주는 돈을 빼서 추천인에게 돈을 입금시킴
   
---
* 정답 코드
```python
def solution(enroll, referral, seller, amount):
    people = {}
    atomy = {}
    for i in range(len(enroll)):
        people[enroll[i]] = i
        atomy[enroll[i]] = referral[i]
    
    money = [0] * (len(enroll))
    for i in range(len(seller)):
        num = people[seller[i]] # 셀러의 번호
        cash = amount[i] * 100    # 셀러가 번 돈
        refer = atomy[seller[i]]    # 셀러를 조직에 넣은 사람
        money[num] += cash   # 일단 판 돈을 받는다
        while refer != '-' and cash > 0:
            give = cash // 10   # 추천인에게 주는 돈
            take = cash - give  # 자기가 갖는 돈
            cash = give # 추천인에게 남는 돈
            money[num] -= give  # 헌납하는 돈을 뺀다
            num = people[refer]
            money[num] += give
            refer = atomy[refer]
        if cash > 0:
            money[num] -= cash // 10
    return money
```
- 시간복잡도 : O(NlogN)
- 공간복잡도 : O(N)
- 문제 유형 : 트리, 구현
---
---
## 9주 공통 문제 2
* 🥇❓ [백준 21610 마법사 상어와 비바라기](https://www.acmicpc.net/problem/21610)
* 접근 방법
	* 파이어볼 문제와 비슷한데 요구 사항이 많아서 그런지 약간 더 어렵게 느껴졌음
  * 일단 방향 벡터를 1~8번 방향대로 만들기 (방향 벡터 때문에 삽질하는 경우가 많기 때문에 정신 똑바로 차리고 만들어야 함)
  * 이건 그냥.. 제시한대로 구현하면 돼서 딱히 설명할 건 없음
  * 단, Python3로 제출하면 시간 초과가 나기 때문에 Pypy3로 제출해야 했음
  
---
* 정답 코드
```python
import sys
input = sys.stdin.readline

dx = [0, -1, -1, -1, 0, 1, 1, 1]
dy = [-1, -1, 0, 1, 1, 1, 0, -1]
N, M = map(int, input().split())
board = [list(map(int, input().split())) for _ in range(N)]
move = [list(map(int, input().split())) for _ in range(M)]
cloud = [(N-2, 0), (N-2, 1), (N-1, 0), (N-1, 1)]  # 초기 구름

for d, s in move:
  d -= 1  # 방향 벡터는 실제로 0~7이므로 1 빼줌
  water = []
  for x, y in cloud:
    # 구름 이동
    nx = (x + (dx[d] * s)) % N
    ny = (y + (dy[d] * s)) % N
    board[nx][ny] += 1  # 비 내림
    water.append((nx, ny))  # 비 내린 칸 추가

  # 물복사버그
  for x, y in water:
    cnt = 0
    for i in range(1, 8, 2):  # 대각선 방향
      nx = x + dx[i]
      ny = y + dy[i]
      if nx < 0 or nx >= N or ny < 0 or ny >= N:
        continue
      if board[nx][ny] == 0:
        continue
      # 물이 있고 유효한 좌표일 경우에만 cnt 증가
      cnt += 1
    # 물복사
    board[x][y] += cnt
  
  cloud = []
  for x in range(N):
    for y in range(N):
      # 모든 칸을 돌아보고 물이 2 이상이며 물복사하지 않은 칸에 구름을 만듦
      if board[x][y] >= 2 and (x, y) not in water:
        cloud.append((x, y))
        board[x][y] -= 2  # 2를 빼줌

res = 0
for row in board:
  res += sum(row)
print(res)
```
- 시간복잡도 : ? (시간복잡도 구하는 게 의미가 없는 문제인 것 같아요)
- 공간복잡도 : O(N)
- 문제 유형 : 구현
- Pypy3 제출, 126832KB / 464ms