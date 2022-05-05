---
layout: post
title:  "annotation의 주의점 (쿼리문과 비교)"
author: Changyun
categories: [ Django, ORM, DRF ]
image: assets/images/17.jpg
---

annotation의 주의점 (쿼리문과 비교) 실행하는 쿼리문과의 비교

# annotation의 주의점 (쿼리문과 비교)

태그: DRF, ORM

# ORM 최적화?

### 예시 ORM

```python
qs.only(
    'id', 'title', 'course__si__name_k', 'course__hotspot__name_k'
)
.filter(
    title__icontains=query,
)
.annotate(
    si_name=F('course__si__name_k'),
    hotspot_name=F('course__hotspot__name_k'),
)
.order_by('-id')
```

### 예시 Serializer

```python
class ExampleSerializer(ModelSerializer):
    si_name = CharField()
    hotspot_name = CharField()

    class Meta:
        model = Spot
        fields = ('id', 'title', 'si_name', 'hotspot_name')
```

위의 ORM과 Serializer를 보면 문제 되는것은 없어보인다.

한번 문제를 생각해보고 스크롤 다운!

---

### 문제? 문제인가..?

[힌트] 실행되는 쿼리문을 보면 차이가 있다.

```sql
SELECT `course_spot`.`id`,
       `course_spot`.`course_id`,
       `course_spot`.`title`,
       `course_si`.`name_k` AS `si_name`,
       `course_hotspot`.`name_k` AS `hotspot_name`,
       `course_course`.`id`,
       `course_course`.`si_id`,
       `course_course`.`hotspot_id`,
       `course_si`.`id`,
       `course_si`.`name_k`,
       `course_hotspot`.`id`,
       `course_hotspot`.`name_k`
FROM some_table
```

차이점을 보기 힘드니 위의 쿼리셋 only안에 있는 영역만 하이라이팅 해보면 ...

```sql
SELECT `course_spot`.`id`,
       `course_spot`.`course_id`,
       `course_spot`.`title`,
       `course_si`.`name_k` AS `si_name`,
       `course_hotspot`.`name_k` AS `hotspot_name`,
       `course_course`.`id`,
       `course_course`.`si_id`,
       `course_course`.`hotspot_id`,
       `course_si`.`id`,
       `course_si`.`name_k`,
       `course_hotspot`.`id`,
       `course_hotspot`.`name_k`
FROM some_table
```

annotation을 한만큼 중복된 열을 조회해서 출력을 해주게 된다.

```sql
SELECT `course_spot`.`id`,
       `course_spot`.`course_id`,
       `course_spot`.`title`,
       `course_si`.`name_k` AS `si_name`,
       `course_hotspot`.`name_k` AS `hotspot_name`,
       `course_course`.`id`,
       `course_course`.`si_id`,
       `course_course`.`hotspot_id`,
       `course_si`.`id`,
       `course_hotspot`.`id`
FROM some_table
```

바로 쿼리문을 작성한다면 위와 같이 작성을 대부분 할텐데

ORM annotation을 쓰면 only에서 si_name, hotspot_name 쓰고 annotation에서 한번더 사용하게 되는 문제가 있다.

~~고작 2개 차이인데 얼마나 차이가 난다고..~~ 10000개가 넘는 행을 조회하는 경우에는 **fetch time**에서의 차이가 존재한다.

## 해결방법?

annotation을 써서 문제가 발생했으니 annotation을 안쓰면 된다. what?!

우선 annotation을 지우고 ..

```python
qs.only(
    'id', 'title', 'course__si__name_k', 'course__hotspot__name_k'
)
.filter(
    title__icontains=query,
)
.order_by('-id')
```

Serializer에서 source로 지정해준다.

```python
class ExampleSerializer(ModelSerializer):
    si_name = CharField(source='course.si.name_k')
    hotspot_name = CharField(source='course.hotspot.name_k')

    class Meta:
        model = Spot
        fields = ('id', 'title', 'si_name', 'hotspot_name')
```

출력된 쿼리문을 보면 편-안하다.

```sql
SELECT 
    `course_spot`.`id`,
    `course_spot`.`course_id`,
    `course_spot`.`title`,
    `course_course`.`id`,
    `course_course`.`si_id`,
    `course_course`.`hotspot_id`,
    `course_si`.`id`,
    `course_si`.`name_k`,
    `course_hotspot`.`id`,
    `course_hotspot`.`name_k`
FROM
    `course_spot`
```

## Next

ORM에서 .only로 4개만 넣었는데 왜 SELECT문에는 10개가 있나요?