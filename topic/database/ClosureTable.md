### 클로저 테이블 (ClosureTable)

클로저 테이블은 계층 구조를 표현하기 위한 데이터베이스 테이블 설계 방식 중 하나입니다.  
각 노드의 모든 조상과 후손을 저장합니다.

<br />

### 클로저 테이블 구조
- 기본 테이블 : 실제 엔티티(댓글, 카테고리 등)를 저장하는 테이블
- 클로저 테이블 : 각 노드 간 모든 관계를 저장하는 테이블
  - ancestor_id: 조상 노드의 ID 
  - descendant_id: 자손 노드의 ID 
  - depth: 조상과 자손 간의 거리(깊이)

<br />

### 클로저 테이블은 언제 사용할까?
- 계층 구조가 자주 변경되지 않는 경우
  - 예 : 게시판 댓글, 카테고리
- 트리의 깊이가 가변적이고, 깊은 경우
- 계층 구조에서 특정 노드의 모든 자손/조상을 자주 조회하는 경우

<br />

### 클로저 테이블의 장점
- 빠른 계층 구조 탐색
  - 특정 노드의 모든 자손/조상 노드를 빠르게 조회할 수 있음
- 유연한 계층 구조 표현
  - 트리의 깊이에 관계없이 효율적으로 관리 가능

<br />

### 클로저 테이블의 단점
- 데이터 저장 비용
  - 모든 관계를 저장하기 때문에 많은 insert 작업 필요
  - 또한 메모리와 디스크 공간을 더 많이 사용
- 복잡성 증가
  - 테이블 설계 및 CRUD 로직이 복잡해질 수 있음

<br />

### 데이터가 저장되는 흐름
#### 첫번째 댓글 등록
- 댓글 ID : 1
- 클로저 테이블

| Ancestor | Descendant | Depth |
|:--------:|:----------:|:-----:|
|    1     |     1      |   0   |

#### 두번째 댓글 등록
- 댓글 ID : 2
- 클로저 테이블

| Ancestor | Descendant | Depth |
|:--------:|:----------:|:-----:|
|    2     |     2      |   0   |

#### 첫번째 댓글에 대댓글 등록
- 댓글 ID : 3
- 클로저 테이블

| Ancestor | Descendant | Depth |
|:--------:|:----------:|:-----:|
|    1     |     3      |   1   |
|    3     |     3      |   0   |

#### 대댓글에 대대댓글 등록
- 댓글 ID : 4
- 클로저 테이블

| Ancestor | Descendant | Depth |
|:--------:|:----------:|:-----:|
|    1     |     4      |   2   |
|    3     |     4      |   1   |
|    4     |     4      |   0   |

<br />

### 결론

자주 변경되지 않는 유연한 계층 구조를 설계 후, 각 노드의 자손/조상을 자주 조회해야 할 상황이라면 클로저 테이블 설계를 고려해 볼 만합니다.

<br />

### 관련 코드
- https://github.com/khghouse/board/blob/master/src/main/java/com/board/domain/comment/Comment.java
- https://github.com/khghouse/board/blob/master/src/main/java/com/board/domain/comment/CommentHierarchyRepository.java
- https://github.com/khghouse/board/blob/master/src/main/java/com/board/service/comment/CommentService.java
- https://github.com/khghouse/board/blob/master/src/main/java/com/board/service/comment/CommentHierarchySerivce.java
