## 기존 API 호출 방식

- 일반적으로 클라이언트와 서버가 통신할 때 서버가 구현한 API를 호출해 데이터를 송수신함. 따라서 하나의 view를 그리기 위해 API를 여러번 호출해 데이터를 조합해서 사용한다. → 페이지가 복잡해질수록 호출 횟수가 많아지며 처리과정이 들어간다면 데이터 조합이 더 복잡해지게 됨

- 이를 해결하기 위해 Rx와 Flow 같은 데이터 흐름을 만들어 순차 처리 로직을 넣는 방식의 프로그래밍이 사용되고 있지만 map 등으로 데이터를 변환시키키고 데이터를 조합하는 것을 클라이언트에서 처리해주어야 하기 때문에 클라이언트 단의 로직이 복잡해지게 됨 → 로직이 복잡해지게 되면 자연스럽게 유지보수도 어려워짐
- RESP API를 포함한 기존 API 호출 방식은 사용자가 서버에서 정의한 데이터 구조를 한번에 하나씩 가져올 수 있기 때문에 이를 해결하기 위해 클라이언트에서 직접 view에 보여질 쿼리를 만드는 GraphQL이 나타남.

## GraphQL(gql) 이란?

- API를 더욱 빠르고 유연하며 개발자 친화적으로 만들기 위해 설계된 쿼리 언어
- 클라이언트에서 필요한 데이터만을 쿼리할 수 있게해 위의 문제를 직관적으로 해결
- sql은 **데이터베이스 시스템**에 저장된 데이터를 효율적으로 가져오는 것이 목적, gql은 **웹 클라이언트**가 데이터를 서버로 부터 효율적으로 가져오는 것이 목적 → sql의 문장(statement)은 주로 백앤드 시스템에서 작성하고 호출 하는 반면, gql의 문장은 주로 클라이언트 시스템에서 작성하고 호출
- 클라이언트가 자신에게 필요한 query를 선언해 graphQL에 넘기면, GraphQL은 해당 query를 해석해서 서버에서 필요한 데이터를 가져와 클라이언트에 데이터 반환
- 쿼리 예시 (id에 따라 저자의 이름과 이메일을 가져오고 싶을 때)
  ```graphql
  query Author($id: Int) {
    author(id: $id) {
      name
      email
    }
  }
  ```

## Rest API vs GraphQL

- REST API는 URL, METHOD등을 조합하여 다양한 Endpoint가 존재하지만 gql은 단 하나의 Endpoint만 존재한다. (REST API는 여러 Resource에 접근할 때 여러번의 요청이 필요하지만 GraphQL은 한번의 요청으로 다양한 Resource에 접근 가능)
- REST API에서는 각 Endpoint마다 데이터베이스 SQL 쿼리가 달라지는 반면, gql API는 gql 스키마의 타입마다 데이터베이스 SQL 쿼리가 달라진다.
- REST에서는 Resource의 크기와 형태를 서버에서 결정하지만, GraphQL에서는 Resource에 대한 정보만 정의하고, 필요한 크기와 형태는 client단에서 요청 시 결정함.
- REST에서는 URI가 Resource를 나타내고 Method가 작업의 유형을 나타내지만, GraphQL에서는 GraphQL Schema가 Resource를 나타내고 Query, Mutation 타입이 작업의 유형을 나타냄.
- REST에서 각 요청은 해당 엔드포인트에 정의된 핸들링 함수를 호출하여 작업하지만, GraphQL에서는 요청 받은 각 필드에 대한 resolver를 호출하여 작업 처리

- gql API를 사용하면 여러번 네트워크 호출 필요 없이 한번의 네트워크 호출로 처리 가능
- REST API 요청과 응답

  ```json
  [GET] /books/1

  {
    "title": "Romance of the Three Kingdoms",
    "author": {
      "firstName": "Luo",
      "lastName": "Guanzhong"
    }
  }
  ```

