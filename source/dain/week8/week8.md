## 8주 공통 문제 1
* 🥇 [백준 15684 사다리 조작](https://www.acmicpc.net/problem/15684)

* 접근 방법
	* 개인적으로는 이 문제가 제일 쉬웠는데 스터디 시간에 푼 스도쿠 문제랑 제일 비슷한 문제이기 때문
  * 사다리 가로선이 3을 넘을 수 없기 때문에 최대 depth는 3까지라는 것을 기억하고 3이 넘거나 갱신된 res보다 depth가 커지면 뒤로 돌아가는 백트래킹 기법을 사용할 수 있음. 무난한 구현+백트래킹 느낌
  * 처음에는 각 세로선별로 자기 번호를 찾아갈 수 있게 최적화해줄 수 없나 생각했는데 그냥 완탐하는 게 더 쉬울 것 같고 코드 생각하기도 어려워서 그냥 구현함
  * 두 가로선이 연속하거나 접하면 안 되기 때문에 visited로 관리하면서 dfs를 계속하면 됨
  * check 함수에서는 각 시작번호가 끝번호를 잘 찾아가는지 확인하는 기능을 함
   
---
* 정답 코드
```python
def check():
  for i in range(N):
    line = i
    for j in range(H):
      if visited[j][line]:
        line += 1
      elif line > 0 and visited[j][line-1]:
        line -= 1
    if i != line:
      return False
  return True

def dfs(x, y, cnt):
  global res
  if check():
    res = min(res, cnt)
    return
  elif cnt == 3 or res <= cnt:
    return
  for i in range(x, H):
    k = y if i == x else 0
    for j in range(k, N-1):
      if not visited[i][j] and not visited[i][j+1]:
        if j > 0 and visited[i][j-1]:
          continue
        visited[i][j] = 1 # 가로선 놔보기
        dfs(x, y+2, cnt+1)
        visited[i][j] = 0 # 가로선 다시 회수

N, M, H = map(int, input().split())
visited = [[0 for _ in range(N)] for _ in range(H)]
if not M:
  print(0)
  exit(0)
for _ in range(M):
  x, y = map(int, input().split())
  visited[x-1][y-1] = 1
res = 4
dfs(0, 0, 0)
print(res if res < 4 else -1)
```
- 시간복잡도 : O(N^2)
- 공간복잡도 : O(NH)
- 문제 유형 : 구현, 백트랙킹
---
---
## 7주 공통 문제 2
* 🥇❓ [백준 1062 가르침](https://www.acmicpc.net/problem/1062)
* 접근 방법
	* 이 문제는 예전에도 풀어보려고 했는데 어려워서 포기했던 문제고 가장 어려웠음
  * 조합을 만들어서 풀어야 할 것 같다는 생각이 들긴 하는데 구체적으로 어떻게 구현해야 할지 막막했던 문제
  * 찾아보니 비트마스킹을 사용해서 풀 수도 있고 완전탐색으로 풀 수도 있어서 두 개의 풀이법을 찾아봤고 비트마스킹 채택
  * 코드를 보면서 든 생각이 아 완탐에서는 머리 굴릴 생각 말고 그냥 정직하게 다 조합을 만들어야겠구나.. 싶었다 최적화하려다가 오히려 머리가 굳어서 문제를 못 풀게 되는 상황이 생김..
  
* 검색 후 정리
  * 일단 문자를 문자 그대로 받아들이는 게 아니라 비트마스킹을 해서 처리함. a는 1, b는 10, c는 100 이런 식으로.. 그래서 ca면 101이 된다.
  * 그래서 조합마다 bit라는 변수에 그 조합의 알파벳만 비트 OR 연산을 하고 learn에 들어 있는 남극의 단어들을 얼마나 배울 수 있는지 센다.
  * 모든 조합에 이것을 시도해본 후 가장 단어를 많이 배울 수 있는 res를 갱신한다. 
---
* 정답 코드
```python
from itertools import combinations

N, K = map(int, input().split())
learn = [0 for _ in range(N)]
must = ['a', 'n', 't', 'i', 'c']
for i in range(N):
  word = list(input())[4:-4]
  for c in word:
    learn[i] |= (1 << ord(c) - ord('a'))

if K < 5:
  print(0)
  exit(0)
elif K == 26:
  print(N)
  exit(0)

alpha = 'bdefghjklmopqrsuvwxyz'
combi = list(combinations(list(alpha), K-5))
res = 0
for com in combi:
  bit = 0
  for c in must:
    bit |= (1 << ord(c) - ord('a'))
  for c in com:
    bit |= (1 << ord(c) - ord('a'))
    
  cnt = 0
  for i in range(len(learn)):
    if learn[i] & bit == learn[i]:
      cnt += 1
  res = max(res, cnt)
print(res)
```
- 시간복잡도 : O(N!)
- 공간복잡도 : O(N)
- 문제 유형 : 백트랙킹, 비트마스크

## 8주 공통 문제 3
* 🥇 [백준 1038 감소하는 수](https://www.acmicpc.net/problem/1038)
  
* 접근 방법
	* 예전에 푼 문제라서 딱히 다시 완탐 방식으로 풀지는 않았음
  * 큐를 이용했고, 수들을 문자로 바꿔 큐에 넣은 다음 앞에서 하나씩 빼서 0~9까지의 루프에 넣는다. 앞에서 뺀 문자의 맨 마지막 수를 int로 바꾼 다음 for루프의 숫자보다 작거나 같다면 감소하는 수가 될 수 없으므로 break
  * 만약 for루프의 숫자보다 크다면 for루프 숫자를 끝에 덧붙여 감소하는 수를 만들 수 있으므로, cnt를 1 증가시키고 뒤에 숫자를 덧붙여서 큐에 다시 넣음
  
---
* 정답 코드
```python
from collections import deque
N = int(input())
qu = deque(list('0123456789'))
cnt = 9
if N < 10:
  print(qu[N])
  exit(0)

while qu:
  n = qu.popleft()
  for i in range(0, 10):
    if int(n[-1]) <= i:
      break
    qu.append(n+str(i))
    cnt += 1
    if cnt == N:
      print(qu[-1])
      exit(0)

print(-1)
```
- 시간복잡도 : O(1) 
- 공간복잡도 : O(1)
- 문제 유형 : 완전탐색, 큐