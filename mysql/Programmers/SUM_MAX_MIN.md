# SUM MAX MIN

### 가격이 제일 비싼 식품의 정보 출력하기
https://school.programmers.co.kr/learn/courses/30/lessons/131115
```mysql
SELECT product_id, product_name, product_cd, category, price
from food_product
where price = (select max(price) from food_product) ;
```
가장 높은 가격을 구하기 위해 내부의 sql 문을 또 만들어서 비교했다.  

---

### 중복 제거하기
https://school.programmers.co.kr/learn/courses/30/lessons/59408
```mysql
SELECT COUNT(DISTINCT NAME)
FROM ANIMAL_INS
```
중복 제거하려면 distinct를 사용한다. 