- GraphQL 요청

  ```typescript
  # Type 지정 (형태만 지정한 상태)
  type Book {
    id: ID
    title: String
    author: Author
  }
  type Author {
    id: ID
    firstName: String
    lastName: String
    books: [Book]
  }

  # 지정한 형태에 접근할 수 있도록 query 타입이 필요
  type Query {
    book(id: ID!): Book
    author(id: ID!): Author
  }

  # 요청 방법
  /graphql?query={ book(id: "1") { title, author { firstName } } }
  ```

  ```json
  // 응답 데이터
  {
    "title": "Black Hole Blues",
    "author": {
      "firstName": "Janna"
    }
  }
  ```

## 스키마/타입(schema/type)

- 스키마란 **데이터 타입의 집합**으로 클라이언트, 서버 개발자같의 의사소통 비용을 줄이고 빠르게 개발할 수 있게 돕는 API 문서 역할을 담당한다.
- GraphQL의 query 형태는 리턴되는 값과 거의 일치하기 때문에 API 설계 전 사용할 스키마를 정의해야 한다.
- 타입 (Type)

  - 스키마의 핵심 단위로, 커스텀 객체로 작성하여 이를 통해 애플리케이션의 핵심 기능을 알 수 있다.

    ```typescript
    type Character {
      name: String!
      appearsIn: [Episode!]!
    }

    // 오브젝트 타입 : Character
    // 필드 : name, appearsIn
    // 스칼라 타입 : String, ID, Int 등
    // 느낌표(!) : 필수 값을 의미(non-nullable)
    // 대괄호([, ]) : 배열을 의미(array)
    ```

  - 스키마 대부분의 타입은 위와 같은 일반 객체 타입이지만 특수한 2가지 타입인 qeury, mutation 타입이 존재한다.

## Query, Mutation, Subscription

- http 요청을 할 때 GET, POST, DELTE, PATCH 등의 메서드가 제공되는 것처럼 GraphQL 또한 query, mutation, subscription이라는 3가지 요청 방식이 제공됨
- query
  - 읽기 전용 메서드
  - 데이터 변화 필요 없이 단순 조회가 필요할 때 사용
  - 객체 단위로 가져와서 사용 (이름, 변수, 필드 총 3가지 구성요소로 이루어짐)
- mutation
  - 데이터 변경한 후 가져오기 위한 메서드
  - 요청을 통해 데이터 변경할 때 사용
- subscription
  - 실시간으로 변경된 데이터를 가져오기 위한 요청 방식
  - 웹소켓을 통해 소켓 통신을 열어두고 데이터 업데이트 시 알리는 방식

## GraphQL 구조

