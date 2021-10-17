# Database

## 관계형 database table
- database에 필수적인 데이터 구조를 제공한다
- 여러개의 table을 포함할 수 있으며, 각 table은 특정 타입의 정보를 저장한다.
    - tuple : 하나의 항목을 대표하는 데이터
    - attribute : 한 항목의 이름
    - relation / table : 같은 attribute를 공유하는 tuple의 모임
![](https://i.imgur.com/wAOX55q.png)

## Database Schema
- 데이터베이스 테이블에 저장된 데이터의 특성을 정의
ex) 사용자 이름은 10자 이내의 문자열, 전화번호는 특정 형식의 숫자 데이터 필드...
- 데이터베이스 전체의 구조와 다양한 테이블 간의 관계를 정의
## record
- 새로운 record는 table의 row로 저장되고, 각 row는 연관된 데이터 column으로 구성된다
## Primary key
- 각 행을 고유하게 식별하는 데 사용될 수 있는 필수적인 column(1개 or 여러개 조합)

## SQLite
- embedded RDBMS(Relational Database Management System)
- app과 연결되는 library형태로 제공되기떄문에 embedded로 부른다
- 독립 실행형 데이터베이스 서버가 없고, 모든 operation은 SQLite Library에 포함된 함수의 호출을 통해 app 내부에서 처리된다
- 크리가 작아 모바일용 RDBMS로 적합하다

## Structured Query Language(SQL)
- sequel로 발음
- 대부분의 RDBMS에서 사용하는 표준 databsae language
![](https://i.imgur.com/V9REEbc.png)

## SQL Databases
- row, column을 가진 table에 data 저장
- field: row와 column의 intersection. 즉 data를 의미. 다른 table의 reference를 가질 수 있다(foreign key같은거)
- 각 row는 unique ID(==primary key)로 구분되는 하나의 entity다
- 각 column은 table당 unique한 name으로 구분된다

## SQLite
- SQLite는 다음과 같은 특징을 갖는 SQL database engine을 구현한 것
    - Self-contained : requires no other components. 다른 라이브러리에대한 의존성 갖지 않는다
    - Serverless : requires no server backend.
    - Zero-configuration : doesn't need to be configured
    - Transactional : transaction안에서 모든 변경사항과 쿼리가 격리되어 실행된다. 앱이 종료되어도 query는 실행된다
- 가장 많이 사용되는 database engine
- C언어로 작성되었지만, Android SDK는 Java기반의 wrapper를 사용해 SQLite에 접속한다

### SQLite - Serverless
- 대부분의 SQL database engine은 별도의 server process로 구현된다 -> 프로세스간 통신방식으로 서버에 요청을 보내고 결과를 반환
- SQLite: disk의 database파일에 write/read. 중간 서버 프로세스 x

#### 장점
- 설치, 설정, 구성, 초기화, 관리, troubleshootin을 위한 별도의 서버 프로세스가 없다. -> 진입장벽이 낮다
- disk에 접근할 수 있는 모든 프로그램은 SQLite 사용가능
#### 단점
- Client앱의 버그를 지원할 수 없다
- 서버가 있다면, database 접근을 보다 정밀하게 제어할 수 있다

### Entity
- database에 저장할 객체
- Entity클래스는 table을 정의하며, 각 인스턴스는 table의 row로 저장된다

### Query
- database table의 데이터를 요청
- SQL query language : database와 상호작용을 위해 사용한다 -> row 추가/삭제/갱신/검색

## SQLiteOpenHelper(library)
- database생성과 버전 업그레이드를 더 쉽게 하기 위해 설계된 helper 클래스
적용 전
![](https://i.imgur.com/E6ha5ld.png)
적용 후
![](https://i.imgur.com/CiVY8vP.png)

### Cursor
- database query의 결과에 접근하기 위해 제공되는 클래스
- SQL SELECT 명령은 일치하는 많은 행들을 반환하고, Cursor 인스턴스를 사용해 데이터에 접근할 수 있다.
![](https://i.imgur.com/rK04gYx.png)

## Room Database(library)

### Entity
- Entity는 data class로 정의한다
- interaction은 interface로 정의한다
![](https://i.imgur.com/uJTv4sm.png)

### 사용방법
- step1 : RoomDatabase 구현
- step2 : DAO 제공

### Annotated class - annotation 붙은 클래스
- metadata를 추가하기 위해 annotation 사용
- several types
     - create database
     - define tables
     - define property(primary key 정의)
     - queries
라이브러리 사용 전 후
![](https://i.imgur.com/O1vsQg7.png)
