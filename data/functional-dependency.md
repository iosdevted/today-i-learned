# Database Normalization; Functional Dependency

관계형 데이터베이스의 설계 단계에서 데이터의 중복을 최소화하기 위해 데이터의 구조를 결정하는 작업을 정규화 (normalization)라고 한다. 일반적으로 데이터베이스 정규화 작업에서는 크고 제대로 조직되지 않은 테이블들과 테이블 간의 관계를 작고 잘 조직된 것으로 변경한다.

## 목적

- 불필요한 데이터를 제거, 데이터의 중복을 최소화 하기 위해서
- 데이터베이스 구조 확장 시 재디자인을 최소화
- 다양한 관점에서의 query를 지원하기 위해서
- 무결성 제약조건의 시행을 간단하게 하기 위해서
- 각종 이상 현상(Anomaly) 을 방지하기 위해서, 테이블의 구성을 논리적이고 직관적으로 한다

## 함수적 종속(Functional Dependency)

함수적 종속이란 어떤 릴레이션 R이 있을때 X와 Y를 각각 속성의 부분집합이라고 가정해봅니다. 여기서 X의 값을 알면 Y의 값을 바로 식별할 수 있고, X의 값에 Y의 값이 달라질 때, Y는 X에 함수적 종속이라고 합니다. 이 경우 X를 `결정자`, Y를 `종속자`라고 합니다. 이를 기호로 표현하면 X→Y입니다. 이런 함수적 종속관계에는 완전 함수적 종속과 부분 함수적 종속 및 이행적 함수 종속이 있습니다.

| 학번 | 이름 | 나이 | 성별 | 전공코드 | 전공명 |
|-----|-----|----|-----|--------|-------|
|11011|박지현|26|여성|AAA1|국문학과|
|11011|박지현|26|여성|C0B7|컴퓨터공학과|
|131001|김민석|25|남성|C0A5|전기전자공학과|

이 릴레이션에서는 '학번'을 알면 '이름', '나이', '성별' 속성을 식별할 수 있으며, '힉번'이 다르면 그에따른 값도 다릅니다. 따라서 '이름', '나이', '성별' 속성은 '힉번'에 함수적인 종속관계입니다. 같은 이유로 전공 속성또한 '전공코드'에 함수적인 종속관계에 있습니다.

학번→이름, 학번→나이, 학번→성별

