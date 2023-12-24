# JOIN
  
### 주문량이 많은 아이스크림들 조회하기
https://school.programmers.co.kr/learn/courses/30/lessons/133027
```mysql
SELECT F.flavor
from first_half as F join july as J on F.flavor = J.flavor
group by F.flavor
order by sum(J.total_order + F.total_order) desc limit 3 ;
```
그냥 조인 느낌으로 하고 맛을 그룹으로 두고 total_order를 더하는 걸로 하고, 상위 3개를 위해 desc limit 3을 이용했다.  
외래키를 잘 인지하여 작성하자.  

---

### 특정 기간동안 대여 가능한 자동차들의 대여비용 구하기
https://school.programmers.co.kr/learn/courses/30/lessons/157339
```mysql
SELECT C.car_id, C.car_type, 
    round((C.daily_fee * 30 / 100 * (100 - (select P.discount_rate
                                           from car_rental_company_discount_plan as P
                                           where P.car_type = C.car_type and P.duration_type = '30일 이상')))) as FEE
from car_rental_company_car as C join car_rental_company_rental_history as H on C.car_id = H.car_id
where C.car_type in ('SUV', '세단') and (C.car_id not in (select car_id
                                                       from car_rental_company_rental_history
                                                       where start_date <= '2022-11-01' and end_date >= '2022-11-01'))
group by C.car_id
having FEE >= 500000 and FEE < 2000000
order by FEE desc, C.car_type asc, C.car_id desc ;
```
데일리 요금 x 30 에다가 (100 - 할인율) / 100 을 곱해서 금액을 설정한다.  
where 절에서는 car_id에서 날짜가 11월 안이 아니면 제외하는 걸로 not in을 한다.  

---

### 5월 식품들의 총매출 조회하기
https://school.programmers.co.kr/learn/courses/30/lessons/131117
```mysql
select P.product_id, P.product_name, sum(p.price * O.amount) as 'total_sales'
from food_product as P join food_order as O on P.product_id = O.product_id
where date_format(O.produce_date, '%Y-%m') = '2022-05'
group by P.product_id
order by total_sales desc, P.product_id ;
```
총매출 부분은 그냥 price * amount에서 sum을 감싸면 된다.  

---

### 그룹별 조건에 맞는 식당 목록 출력하기
https://school.programmers.co.kr/learn/courses/30/lessons/131124
```mysql
SELECT P.member_name, R.review_text, date_format(R.review_date, '%Y-%m-%d') as review_date
from member_profile as P join rest_review as R on P.member_id = R.member_id
where R.member_id in (
    select member_id
    from rest_review
    group by member_id
    having count(member_id) = (select count(member_id)
                              from rest_review
                              group by member_id
                              order by count(member_id) desc limit 1
                              )
)
order by review_date asc, R.review_text asc ;
```
member_id가 가장 자주 언급되는 것을 찾기 위해서 먼저 첫번째 중첩 sql문은 그냥 member_id가 여기에 속하는지 in을 사용한다.  
이때 rest_review 테이블에서 group by로 member_id를 묶고, having 절로 count()를 활용한다.  
그리고 그곳에서 다시 중첩 sql문을 만들어서 member_id의 count()로 이용하여 group by로 묶고 order by로 개수를 내림차순을 한후 limit 1로 가장 최상단의 member_id의 개수를 나타낸다.  

---

