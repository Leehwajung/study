# The NoSQL-Movement

- [Introduction](#introduction)
- [Motives and Main Drivers](#motives-and-main-drivers)
    - [Motives of NoSQL practioners](#motives-of-nosql-practioners)
    - [Theoretical work](#theoretical-work)
    - [Main Drivers](#main-drivers)
- [Criticism](#criticism)
    - [Scepticism on the Business Side](#scepticism-on-the-business-side)
    - [NoSQL as a Hype](#nosql-as-a-hype)
    - [NoSQL as Being Nothing New](#nosql-as-being-nothing-new)
    - [NoSQL Meant as a Total "No to SQL"](#nosql-meant-as-a-total-no-to-sql)
    - [Stonbraker's Critical Reception of NoSQL Database](#stonbrakers-critical-reception-of-nosql-database)
    - [Requirements of Administrators and Operators](#requirements-of-administrators-and-operators)
    - [Performance vs. Scalability](#performance-vs-scalability)
    - [Not All RDBMSs Behave like MySQL](#not-all-rdbmss-behave-like-mysql)
    - [Misconceptions of Critics](#misconceptions-of-critics)
- [Classifications and Comparisons of NoSQL Databases](#classifications-and-comparisons-of-nosql-databases)
    - [Taxonomies by Data Model](#taxonomies-by-data-model)
    - [Categorization by Ben Scofield](#categorization-by-ben-scofield)
    - [Comparison by Scalability, Data and Query Model, Persistence-Design](#comparison-by-scalability-data-and-query-model-persistence-design)

## Introduction

- 오늘날의 RDBMS
    - 웹과 비즈니스 애플리케이션에서 구조화된 데이터를 저장하는 주요 기술
    - 관계 해석(Relational Calculus)에 의존적
    - SQL에 의한 Ad Hoc Query 기능이 널리 채택됨
    - 종종 여러 클라이언트가 일관된 방식으로 접근할 수 있는 유일한 대안으로 여겨짐
- NoSQL의 등장
    - 데이터 스토어에 관해 "one size fits all"에 대한 의문에서 등장
    - NoSQL은 새로운 데이터 스토어 뿐만 아니라 그로의 움직임을 포괄함
        - 웹 개발자 사이에서 비 관계형 데이터베이스의 사용 증가를 설명하는 데 사용됨

## Motives and Main Drivers

- NoSQL 용어의 역사
    - 1998년, SQL 사용을 생략한 관계형 데이터베이스에서 처음 사용됨
    - 2009년, 비 관계형 데이터베이스 컨퍼런스에서 다시 사용됨
        - 샌프란시스코에서 NoSQL 모임을 조직한 Last.fm 개발자 Jon Oskarsson이 조직함
    - NoSQL Movement의 야망에 대해, 대안을 찾는 포인트는 관계형 데이터베이스가 적합하지 않은 문제를 해결해야 한다는 것이라고 함
        - Rackspace의 직원 Eric Evans가 기술

### Motives of NoSQL practioners

#### NoSQL 데이터 스토어를 개발하고 사용하는 이유 (Computerworld Article)

- 불필요한 복잡성 회피
    - 관계형 데이터베이스가 제공하는 다양한 기능과 엄격한 데이터 일관성은 특정 애플리케이션과 용례에서 필요 이상일 수 있음
        - e.g. Adobe의 ConnectNow
- 많은 처리량
    - 일부 NoSQL 데이터베이스는 기존 RDBMS보다 훨씬 높은 데이터 처리량 제공
        - e.g. Google의 Bigtable
- 수평적 확장성과 상용 소프트웨어상에서의 실행
    - NoSQL 데이터베이스가 해결하려는 현재의 관계형 데이터베이스의 세 가지 문제 영역 (블로거 Jonathan Ellis):
        1. 데이터 확장
        2. 단일 서버의 성능
        3. 엄격한 스키마 디자인
    - NoSQL의 해결책
        - 대부분 수평 방향으로 확장할 수 있도록 설계됨
        - 하드웨어의 가용성에 의존하지 않음
        - RDBMS 클러스터 솔루션에서 수행하는 샤딩과 같은 작업을 수행하지 않고도 머신을 추가하거나 제거할 수 있음
            - 일부 NoSQL 데이터 스토어는 자동 샤딩도 제공 (e.g. MongoDB)
- 고비용의 객체-관계 매핑 (ORM) 회피
    - 대부분의 NoSQL 데이터베이스는, 간단하거나, 관계형 데이터 구조와 비교하여 객체 지향 프로그래밍 언어와 더 유사한 데이터 구조로 저장하도록 설계됨
        - 값 비싼 객체-관계 매핑이 필요하지 않음
        - 관계형 데이터베이스의 기능으로는 거의 혜택을 볼 수 없는 복잡도가 낮은 데이터 구조의 애플리케이션에 특히 중요한 점
    - 사용자가 과중하고 반복적인 조작을 기대하는 데이터의 경우 데이터를 SQL에 매핑하는 비용은 그만한 가치가 있지만, 데이터베이스 구조가 매우 단순할 때는 SQL이 도움되지 않을 수 있음

#### NoSQL Movement의 추가적인 요인 (Blog Post on the Computerworld Article Nati Shalom)

- 데이터베이스 클러스터 설정의 복잡성과 비용이 적음
    - 'Sharding'의 복잡성과 비용 없이 PC 클러스터를 쉽고 저렴하게 확장할 수 있음
        - 큰 클러스터나 그리드에서 실행하기 위해 데이터베이스를 여러 테이블로 자르는 작업이 포함됨
- 성능 향상을 위한 신뢰성 타협
    - 더 나은 성능을 위해 신뢰성을 타협할 수 있는 경우가 있음
        - e.g. HTTP 세션 데이터 (사용자가 로그오프하면 사라지므로 영구 저장소에 저장할 필요가 없음)
- 현재의 "One size fit's it all" 데이터베이스 개념이 잘못됨
    - 전통적인 RDBMS에 대한 대안 찾기에 대한 두 가지 주요 추세:
        - 저장되는 데이터 볼륨의 지속적인 증가
        - 짧은 시간에 더 많은 양의 데이터를 처리해야 할 필요성
    - Business 및 Presentation 계층이 일관되지 않은 데이터를 탄탄하게 처리할 수 있으면 ACID가 실제로 중요하지 않음 
- 중앙 집중식 데이터 모델의 배포와 분할이 손쉬울 것이라는 근거 없는 믿음
- 프로그래밍 언어와 개발 프래임워크의 동향
- 클라우드 컴퓨팅의 요구사항
- 캐시 계층 패턴/해결 방법이 더해진 RDBMS vs. 확장성을 염두에 두고 스크래치로 구축된 시스템
- 과거의 요구 vs. 현재의 요구
    - 관계형 데이터베이스
        - 1960년대와 1970년대 데이터베이스는 크고 고사양인 단일 머신을 위해 디자인됨
        - 처음부터 관계형 데이터베이스는 분산이 아닌 중앙화된 배포를 위해 디자인됨
    - 분산 애플리케이션을 처음 구축할 때의 8가지 가정 (실제로는 틀린 것임)
        - 네트워크를 신뢰할 수 있음
        - 레이턴시가 없음
        - 대역폭이 무제한임
        - 네트워크가 안전함
        - 토폴로지가 바뀌지 않음
        - 관리자가 한 명 뿐임
        - 전송 비용이 없음
        - 네티워크가 균일함
- 관계형 데이터베이스의 오버헤드 및 메모리 사용량 감소
- 액세스를 위한 웹 기술 및 RPC 호출 사용
- 선택적 형식의 데이터 쿼리

### Theoretical work

- Michel Stonbraker의 아규
    - RDBMSs가 현재의 사용자 요구사항과 데이터베이스 시장과 다른 25년도 더 전의 하드웨어 특성을 기반으로 설계됨

#### 설계 시 고려 사항

- 메인 메모리
- 멀티 스레딩과 자원 제어
- 그리드 컴퓨팅과 Fork-Lift 업그레이드
    - 노스퀄은 확장하기 쉽다고 이해
- 높은 가용성
- 사람 개입 제거
    - 예전에는 사람이 컴퓨터보다 싸서 막 굴렸지만 이제는 아니므로 자동화가 필요함

#### 트랜잭션과 처리 및 환경에 대한 고려 사항

- 지속적인 Redo 로그는 거의 무조건 병목현상이 발생하므로 피해야 함
- DBMS 서버와 JDBC/ODBC와 같은 인터페이스 간의 통신은 성능 저하의 원인
- Undo 로그는 실제로 병목현상이 발생할 수 있음
- 동적 락킹(Locking)은 동시 엑세스를 허용하기 때문에 병목현상이 생길 수 있음
- 다중 스레드 데이터구조 때문에 트랜잭션이 래칭됨
- 성능을 저하 시키므로 가능하면 2PC (two-phase-commit) 트랜잭션을 피해야 함

#### 트랜잭션과 스키마 특성

- 트리 구조 (Schemes)
- 제약된 트리 애플리케이션 (CTA)
- 단일 사이트 트랜잭센
- 원샷 애플리케이션
- 두 단계의 트랜잭션
- 강한 두 단계의 트랜잭션
- 트랜잭션 결과의 일관성 (Commutativity)
- 무익한 트랜잭션 클래스

### Main Drivers

## Criticism

### Scepticism on the Business Side

### NoSQL as a Hype

### NoSQL as Being Nothing New

### NoSQL Meant as a Total "No to SQL"

### Stonbraker's Critical Reception of NoSQL Database

### Requirements of Administrators and Operators

### Performance vs. Scalability

### Not All RDBMSs Behave like MySQL

### Misconceptions of Critics

## Classifications and Comparisons of NoSQL Databases

### Taxonomies by Data Model

#### Stephen Yen의 분류

- Key-Value-Cache
    - Memcached
    - Repcached
    - Coherence
    - Infinispan
    - EXtreme Scale
    - Jboss Cache
    - Velocity
    - Terracoqa
- Key-Value-Store
    - keyspace
    - Flare
    - Schema Free
    - RAMCloud
- Eventually-Consistent Key-Value-Store
    - Dynamo
    - Voldemort
    - Dynomite
    - SubRecord
    - Mo8onDb
    - Dovetaildb
- Ordered-Key-Value-Store
    - Tokyo Tyrant
    - Lightcloud
    - NMDB
    - Luxio
    - MemcacheDB
    - Actord
- Data-Structures Server
    - Redis
- Tuple Store
    - Gigaspaces
    - Coord
    - Apache River
- Object Database
    - ZopeDB
    - DB4O
    - Shoal
- Document Store
    - CouchDB
    - Mongo
    - Jackrabbit
    - XML Databases
    - ThruDB
    - CloudKit
    - Perservere
    - Riak Basho
    - Scalaris
- Wide Columnar Store
    - Bigtable
    - Hbase
    - Cassandra
    - Hypertable
    - KAI
    - OpenNeptune
    - Qbase
    - KDI

#### Ken North의 분류

- Distributed Hash Table, Key-Value Data Stores
    - memcached
    - MemcacheDB
    - Project Voldemort
    - Scalaris
    - Tokyo Cabinet
- Entity-Attribute-Value Datastores
    - Amazon SimpleDB
    - Google AppEngine datastore
    - Microsoft SQL Data Services
    - Google Bigtable
    - Hadoop
    - HyperTable
    - HBase
- Amazon Platform
    - Amazon SimpleDB
- Document Stores, Column Stores
    - Sybase IQ
    - Vertica Analytic Database
    - Apache CouchDB

#### Rick Cattell의 분류

- Key-value Stores
    - Redis
    - Scalaris
    - Tokyo Tyrant
    - Voldemort
    - Riak
- Document Stores
    - SimpleDB
    - CouchDB
    - MongoDB
    - Terrastore
- Extensible Record Stores
    - Bigtable
    - HBase
    - HyperTable
    - Cassandra

### Categorization by Ben Scofield

|                      | Performance | Scalability     | Flexibility | Complexity | Functionality      |
| -------------------- | ----------- | --------------- | ----------- | ---------- | ------------------ |
| Key-Value Stores     | high        | high            | high        | none       | variable (none)    |
| Column stores        | high        | high            | moderate    | low        | minimal            |
| Document stores      | high        | variable (high) | high        | low        | variable (low)     |
| Graph databases      | variable    | variable        | high        | high       | graph theory       |
| Relational databases | variable    | variable        | low         | moderate   | relational algebra |

### Comparison by Scalability, Data and Query Model, Persistence-Design

#### 확장성

| Datastore | Add Machines Live | Multi-Datacenter Support |
| --------- | :---------------: | :----------------------: |
| Cassandra |         x         |            x             |
| HBase     |         x         |                          |
| Riak      |         x         |                          |
| Scalaris  |         x         |                          |
| Voldemort |                   |    Some code required    |

#### 데이터와 쿼리 모델

| Datastore     | Data Model   | Query API        |
| ------------- | ------------ | ---------------- |
| Cassandra     | Columnfamily | Thrift           |
| CouchDB       | Document     | map/reduce views |
| HBase         | Columnfamily | Thrift, REST     |
| MongoDB       | Document     | Cursor           |
| Neo4j         | Graph        | Graph            |
| Redis         | Collection   | Collection       |
| Riak          | Document     | Nested hashes    |
| Scalaris      | Key/value    | get/put          |
| Tokyo Cabinet | Key/value    | get/put          |
| Voldemort     | Key/value    | get/put          |

#### 영속성 (Persistence) 설계

| Datastore     | Persistence Design                  |
| ------------- | ----------------------------------- |
| Cassandra     | Memtable / SSTable                  |
| CouchDB       | Append-only B-tree                  |
| HBase         | Memtable / SSTable on HDFS          |
| MongoDB       | B-tree                              |
| Neo4j         | On-disk linked lists                |
| Redis         | In-memory with background snapshots |
| Riak          | ?                                   |
| Scalaris      | In-memory only                      |
| Tokyo Cabinet | Hash or B-tree                      |
| Voldemort     | Pluggable (primarily BDB MySQL)     |

#### 고객 요구 기반 분류

- Features-First
- Scale-First
- Simple Structure Storage
- Purpose-Optimized Storage
