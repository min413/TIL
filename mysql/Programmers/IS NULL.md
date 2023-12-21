# IS NULL
  
### 경기도에 위치한 식품창고 목록 출력하기
https://school.programmers.co.kr/learn/courses/30/lessons/131114
```mysql
SELECT warehouse_id, warehouse_name, address, ifnull(freezer_yn, 'N') as freezer_yn
from food_warehouse
where address like '경기도%'
order by warehouse_id;
```
ifnull(값, 출력할 문자열) 함수로 값이 null 일 때 출력할 문자열을 정의한다.  

---