- gql은 서버와 클라이언트 사이의 GrpahQL이라는 service broker 레이어가 더 들어가, 해당 레이어가 클라이언트에서 쿼리를 선언하면 데이터를 절차적으로 처리하며 이러한 구조로 인해 클라이언트는 서버를 알 필요가 없이 GraphQL 레이어랑만 통신함으로써 데이터를 가져올 수 있다. (기존의 클라이언트에서 데이터를 조합하던 역할이 GraphQL로 옮겨진 것)
- gql에서는 쿼리와 뮤테이션을 나눔 → 데이터를 읽을때는(R) 쿼리를 사용, 데이터를 변조할때는(CUD) 뮤테이션을 사용
- 오퍼레이션 네임 쿼리는 쿼리용 함수라고 생각 → REST API를 호출할때와는 다르게 한번의 인터넷 네트워크 왕복으로 원하는 모든 데이터 가져올 수 있음 (데이터베이스의 프로시져는 DBA 혹은 백엔드프로그래머가 작성, 관리하지만 gql 오퍼레이션 네임 쿼리는 클라이언트 프로그래머가 작성, 관리함

  ```graphql
  # 차이점은 변수를 매개변수로 받느냐 아니냐이다.

  # 일반 쿼리
  {
    human(id: "1000") {
      name
      height
    }
  }

  # 오퍼레이션 네임 쿼리
  query HeroNameAndFriends($episode: Episode) {
    hero(episode: $episode) {
      name
      friends {
        name
      }
    }
  }

  query getStudentInfomation($studentId: ID){
    personalInfo(studentId: $studentId) {
      name
      address1
      address2
      major
    }
    classInfo(year: 2018, studentId: $studentId) {
      classCode
      className
      teacher {
        name
        major
      }
      classRoom {
        id
        maintainer {
          name
        }
      }
    }
    SATInfo(schoolCode: 0412, studentId: $studentId) {
      totalScore
      dueDate
    }
  }
  ```

## 리졸버(resolver)

- 데이터베이스는 데이터를 가져오는 구체적인 과정이 데이터베이스 어플리케이션에 구현되어 있지만 gql에서는 데이터를 가져오는 구체적인 과정을 직접 구현 해야함.
- gql에서 데이터를 가져오는 구체적인 과정인 리졸버가 담당하며 직접 구현 해야하지만 이 과정을 통해 데이터를 데이터베이스, 일반 파일, http와 같이 데이터 source의 종류에 상관 없이 가져 올 수 있다.
- gql 쿼리에서는 각각의 필드마다 함수가 하나씩 존재 한다고 생각하면 되는데 이러한 각각의 함수를 리졸버(resolver)라고 한다.
- 만약 필드가 스칼라 값(문자열이나 숫자와 같은 primitive 타입)인 경우에는 실행이 종료되며 더 이상의 연쇄적인 리졸버 호출이 일어나지 않는다. 하지만 필드의 타입이 스칼라 타입이 아닌 사용자가 정의한 타입이라면 해당 타입의 리졸버를 호출한다.
- 리졸버 함수는 다음과 같이 총 4개의 인자를 받음
  ```graphql
  const resolvers = {
    Query: {
      paymentsByUser: async (parent, { userId }, context, info) => {
          const limit = await Limit.findOne({ where: { UserId: userId } })
          const payments = await Payment.findAll({ where: { LimitId: limit.id } })
          return payments
      },
    },
    Payment: {
      limit: async (payment, args, context, info) => {
        return await Limit.findOne({ where: { id: payment.LimitId } })
      },
    },
  };
  ```
  - 첫번째 인자인 parent는 연쇄적 리졸버 호출에서 부모 리졸버가 리턴한 객체이며, 이 객체를 활용해 현재 리졸버가 내보낼 값을 조절 할 수 있다.
  - 두번째 인자는 args로 쿼리에서 입력으로 넣은 인자이다.
  - 세번째 인자는 context로 모든 리졸버에게 전달이 되며, 주로 미들웨어를 통해 입력된 값들이 들어 있다. (로그인 정보 혹은 권한과 같이 주요 컨텍스트 관련 정보를 가지고 있음)
  - 네번째 인자인 info는 스키마 정보와 더불어 현재 쿼리의 특정 필드 정보를 가지고 있다. (잘 사용하지 않는 필드)

## GraphQL 라이브러리

- 릴레이(Relay)
  - GraphQL을 만든 Facebook이 만듬
  - 아폴로에 비해 더 구조적이며 복잡함
  - 아폴로에 비해 사용하기 까다롭다는 의견이 많음
  - 리액트에서만 사용 가능
  - 복잡한 데이터 요구사항을 가진 큰 규모 어플리케이션에 사용
- 아폴로(Apllo GraphQL)
  - 대부분의 프론트엔드 프레임워크에서 사용 가능
  - 릴레이에 비해 더 유연하고 쉽게 사용 가능
  - 다양한 개발환경을 가지고 있는 프로젝트에 사용

## 정리

- gql의 협업 구조상 프론트엔드쪽에 조금 더 할일이 많아짐
- 에자일(**신속한 반복 작업을 통해 실제 작동 가능한 소프트웨어를 개발하여 지속적으로 제공하기 위한 소프트웨어 개발 방식)**하게 웹사이트 프로젝트를 진행하는데 gql이 도움이 될 것
