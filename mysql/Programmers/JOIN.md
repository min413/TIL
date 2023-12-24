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

### 없어진 기록 찾기
https://school.programmers.co.kr/learn/courses/30/lessons/59042
```mysql
SELECT O.animal_id, O.name
from animal_ins as I right join animal_outs as O on I.animal_id = O.animal_id
where I.animal_id is null ;
```
animal_outs에는 있고 animal_ins에는 없는 기록을 찾기 위해서 테이블끼리 join할 때 animal_outs로 join하고,  
I.animal_id가 null인 경우의 id와 name을 출력하면 된다.  

---

### 있었는데요 없었습니다
https://school.programmers.co.kr/learn/courses/30/lessons/59043
```mysql
SELECT I.animal_id, I.name
from animal_ins as I join animal_outs as O on I.animal_id = O.animal_id
where I.datetime > O.datetime
order by I.datetime asc ;
```
날짜가 더 나중일수록 크다고 판단한다.  
여기서 나는 date_format으로 날짜를 바꿔서 비교해봤으나 틀렸다.  
그냥 있는 날짜 그대로 비교하면 된다.  

---

### 오랜 기간 보호한 동물(1)
https://school.programmers.co.kr/learn/courses/30/lessons/59044
```mysql
SELECT I.name, I.datetime
from animal_ins as I left join animal_outs as O on I.animal_id = O.animal_id
where O.datetime is null
order by I.datetime limit 3 ;
```
위의 "없어진 기록 찾기" 문제와 비슷하게 풀면 된다.  
animal_outs에는 없고 animal_ins에만 있는 기록을 찾기 위해 테이블끼리 join할 때 animal_ins로 join한다.  
=> 즉, 기록이 있는 쪽으로 join한다.  
그리고 없는 쪽의 데이터가 is null 인것을 확인한다.  
즉, O.datetime 이 is null일 때를 where 절에 넣는다.  

---

### 보호소에서 중성화한 동물
https://school.programmers.co.kr/learn/courses/30/lessons/59045
```mysql
SELECT I.animal_id, I.animal_type, I.name
from animal_ins as I join animal_outs as O on I.animal_id = O.animal_id
where I.animal_id in (select animal_id
                    from animal_ins
                    where sex_upon_intake like 'Intact%')
      and O.animal_id in (select animal_id
                        from animal_outs
                        where sex_upon_outcome like 'Spayed%' or sex_upon_outcome like 'Neutered%')
order by I.animal_id ;
```
입양 들어갈 때의 성별이 Intact로 시작하는 animal_id가 있는지 in을 이용했고,  
입양 나갔을 때의 성별이 Spayed 또는 Neutered로 시작하는 animal_id가 있는지도 in을 이용했다.  

---

### 상품을 구매한 회원 비율 구하기
https://school.programmers.co.kr/learn/courses/30/lessons/131534
```mysql
SELECT year(S.sales_date) as year, month(S.sales_date) as month, count(distinct S.user_id) as puchased_users, round(count(distinct S.user_id) / (select count(*)
                                            from user_info
                                            where year(joined) = '2021'), 1) as puchased_ratio
from user_info as I join online_sale as S on I.user_id = S.user_id
where year(I.joined) = '2021'
group by year(S.sales_date), month(S.sales_date)
order by year, month ;
```
2021년에 구매한 사람 수 (online_sale 테이블에 있는 user_id 수)에서 user_info의 전체 사람 수를 나눈거를 round()로 관리한다.  
이때 round()의 두번째 인자는 해당 숫자에서 반올림을 한다는 뜻이다.  
