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

### 
