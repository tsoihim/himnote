### 개요
실시간으로 갱신이 일어나는 데이터의 배열에 대한 구간합, 최소값 등을 효율적으로 구할 수 있는 자료구조
이진 트리를 기반으로 구현되며 위 연산들을 LogN 시간 안에 처리 가능

### 동작 방식
배열의 부분합을 저장하는 Segment Tree는 아래와 같은 구조를 가짐
- 실제 배열은 {5, 8, 6, 3, 2, 7, 2, 6}의 데이터를 가짐
- 이진 트리 형태로 각 구간의 합을 기록

![[Pasted image 20250612114222.png|300]]

위 트리는 실제 배열에 아래와 같이 저장될 수 있음

![[Pasted image 20250612114238.png|500]]

위와 같은 구조를 가지고, 기능상 필요한 함수는 아래와 같음
- 트리 구축
	- 최초 데이터를 가지고 세그먼트 트리를 구성
	- build(int[] arr, int node, int start, int end)
- 배열 값 갱신
	- 배열 내 값을 갱신하고 그에 따라 트리 값 갱신
	- update(int index, int newValue, int node, int start, int end)
		- index와 node의 차이에 유의
- 구간합 탐색
	- 세그먼트 트리를 순회하며 특정 구간의 합을 검색
	- querySum(int left, int right, int node, int start, int end)

위 함수들은 재귀적으로 동작하도록 구현

### 구현 예제
```Java
class Solution { // Grok AI를 기반으로 작성된 코드
    private int N;
    private int[] sumTree;

    public void init(int N, int mSubscriber[]) {
        this.N = N;
        sumTree = new int[4 * N];
        build(mSubscriber, 0, 0, N-1);
    }

    // 트리 구축 (재귀적으로 구간합 계산)
    private void build(int[] arr, int node, int start, int end) {
        if (start == end) { // 리프 노드: 배열의 원소 저장
            sumTree[node] = arr[start];
            return;
        }
        int mid = (start + end) / 2; // 왼쪽/오른쪽 자식 노드 구축
        build(arr, 2 * node + 1, start, mid);
        build(arr, 2 * node + 2, mid + 1, end); // 부모 노드는 자식 노드의 합, 최소값, 최대값
        sumTree[node] = sumTree[2 * node + 1] + sumTree[2 * node + 2];
    }

    // 값 업데이트 (index 위치의 값을 newValue로 변경)
    private void update(int index, int newValue, int node, int start, int end) {
        if (start == end) { // 리프 노드: 합 갱신
            sumTree[node] = newValue;
            return;
        }

        int mid = (start + end) / 2;
        if (index <= mid) { // index가 왼쪽 구간에 속하면 왼쪽 자식 탐색
            update(index, newValue, 2 * node + 1, start, mid);
        } else { // 오른쪽 구간에 속하면 오른쪽 자식 탐색
            update(index, newValue, 2 * node + 2, mid + 1, end);
        }

        // 부모 노드 갱신
        sumTree[node] = sumTree[2 * node + 1] + sumTree[2 * node + 2];
    }

    // 구간합 쿼리 (구간 [left, right]의 합 계산)
    private int querySum(int left, int right, int node, int start, int end) {
        if (right < start || end < left) { // 구간이 벗어나면 0 반환
            return 0; 
        }

        if (left <= start && end <= right) { // 구간이 포함되면 노드 값 반환
            return sumTree[node];
        }

        int mid = (start + end) / 2;
        int leftSum = querySum(left, right, 2 * node + 1, start, mid);
        int rightSum = querySum(left, right, 2 * node + 2, mid + 1, end);
        return leftSum + rightSum;
    }
    
    public void update(int index, int newValue) {
        update(index, newValue, 0, 0, N-1);
    }
    
    public int count(int start, int end) {
        return querySum(start, end, 0, 0, N-1);
    }
}
```
