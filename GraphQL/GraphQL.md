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
  ```graphql
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
