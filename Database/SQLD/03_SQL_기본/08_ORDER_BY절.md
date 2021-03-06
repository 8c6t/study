ORDER BY 절
========

## 1. ORDER BY 정렬

- SQL 문장으로 조회된 데이터들을 다양한 목적에 맞게 특정 컬럼을 기준으로 정렬하여 출력하는데 사용
- ORDER BY 절에 컬럼명 대신 SELECT 절에서 사용한 ALIAS 명이나 **컬럼 순서를 나타내는 정수**도 사용 가능
- ALIAS와 컬럼 순서 정수를 섞어서 사용해도 가능
- SQL 문장의 제일 마지막에 위치

### 가. 특징

- 기본적인 정렬 순서는 오름차순(ASC)
- 숫자형 데이터 타입은 오름차순으로 정렬했을 경우에 가장 작은 값부터 출력
- 날짜형 데이터 타입은 오름차순으로 정렬했을 경우 날짜 값이 가장 빠른 값이 먼저 출력
- Oracle에서는 NULL 값을 가장 큰 값으로 간주
  - 오름차순: 가장 마지막
  - 내림차순: 가장 먼저
- SQL Server에서는 NULL 값을 가장 작은 값으로 간주
  - 오름차순: 가장 먼저
  - 내림차순: 가장 마지막

## 2. SELECT 문장 실행 순서

1. FROM
2. WHERE
3. GROUP BY
4. HAVING
5. SELECT
6. ORDER BY

- 위 순서는 옵티마이저가 SQL 문장의 에러를 검사하는 순서이기도 함

## 3. ORDER BY 절에서 사용 가능한 컬럼

1. ORDER BY 절에서 SELECT 절에 정의하지 않은 컬럼을 사용할 수 있다
    - RDB가 데이터를 메모리에 올릴 때 **행 단위로 모든 칼럼을 가져오게 되므로**, SELECT 절에서 일부 칼럼만 선택하더라도 ORDER BY 절에서 메모리에 올라와 있는 다른 컬럼의 데이터를 사용할 수 있다
2. GROUP BY 절 사용 시 GROUP BY 표현식이 아닌 값은 ORDER BY 절에 사용할 수 없다


## 4. TOP-N 쿼리


### 가. ROWNUM

`SELECT 컬럼명 FROM (SELECT 컬럼명 FROM 테이블명 ORDER BY 정렬조건) WHERE ROWNUM_TOP_N_조건`

- Oracle의 경우 정렬이 완료된 후 데이터의 일부가 출력되는 것이 아니라, **데이터의 일부가 먼저 추출된 후 데이터에 대한 정렬 작업**이 일어난다
- ORDER BY절이 사용되는 경우 **Oracle은 ROWNUM 조건을 ORDER BY절보다 먼저 처리되는 WHERE 절에서 처리**하므로, 정렬 후 원하는 데이터를 얻기 위해서는 **인라인 뷰에서 먼저 데이터 정렬을 수행한 후 메인쿼리에서 ROWNUM 조건을 사용**해야 한다
- BETWEEN 조건(A부터 B까지)을 활용하고자 할 경우 ROWNUM을 이용하는 인라인 뷰가 한번 더 필요해진다
- Oracle 12c에서는 ANSI/ISO SQL:2008을 따르는 FETCH 구문이 추가되었다

### 나. TOP()

`TOP (표현식) [PERCENT] [WITH TIES]`

- SQL Server에서는 TOP 조건을 사용하게 되면 별도 처리 없이 관련 ORDER BY 절의 데이터 정렬 후 원하는 일부 데이터만 출력할 수 있다
- TOP 절을 사용하여 결과 집합으로 반환되는 행 수를 제한할 수 있다
- 표현식에는 반환할 행의 수를 지정한다(BIGINT)
- PERCENT가 지정된 경우에는 표션식이 암시적으로 FLOAT 값으로 변환되며, 쿼리 결과 집합에서 표현식% 만큼의 행만 반환된다
- WITH TIES 옵션은 ORDER BY절의 기준 조건으로 TOP N의 마지막 행으로 표시되는 추가행의 데이터가 같을 경우 N+ 동일 정렬 순서 데이터를 추가 반환하도록 지정하는 옵션
