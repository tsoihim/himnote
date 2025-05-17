## **Query Parameter**
`/users?**id**=123 # 아이디가 123인 사용자를 가져온다.`
id 변수를 받아 쿼리를 수행

## **Path Variable**
`/users/123 # 아이디가 123인 사용자를 가져온다.`
URI 자체를 변수로서 사용

## **Query Parameter vs Path Variable**
관례적으로는 리소스 식별에 path variable을 사용하고, 정렬이나 필터링 등에 query param을 많이 사용
`/users # 사용자 목록을 가져온다. /users?occupation=programer # 프로그래머인 사용자 목록을 가져온다. /users/123 # 아이디가 123인 사용자를 가져온다.`