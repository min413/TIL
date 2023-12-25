# String Date
  
### 자동차 대여 기록에서 장기/단기 대여 구분하기
https://school.programmers.co.kr/learn/courses/30/lessons/151138
```mysql
SELECT history_id, car_id, date_format(start_date, '%Y-%m-%d') as start_date, date_format(end_date, '%Y-%m-%d') as end_date, case when datediff(end_date , start_date) + 1 >= 30 then '장기 대여' else '단기 대여' end as rent_type
from car_rental_company_rental_history
where date_format(start_date, '%Y-%m') = '2022-09'
order by history_id desc ;
```
(자잘하게 배워갈 내용이 많았다)  
case when [조건] then 문자열 else 문자열 end  
datediff(날짜1, 날짜2) 에서 계산은 "날짜1 - 날짜2"로 한다.  

---

### 조건에 맞는 사용자 정보 조회하기
https://school.programmers.co.kr/learn/courses/30/lessons/164670
```mysql
SELECT U.user_id, U.nickname, concat(U.city, ' ', concat(U.street_address1, ' ', U.street_address2)) as '전체주소', 
concat(left(U.TLNO, 3), '-', mid(U.TLNO, 4,4), '-', right(U.TLNO, 4)) as '전화번호'
from used_goods_board as B join used_goods_user as U on B.writer_id = U.user_id
group by B.writer_id
having count(B.writer_id) >= 3
order by U.user_id desc;

-- 이렇게도 풀린다

SELECT user_id, nickname, concat(city, ' ', street_address1, ' ', street_address2) as '전체주소', concat(left(TLNO, 3), '-', mid(TLNO, 4,4), '-', right(TLNO, 4)) as '전화번호'
from used_goods_user
where user_id in (select writer_id
                 from used_goods_board
                 group by writer_id
                 having count(writer_id) >= 3)
order by user_id desc; 
```
left(문자열, 왼쪽에서 문자열 개수), mid(문자열, 시작위치(맨 처음은 1), 개수), right(문자열, 오른쪽에서 문자열 개수)  
이중 sql문을 적용했다.  

---

### 조회수가 가장 많은 중고거래 게시판의 첨부파일 조회하기
https://school.programmers.co.kr/learn/courses/30/lessons/164671
```mysql
SELECT concat('/home/grep/src/', F.board_id, '/', F.file_id, F.file_name, F.file_ext) as fild_path
from used_goods_board as B join used_goods_file as F on B.board_id = F.board_id
where B.views = (select max(views)
                from used_goods_board)
order by F.file_id desc;
```

---

### 자동차 대여 기록 별 대여 금액 구하기
https://school.programmers.co.kr/learn/courses/30/lessons/151141
```mysql
SELECT H.history_id, round(C.daily_fee * (100 - ifnull(P.discount_rate, 0)) / 100 * (datediff(H.end_date, H.start_date) + 1)) as fee
from CAR_RENTAL_COMPANY_CAR as C join CAR_RENTAL_COMPANY_RENTAL_HISTORY as H on C.car_id = H.car_id and C.car_type = '트럭' left join CAR_RENTAL_COMPANY_DISCOUNT_PLAN as P on C.car_type = P.car_type and datediff(H.end_date, H.start_date) + 1 >= cast(p.duration_type as UNSIGNED)
group by H.history_id
order by fee desc, H.history_id desc ;
```
from 안에 and로 시작해서 특정 조건을 정의한다.  
이거 level 4라 많이 어려웠다.  

---

### 조건에 부합하는 중고거래 상태 조회하기
https://school.programmers.co.kr/learn/courses/30/lessons/164672
```mysql
SELECT board_id, writer_id, title, price, case when status = 'sale' then '판매중' when status = 'reserved' then '예약중' when status = 'done' then '거래완료' end as status
from used_goods_board
where date_format(created_date, '%Y-%m-%d') = '2022-10-05'
order by board_id desc;
```
case when ~ then ~ when ~ then ~ ... end  

---

### 대여 기록이 존재하는 자동차 리스트 구하기
https://school.programmers.co.kr/learn/courses/30/lessons/157341
```mysql
SELECT distinct C.car_id
from CAR_RENTAL_COMPANY_CAR as C join CAR_RENTAL_COMPANY_RENTAL_HISTORY as H on C.car_id = H.car_id
where date_format(H.start_date, '%m') = '10' and C.car_type = '세단'
order by C.car_id desc;
```
"시작한 기록이 있는" 부분을 놓쳐서 잠깐 헤맸다.  

---

### 조건별로 분류하여 주문상태 출력하기
https://school.programmers.co.kr/learn/courses/30/lessons/131113
```mysql
SELECT order_id, product_id, date_format(out_date,'%Y-%m-%d') as out_date, case when out_date <= '2022-05-01' then '출고완료' when out_date > '2022-05-01' then '출고대기' else '출고미정' end as 출고여부
from food_order
order by order_id asc;
```
출고 일자인 out_date로 해야했다.  
날짜 비교할때는 그냥 있는 그대로 out_date 쓰고 '2022-05-01'로 비교해도 된다.  

---

### 자동차 평균 대여 기간 구하기
https://school.programmers.co.kr/learn/courses/30/lessons/157342
```mysql
SELECT car_id, round(avg(datediff(end_date, start_date) + 1), 1) as average_duration
from CAR_RENTAL_COMPANY_RENTAL_HISTORY
group by car_id
having avg(datediff(end_date, start_date) + 1) >= 7
order by average_duration desc, car_id desc ;
```
group by로 묶고 조건은 having으로 한다.  
round(값, 숫자)에서 숫자는 화면에 표시하고 싶은 숫자다.  
즉, 소수점 두번째에서 반올림하고 싶으면 화면에 소수점 첫번째까지 표시되기 때문에 round(값, 1)로 하면 된다.  

---

### 취소되지 않은 진료 예약 조회하기
https://school.programmers.co.kr/learn/courses/30/lessons/132204
```mysql
SELECT A.apnt_no, P.pt_name, P.pt_no, D.mcdp_cd, D.dr_name, A.apnt_ymd as apnt_ymd
from patient as P join appointment as A on P.pt_no = A.pt_no join doctor as D on D.dr_id = A.mddr_id 
where A.mcdp_cd = 'CS' and date_format(A.apnt_ymd, '%Y-%m-%d') = '2022-04-13' and A.apnt_cncl_yn = 'N'
order by A.apnt_ymd asc ;
```
"2022-04-13에" => apnt_ymd가 해당 날짜이고,  
"취소되지 않은" => apnt_cncl_yn이 'N'이어야 한다.  
그냥 문제의 의도를 제대로 이해하지 못했음.  

---

### 루시와 엘라 찾기
https://school.programmers.co.kr/learn/courses/30/lessons/59046
```mysql
SELECT animal_id, name, sex_upon_intake
from animal_ins
where name in ('Lucy','Ella','Pickle','Rogan','Sabrina','Mitty')
order by animal_id ;
```
어떤 문자들 중 속하는 것을 찾으려면 괄호로 묶어서 여러개 나열하고 in을 하면 된다.  

---
