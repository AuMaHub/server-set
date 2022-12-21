# MySQL 관련 정리 내용

- [MySQL 관련 정리 내용](#mysql-관련-정리-내용)
  - [타임존 세팅](#타임존-세팅)
  - [FULLTEXT 설정](#fulltext-설정)
    - [인덱스 지정](#인덱스-지정)
    - [인덱스 삭제](#인덱스-삭제)
    - [인덱스 적용 확인](#인덱스-적용-확인)
  - [FULLTEXT 쿼리문](#fulltext-쿼리문)
  - [FULLTEXT 검색 글자 제한 수 조정](#fulltext-검색-글자-제한-수-조정)
  - [FULLTEXT 중지 단어(stopwords) 설정](#fulltext-중지-단어stopwords-설정)
  

## 타임존 세팅
1. mysql에 접속해서 타임존 상태 확인
    ```
    select @@global.time_zone, @@session.time_zone,@@system_time_zone;
    ```
    > 타임존이 System이면 서버위치 기준으로 시간이 표시됨
2. mysql 파일 접근
    ```
    sudo vim /etc/mysql/mysql.conf.d/mysqld.cnf
    ```
3. 파일 맨 아래 내용 추가
    ```
    default-time-zone="+09:00"
    ```
4. MySQL 재시작
    ```
    sudo service mysql restart
    ```
5. 시간 확인
    ```
    select now();
    ```

## FULLTEXT 설정
[참고 사이트](https://inpa.tistory.com/entry/MYSQL-%F0%9F%93%9A-%ED%92%80%ED%85%8D%EC%8A%A4%ED%8A%B8-%EC%9D%B8%EB%8D%B1%EC%8A%A4Full-Text-Index-%EC%82%AC%EC%9A%A9%EB%B2%95)
### 인덱스 지정
1. 인덱스 지정 방법 1
    ```
    CREATE TABLE <테이블명>(
        …,
        열이름 데이터형식,
        …,
        FULLTEXT <인덱스명> (<컬럼명>)
    );
    ```
2. 인덱스 지정 방법 2
    ```
    CREATE FULLTEXT INDEX <인덱스명> ON <테이블명> (<컬럼명>);
    ```
3. 인덱스 지정 방법 3
    ```
    ALTER TABLE <테이블명> ADD FULLTEXT (<컬럼명>);
    ```

### 인덱스 삭제
1. 인덱스 삭제 방법 1
   ```
   ALTER TABLE <테이블명> DROP INDEX FULLTEXT (<컬럼명>);
   ```

### 인덱스 적용 확인
1. 인덱스 적용 확인 방법 1
   ```
   SHOW INDEX FROM <테이블명>;
   ```

## FULLTEXT 쿼리문

1. 쿼리문의 기본 형식은 ``` WHERE MATCH(<컬럼명>, <컬럼명2>, ...) AGAINST('<검색내용>') ```로 자연어 검색이 디폴트로 적용됨

2. 자언어 검색
   
3. 불린 검색
   
4. 쿼리 확장 검색


## FULLTEXT 검색 글자 제한 수 조정
1. FULLTEXT 검색은 기본적으로 3글자 이상을 검색해야 결과가 나옴
   
2. 검색 글자 제한 수를 확인 하려면 ``` SHOW VARIABLES LIKE 'innodb_ft_min_token_size'; ```를 실행해서 최소 글자가 몇개인지 확인할 수 있음
   
3. 검색 글자 제한 수를 수정하려면 my.ini 혹은 my.cnf에서 ``` innodb_ft_min_token_size=<최소 글자 수> ```를 맨 아랫줄에 추가함
   
4. 저장하고 mysql을 재시작하면 적용됨


## FULLTEXT 중지 단어(stopwords) 설정
1. FULLTEXT 인덱스는 긴 문장에 대해서 인덱스를 생성하기 때문에 효율을 위해 검색이 필요 없을만한 단어들을 인덱스로 생성하지 않게 설정되어 있음
   
2. 기본으로 36개의 중지 단어가 설정되어 있으며, 아래 명령어를 통해 확인할 수 있음
   ```
   SELECT * FROM INFORMATION_SCHEMA.INNODB_FT_DEFAULT_STOPWORD;
   ```
3. 중지 단어를 추가하려면 아래 쿼리문들을 참고
    <details>
        <summary>중지 단어 추가</summary>
        
        -- 중지단어 만들기전에 만들어놓은 풀텍스트 인덱스는 삭제
        drop index idx_all on FulltextTbl ;


        -- 중지단어를 위한 테이블 만들기
        -- 테이블의 데이터는 반드시 value , 타입은 varchar -> 약속
        CREATE TABLE user_stopword (value VARCHAR(30));


        --중지 단어 만들기
        insert into user_stopword values ('그는'), ('그리고'), ('극에') ; 


        -- 중지 단어 테이블에 지금 만들 테이블을 추가하는 작업
        -- 그러면 이제 user_stopword 테이블에 들어있는 단어로는 풀텍스트 인덱스를 만들지 않게 된다
        set global innodb_ft_server_stopword_table = '디비명/user_stopword'; -- 모두 소문자


        -- 만든 테이블이 들어갔는지 확인
        show global variables like 'innodb_ft_server_stopword_table' ;


        -- 중지단어 테이블 만들었으니, 이제 다시 풀텍스트 인덱스 생성.
        create Fulltext index idx_description on FulltextTbl ( description );


        -- 그러면 중지단어에 등록한 단어를 제외한 인덱스 단어들이 생성됨 (갯수가 줄어들음)
        select word, doc_count, doc_id, position 
        from information_schema.innodb_ft_index_table ;
        
    </details>
    
4. 중지 단어를 제거하려면 my.ini 혹은 my.cnf에서 ``` SET GLOBAL innodb_ft_enable_stopword = 0; ```를 맨 아랫줄에 추가함

