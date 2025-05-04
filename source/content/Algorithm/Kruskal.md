최소 신장 트리를 구하는데 사용할 수 있는 알고리즘

가중치별로 경로를 정렬한다음 사이클을 만들지 않는 간선을 계속 추가해나가는 방식 사용
사이클을 만들지 않기 위해서 각 정점의 parents와 ancestor를 계속 업데이트해야함
- 여기서 핵심 포인트는 오야붕 꺾기 (ex. parents[ancestor(f)] = t)

최종적으로, MST에 연결된 모든 정점이 한 오야붕을 바라보게 만들어야함

아래는 프로그래머스 예제
- https://school.programmers.co.kr/learn/courses/30/lessons/42861

```Python
def ancestor(node, parents):
    if parents[node] == node:
        return node
    else:
        return ancestor(parents[node], parents)

def solution(n, costs):
    answer = 0    
    bridge = 0
    edges = sorted(costs, key = lambda x:x[2])
    parents = [i for i in range(n)]
    
    for i, edge in enumerate(edges):
        front = edge[0]
        back = edge[1]
        weight = edge[2]
            
        if ancestor(front, parents) != ancestor(back, parents): 
            parents[ancestor(back, parents)] = front # Point
            answer += weight
            bridge += 1

        if bridge == n-1:
            break
    
    return answer
```