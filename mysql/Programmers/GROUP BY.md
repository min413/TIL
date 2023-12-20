# GROUP BY

### 즐겨찾기가 가장 많은 식당 정보 출력하기
https://school.programmers.co.kr/learn/courses/30/lessons/131123
```mysql
SELECT food_type, rest_id, rest_name, favorites
from rest_info
where (food_type, favorites) in (select food_type, max(favorites)
                                from rest_info
                                group by food_type)
order by food_type desc ;
```
서브 쿼리에서 여 조건 (다중 조건)을 동시에 만족하는 것을 사용하려면 = 대신 in을 사용해야 한다.  
in 연산자를 사용하면 러서브 쿼리 결과에서 여러 행, 여러 칼럼과 동시에 비교할 수 있다.  
= 연산자는 단일값 비교, in 연산자는 값 리스트에 포함된 값을 검사하기 때문에 여러 칼럼을 비교할 때 사용한다.  
  
서브 쿼리로 음식 종류별 최대 값을 찾기 위해 종류로 그룹화 한 후 max 집계 함수를 이용한다.  
이것을 전체 테이블을 돌면서 비교한다.  
서브 쿼리의 두 가지 조건을 동시에 만족해야 하기 때문에 괄호로 묶어준 다음 in으로 확인한다.  

---

### 자동차 종류 별 특정 옵션이 포함된 자동차 수 구하기
https://school.programmers.co.kr/learn/courses/30/lessons/151137
```mysql
SELECT car_type, count(*) as cars
from car_rental_company_car
where options regexp '통풍시트|열선시트|가죽시트'
group by car_type
order by car_type ;
```
regexp로 정규표현식으로 like와 같은 기능을 한다 (%통풍시트% 이런식으로)  

---

### 조건에 맞는 사용자와 총 거래금액 조회하기
https://school.programmers.co.kr/learn/courses/30/lessons/164668
```mysql
SELECT U.user_id, U.nickname, sum(B.price) as total_sales
from used_goods_board as B join used_goods_user as U on B.writer_id = U.user_id
where B.status = 'DONE'
group by U.user_id
having sum(B.price) >= 700000
order by total_sales ;
```
group by와 having 절, 그리고 두개의 테이블 join까지 모든 것을 다뤄보는 문제였다.  
특히 나는 status가 DONE 된 것을 고려해야 한다는 점을 놓쳐서 처음 풀었을 때 틀렸었다.  
문제를 자세히 읽고 적용하도록 하자.  

---



