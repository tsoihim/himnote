그래프의 한 정점에서 다른 정점까지의 최소거리를 구하는 알고리즘
- 그리디하므로, 가중치가 있는 경우 완전탐색보다 먼저 적용해볼 것
- 음의 가중치가 있는 경우 적용 불가

아래 두 원리가 핵심
- 시작 노드에서부터 다음 방문 노드 중 최단거리가 가장 작은 노드를 선택하는 것을 반복
- 노드를 돌아가며 거리가 단축되는 경우 값을 갱신
	- 따라서 방문 처리는 하지 않음
	- 방문처리를 한다면, poll 이후에 방문처리하여 최단거리 갱신이 가능하도록 해야함

heapq 이용한 우선순위큐 사용하여 구현 가능

아래는 백준 예제
- https://www.acmicpc.net/problem/1584

```Python
import heapq
import sys

matrix = [[0 for i in range(501)] for j in range(501)]

for i in range(int(input())):
    x1, y1, x2, y2 = [int(_) for _ in input().split()]
    for j in range(min(x1, x2), max(x1, x2)+1):
        for k in range(min(y1, y2), max(y1, y2)+1):
            matrix[j][k] = 1

for i in range(int(input())):
    x1, y1, x2, y2 = [int(_) for _ in input().split()]
    for j in range(min(x1, x2), max(x1, x2)+1):
        for k in range(min(y1, y2), max(y1, y2)+1):
            matrix[j][k] = -1

q = []
distances = [[sys.maxsize] * 501 for i in range(501)]
distances[0][0] = 0
cases = [(1, 0), (-1, 0), (0, 1), (0, -1)]

heapq.heappush(q, (0, 0, 0))
while q:
    cost, x, y = heapq.heappop(q)

    if distances[x][y] < cost:
        continue

    for case in cases:
        _x, _y = x+case[0], y+case[1]

        if 0 <= _x <= 500 and 0 <= _y <= 500:
            if matrix[_x][_y] == -1:
                continue

            next_cost = 0
            if matrix[_x][_y] == 1:
                next_cost += 1

            if distances[_x][_y] > cost + next_cost:
                distances[_x][_y] = cost + next_cost
                heapq.heappush(q, (cost+next_cost, _x, _y))

if distances[500][500] == sys.maxsize:
    print(-1)
else:
    print(distances[500][500])
```