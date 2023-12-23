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

