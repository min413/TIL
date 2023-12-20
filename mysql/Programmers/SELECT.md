# SELECT

### 12세 이하인 여자 환자 목록 출력하기
https://school.programmers.co.kr/learn/courses/30/lessons/132201  
```mysql
SELECT PT_NAME, PT_NO, GEND_CD, AGE, ifnull(TLNO,'NONE') as TLNO
from PATIENT
where GEND_CD = 'W' and AGE <= 12
order by AGE desc, PT_NAME ;
```
ifnull(값, none이면 출력할 문자열) 함수로 null 데이터를 특정 문자열로 출력할 수 있다.  

---

### 평균 일일 대여 요금 구하기
https://school.programmers.co.kr/learn/courses/30/lessons/151136
```mysql
SELECT round(avg(DAILY_FEE)) as AVERAGE_FEE
from CAR_RENTAL_COMPANY_CAR
where CAR_TYPE = 'SUV' ;
```
round() 함수로 반올림 설정한다.  
만약 소수점 두번째에서 반올림하려면 round(값, 2)를 하고 그냥 round(값)은 소수점 첫째자리에서 반올림하여 정수가 출력된다.  

---

### 흉부외과 또는 일반외과 의사 목록 출력하기
https://school.programmers.co.kr/learn/courses/30/lessons/132203
```mysql
SELECT DR_NAME, DR_ID, MCDP_CD, date_format(HIRE_YMD, '%Y-%m-%d') as HIRE_YMD
from DOCTOR
where MCDP_CD = 'CS' or MCDP_CD = 'GS'
order by HIRE_YMD desc, DR_NAME asc ;
```
date_format() 함수로 특정 날짜 형식으로 출력할 수 있다.  

---
  
### 조건에 맞는 도서 리스트 출력하기
https://school.programmers.co.kr/learn/courses/30/lessons/144853
```mysql
SELECT BOOK_ID, date_format(published_date, '%Y-%m-%d') as PUBLISHED_DATE
from book
where date_format(published_date, '%Y') = '2021' and category = '인문'
order by published_date ;
```
date_format()으로 특정 년을 바로 얻어낼 수 있다.  

---
  
### 조건에 부합하는 중고거래 댓글 조회하기
https://school.programmers.co.kr/learn/courses/30/lessons/164673
```mysql
SELECT B.title, B.board_id, R.reply_id, R.writer_id, R.contents, date_format(R.created_date, '%Y-%m-%d') as created_date
from used_goods_board as B join used_goods_reply R on B.board_id = R.board_id
where B.created_date between '2022-10-01' and "2022-10-31"
order by R.created_date , B.title ;
```
두 개 이상의 테이블을 join하여 사용하려면 from에서 join ~ on 으로 이어준다.  

---

### 서울에 위치한 식당 목록 출력하기
https://school.programmers.co.kr/learn/courses/30/lessons/131118
```mysql
SELECT I.rest_id, I.rest_name, I.food_type, I.favorites, I.address, round(avg(R.review_score), 2) as score
from rest_info as I join rest_review as R on I.rest_id = R.rest_id
where I.address like '서울%'
group by I.rest_id
order by round(avg(R.review_score), 2) desc, I.favorites desc ;
```
SELECT에서 집계함수를 사용할 때는 뭘 기준으로 할 것인지 알려줘야하기 때문에 GROUP BY를 사용해야 한다.  
=> group by 사용법을 인지하자 ( + having 절도)  

---

### 재구매가 일어난 상품과 회원 리스트 구하기
https://school.programmers.co.kr/learn/courses/30/lessons/131536
```mysql
SELECT user_id, product_id
from online_sale
group by user_id, product_id
having count(*) >= 2
order by user_id, product_id desc ;
```
having 절은 group by 절과 함께 사용되어 그룹화된 결과에 조건을 부여한다.  
보통 having 절은 집계함수(count, sum, avg 등)과 함께 사용한다.  
where 절과 비슷하게 조건을 부여하는 역할을 하지만,  
where 절은 개별 레코드에 대한 조건,  
having 절은 **그룹화된** 결과 집합에 조건을 검사한다.  

여기서 user_id와 product_id를 그룹으로 만들어서 해당 그룹의 개수가 2 이상이면 출력하도록 했다.

---

### 오프라인/온라인 판매 데이터 통합하기
https://school.programmers.co.kr/learn/courses/30/lessons/131537
```mysql
SELECT date_format(sales_date, '%Y-%m-%d') as sales_date, product_id, user_id, sales_amount
from online_sale
where date_format(sales_date, '%Y-%m-%d') between '2022-03-01' and '2022-03-31'
union all
select date_format(sales_date, '%Y-%m-%d') as sales_date, product_id, NULL, sales_amount
from offline_sale
where date_format(sales_date, '%Y-%m-%d') between '2022-03-01' and '2022-03-31'
order by sales_date, product_id, user_id ;
```
union all은 쿼리 결과 값을 합쳐서 출력한다.  
그냥 union은 중복을 제거하지만 union all은 중복도 허용하여 출력한다.  

---