![Screen Shot 2021-04-20 at 9 44 23 PM](https://user-images.githubusercontent.com/50784573/115397995-a3c2b880-a221-11eb-967b-a2c8d6709cdd.png)

![Screen Shot 2021-04-20 at 9 49 34 PM](https://user-images.githubusercontent.com/50784573/115398612-4c711800-a222-11eb-8310-3138db8131e8.png)

- 재귀 규칙 : Y가 X의 부분 집합이면 X → Y 이다.
- 증가 규칙 : X → Y 이면 WX → WY 이고 WX → Y 이다.
- 이행 규칙 : X → Y 이고 Y → Z 이면 X → Z 이다.
- 유니온 규칙 : X → Y 이고 X → Z 이면 X → YZ 이다.
- 분해 규칙 : X → YZ 이면 X → Y와 X → Z 이다.
- 가이행 규칙 : 만일 W → X 이고 XY → Z 이면 WY → Z 이다.

1. Réflexivité. Tout ensemble d’attributs détermine lui-même ou une partie de lui-même:

```
y ⊂ x => x → y

Exemple : Dans la table Livre précédente :

(Auteur, Titre, Genre) → Titre
```

2. Augmentation : `x → y => xz → yz`

```
Exemple : Genre → Prix peut être augmenté en 

Genre, Titre → Prix, Titre
```

3. Transitivité: `x→y & y→z => x→z`

```
Exemple : Genre → Prix & Auteur → Genre =⇒ Auteur → Prix
```

## First Normal Form (1NF)

테이블에 존재하는 필드가 모두 scalar value만을 가지며, 필드의 값이 모두 atomic 할 때, 1NF라고 한다. 여기에서 atomic 하다는 것은 테이블에 중복되는 항목이 존재하지 않아야 한다는 것과 같다. 1NF에서 "중복되는 항목이 없다"에 대한 정의는 명확한 것이 아니기 때문에 1NF에 대한 정의 또한 여러 개가 존재할 수 있다.

![Screen Shot 2021-04-20 at 9 54 56 PM](https://user-images.githubusercontent.com/50784573/115399340-0bc5ce80-a223-11eb-8b1e-f4dac88e2134.png)

## Second Normal Form (2NF)

제 2정규화를 수행 했을 경우 테이블의 모든 컬럼이 완전 함수적 종속을 만족한다.(부분 함수적 종속을 모두 제거되었다.) 이를 이해하기 위해서는 부분 함수적 종속과 완전 함수적 종속이라는 용어를 알아야 한다.

- 함수적 종속: X의 값에 따라 Y값이 결정될 때 X -> Y로 표현하는데, 이를 Y는 X에 대해 함수적 종속 이라고 한다. 예를 들어 학번을 알면 이름을 알 수 있는데, 이 경우엔 학번이 X가 되고 이름이 Y가 된다. X를 결정자이라고 하고, Y는 종속자라고 한다. 다른 말로 X가 바뀌었을 경우 Y가 바뀌어야만 한다는 것을 의미한다.

- 함수적 종속에서 X의 값이 여러 요소일 경우, 즉, {X1, X2} -> Y일 경우, X1와 X2가 Y의 값을 결정할 때 이를 완전 함수적 종속 이라고 하고, X1, X2 중 하나만 Y의 값을 결정할 때 이를 부분 함수적 종속 이라고 한다.

![Screen Shot 2021-04-20 at 10 04 42 PM](https://user-images.githubusercontent.com/50784573/115400577-6a3f7c80-a224-11eb-8154-45d7090225b5.png)

위에서 Model과 Manufacturer를 알면 Model Full Name 필드를 아예 유지하지 않거나 참조하지 않아도 결정되기 때문에, {Model, Manufacturer} -> Model Full Name 이라고 할 수 있다. 하지만 {Model, Manufacturer} -> Manufacturer Country에서 Model과 Manufacturer Country는 아무런 연관 관계가 없기 때문에, Manufacturer Country는 Manufacturer와만 종속관계에 있게 되고 이를 부분 함수 종속이라고 하게 되는 것이다. 

![Screen Shot 2021-04-20 at 10 05 49 PM](https://user-images.githubusercontent.com/50784573/115400708-91964980-a224-11eb-9ff9-0dba52edbacd.png)

## Third Normal Form (3NF)

테이블(Relation)이 제 3정규형을 만족한다는 것은 아래 두 가지 조건을 만족하는 것을 의미한다.

1. Relation이 제 2정규화 되었다.(The relation is in second normal form)
2. 기본 키(primary key)가 아닌 속성(Attribute)들은 기본 키에만 의존해야 한다.

아래는 두 번째 조건이 위반된 사례이다.

![Screen Shot 2021-04-20 at 10 11 27 PM](https://user-images.githubusercontent.com/50784573/115401434-5b0cfe80-a225-11eb-9714-cd7b5458ca92.png)


위 테이블에서 {Tournament, Year}가 후보키가 된다. 하지만 Winner Date of Birth은 기본키가 아닌 속성인 Winner를 거쳐 {Tournament, Year}에 의존하고 있는 것을 알 수 있는데, 이는 3NF를 위반한 것이 된다. 따라서 테이블을 아래와 같이 둘로 나누어 주자.

![Screen Shot 2021-04-20 at 10 11 51 PM](https://user-images.githubusercontent.com/50784573/115401482-695b1a80-a225-11eb-9cbc-3e3a4b158f44.png)

## Boyce-Codd Normal Form (BCNF)

BCNF 정규화란 제3 정규화를 진행한 테이블에 대해 모든 결정자가 후보키가 되도록 테이블을 분해하는 것이다. 예를 들어 다음과 같은 특강수강 테이블이 존재한다고 하자.

![Screen Shot 2021-04-20 at 10 20 05 PM](https://user-images.githubusercontent.com/50784573/115402652-8fcd8580-a226-11eb-9016-714a4230e8cd.png)

특강수강 테이블에서 기본키는 (학생번호, 특강이름)이다. 그리고 기본키 (학생번호, 특강이름)는 교수를 결정하고 있다. 또한 여기서 교수는 특강이름을 결정하고 있다. 
그런데 문제는 교수가 특강이름을 결정하는 결정자이지만, 후보키가 아니라는 점이다. 그렇기 때문에 BCNF 정규화를 만족시키기 위해서 위의 테이블을 분해해야 하는데, 다음과 같이 특강신청 테이블과 특강교수 테이블로 분해할 수 있다.

## Transitive Closure

![Screen Shot 2021-04-20 at 10 23 52 PM](https://user-images.githubusercontent.com/50784573/115403122-171af900-a227-11eb-8314-0ffda506bf50.png)

## Couverture minimale

![Screen Shot 2021-04-20 at 10 25 38 PM](https://user-images.githubusercontent.com/50784573/115403416-55b0b380-a227-11eb-835f-f118cb56c2ea.png)

## 추가

어떤 정규화를 만족하는지는 속성값 보단 후보키를 알아내고, 후보키를 바탕으로 추론하자. 

## References

[Illustration on Finding Candidate Key | Example #GATE​-2013 | DBMS](https://www.youtube.com/watch?v=9fuJUQJd-A8)

[Base de données - La normalisation](https://www.youtube.com/watch?v=Uc0NVAJb5Z0)

[Exercice sur la normalisation](https://www.youtube.com/watch?v=wIb68JGf8C0)

[Théorie de la normalisation relationnelle (dépendance fonctionnelle, forme normale, clé)](https://stph.scenari-community.org/bdd/nor1-pdf)

[Cours complet pour apprendre les différents types de bases de données et le langage SQL](https://sgbd.developpez.com/tutoriels/cours-complet-bdd-sql/?page=normalisation#LVIII)

[데이터베이스 정규화 개념 설명 및 예제](https://wkdtjsgur100.github.io/database-normalization/)

[데이터베이스 정규화란? (이상 문제, 함수적 종속, 정규화 과정)](https://nirsa.tistory.com/107)

[[데이터베이스]릴레이션 키 개념& 종류(기본키, 슈퍼키, 대체키, 복합키, 후보키)&특징, 유일성 최소성이란?](https://jhnyang.tistory.com/71)