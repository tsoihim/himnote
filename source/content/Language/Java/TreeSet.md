### 개요
TreeSet이란 Set 인터페이스를 구현할 클래스로, 이진 탐색 트리의 구조를 채택
- 엄밀히는 Red-Black Tree에 기반한 TreeMap 구현에 기반함
- 추가와 삭제에 시간이 조금 걸리는 대신 검색에 높은 성능을 보임
- thread 안전하지는 않음

first, last 등의 메소드를 통해 각각 Set 내 가장 작은 원소, 큰 원소를 가져올 수 있음
(아래의 예제 참고)

TreeSet을 활용하면 Set 내 데이터의 최소, 최대값을 쉽게 갱신 및 조회 가능
- TreeSet 두개를 이어 붙여서 활용하면 중간값도 확인 가능

### 코드 예제
``` Java
    public static void main(String[] args) throws Exception {
        TreeSet<Integer> treeSet = new TreeSet<>();
        for (int i=0; i<10; i++)
            treeSet.add(i);

        System.out.println(treeSet.toString());
        System.out.println(treeSet.first()); // 가장 작은 원소
        System.out.println(treeSet.last()); // 가장 큰 원소
    }
    /* 실행 결과
    [0, 1, 2, 3, 4, 5, 6, 7, 8, 9]
	0
	9
    */
    
	// 중간값이 필요한 경우 up, down 두 개의 TreeSet 사용
	// 한 Set 사이즈가 1만큼 더 크다면 중간값 바로 구할 수 있음
	// 아래와 같은 메소드로 TreeSet 업데이트 해줌
    void add(int num) {
        down.add(num);
        up.add(down.pollFirst());
        if (up.size() > down.size()) {
            down.add(up.pollLast());
        }
    }
```
- first, last 외에 pollFirst, pollLast를 호출하면 해당 원소를 pop도 같이 해줌