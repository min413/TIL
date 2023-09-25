# id, class, name의 차이점
---

## id, class, name 비교
|id|class|name|
|---|---|---|
|한 요소에만 스타일 지정|여러 요소에 스타일 지정|여러 요소 스타일 지정|
|#아이디|.아이디|css에서 불가|
|js에서 getElementByID에서 사용|x|form태그에서 submit 전송시 사용|
---

## id와 class의 우선순위
**id > class** 
id가 class보다 우선순위에 있다

```css
.class_tag{
    color: red;
}

#id_tag{
    color: blue;
}
```
id가 class보다 우선이라 red색이 적용됨

---

## id와 class 동시에 사용?
혼용되는 태그가 제일 먼저 적용된다

```css
.class_tag{
    color: red;
}

#id_tag{
    color: blue;
}

.class_tag#id_tag{
    color: green;
}
```

혼용되어 사용된 태그들이 제일 우선순위에 있다 => green색 적용
```css
#id_tag.class_tag{
    color: green;
}
```
이렇게 바뀌어도 똑같이 적용된다

---

## class 태그는 한번에 여러개 사용 가능
띄어쓰기로 class 태그는 한 요소를 여러개 적용 가능하다
이때 css 파일에서 가장 나중에 정의된 class의 style이 적용된다

```html
<div class="class_tag" id="id_tag">
    hahaha
</div>
<div class="class_tag another_class_tag">
    hohohohohoho
</div>
```

```css
.class_tag{
    color: red;
}

#id_tag{
    color: blue;
}

.another_class_tag{
    color: black;
}
```
이렇게 another_class_tag가 나중에 선언된다면 black이 적용됨
<br/>
css에서 선언되는 순서가 영향이 있다

```css
.another_class_tag{
    color: black;
}

.class_tag{
    color: red;
}

#id_tag{
    color: blue;
}
```
이렇게 class_tag가 나중에 선언된다면 red가 적용됨

위에 설명했던 id, class 혼용 태그가 더 우선순위로 적용된다
<br>

