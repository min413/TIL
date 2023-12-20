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

### 저자 별 카테고리 별 매출액 집계하기
https://school.programmers.co.kr/learn/courses/30/lessons/144856
```mysql
SELECT B.author_id, A.author_name, B.category, sum(B.price * S.sales) as sales
from book as B join author as A on B.author_id = A.author_id join book_sales as S on B.book_id = S.book_id
where date_format(S.sales_date, '%Y-%m') = '2022-01'
group by B.author_id, B.category
order by B.author_id, B.category desc ;
```
sum()으로 합을 구해야 한다.  
3개의 테이블을 join하였다.  
date_format으로 2022년 1월을 지정했다.  

---

### 자동차 대여 기록에서 대여중 / 대여 가능 여부 구분하기
https://school.programmers.co.kr/learn/courses/30/lessons/157340
```mysql
SELECT car_id, 
    case when car_id in (
        select car_id
        from car_rental_company_rental_history
        where start_date <= '2022-10-16' and '2022-10-16' <= end_date
        group by car_id
        ) then '대여중'
    else '대여 가능'
    end as AVAILABILITY
from car_rental_company_rental_history
group by car_id
order by car_id desc ;
```
특정 날짜에 있는 car_id가 있는지에 대한 내부 쿼리를 만들어서 in으로 처리한다.  
case when ~ then ~ else ~ end 문법을 이용했다.  

---

### 대여 횟수가 많은 자동차들의 월별 대여 횟수 구하기
https://school.programmers.co.kr/learn/courses/30/lessons/151139
```mysql
SELECT month(start_date) as month, car_id, count(history_id) as records
from CAR_RENTAL_COMPANY_RENTAL_HISTORY
where car_id in (select car_id
                from CAR_RENTAL_COMPANY_RENTAL_HISTORY
                where month(start_date) in (8,9,10)
                group by car_id
                having count(history_id) >= 5)
    and month(start_date) in (8,9,10)
group by month, car_id
having count(history_id) >= 1
order by month, car_id desc ;
```
조건을 추가적으로 정의하기 위해 where 절에서 또 다른 sql문을 만들었다.  
이거는 좀 어려워서 다시 풀어봐야 겠다 => 블로그에 작성하기  

---

### 식품분류별 가장 비싼 식품의 정보 조회하기
https://school.programmers.co.kr/learn/courses/30/lessons/131116
```mysql
select category, price as max_price, product_name
from food_product
where (category, price) in (select category, max(price) as price
                           from food_product
                           where category in ('과자', '국', '김치', '식용유')
                           group by category)
order by max_price desc
```
이것도 마찬가지로 내부에 또 쿼리를 정리했다.  
여기서는 식품분류가 '과자', '국', '김치', '식용유'인 경우가 따로 쿼리가 정의되었다.  
이 쿼리에서 나온 것이 (category, price)로 in 을 이용해서 나온다.  

---

