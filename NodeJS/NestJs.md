# NestJS

## 프로젝트 생성 및 실행

- 프로젝트 시작 할 폴더 생성
- 폴더 안에서 nest 기본 구조 생성
  ```jsx
  npm i -g @nestjs/cli
  nest new ./   (현재 폴더에서 생성할때)
  ```
- 앱 실행
  ```jsx
  npm start:dev
  ```

## NestJS 기본 구조

### tslint.json

- 타입스크립트를 위한 린트 (타입스크립트를 쓰는 가이드 라인 제시, 문법에 오류가 나면 알려주는 역할 등)
- 개발자들이 특정한 규칙을 가지고 코드를 깔끔하게 짤수있게 도와주는 라이브러리

### prettierrc

- 코드 형식 맞추는데 사용 (작은 따옴표 or 큰 따옴표 , Indent 값을 2로줄지, 4로 줄지..)
- 에러 찾는 것이 아닌 코드 포맷터 역할

### nest-cli.json

- nest 프로젝트를 위해 특정한 설정을 할 수 있는 json 파일

### tsconfig.json

- 어떻게 타입스크립트를 컴파일 할지 설정

### tsconfig.build.json

- tsconfig.json의 연장선상 파일이며, build를 할 때 필요한 설정들
- “excludes”에서는 빌드할 때 필요 없는 파일들 명시

### package.json

- build : 운영환경을 위한 빌드
- format : 린트에러가 났을시 수정
- start : 앱 시작

### src폴더

- 대부분의 비즈니스 로직이 이 폴더에 들어감
- main.ts : 앱을 생성하고 실행
- app.module.ts : 앱 모듈을 정의?

## 로직 흐름

- 브라우저에서 Request → Controller(app.controller) → Service(app.service) → Controller(app.controller / service에서 리턴값을 받아옴) → 브라우저에 Response 전달

## Nest JS 모듈이란?

- 모듈은 @Module() 데코레이터로 주석이 달린 클래스이며 @Module() 데코레이터는 Nest가 애플리케이션 구조를 구성하는 데 사용하는 메타 데이터를 제공한다.
- 각 응용 프로그램에는 하나 이상의 모듈 (루트 모듈)이 있다. 루트 모듈은 Nest가 사용하는 시작점
- 모듈은 밀접하게 관련된 기능 집합으로 구성 요소를 구성하는 효과적인 방법 (기능별로 만든다 → ex) 유저 모듈, 주문 모듈, 챗 모듈…)
- 같은 기능에 해당하는 것들은 하나의 모듈 폴더안에 넣어 사용한다. (UseController, UserService, UserEntitiy → 다 같은 기능이기에 UserModule안에 넣는다.)
- 모듈은 기본적으로 싱글 톤이므로 여러 모듈간에 쉽게 공급자의 동일한 인스턴스를 공유 할 수 있다. (하나의 모듈을 UserModule, OrderModule, ChatModule에서 공유하듯이 사용 가능하다는 말)

## 모듈 생성하기

- 모듈 생성 명령어

  ```jsx
  nest g module boards

  // nest : using nestcli -> nestcli를 사용
  // g : generate -> 생성하다
  // module : 위의 generate와 이어서 모듈을 생성하겠다는 뜻
  //  boards : 모듈의 이름을 'boards'로 설정 (사용자 임의)
  ```

## 컨트롤러란?

- controller는 들어오는 요청을 처리하고 클라이언트에 응답을 반환
- controller는 @Controller 데코레이터로 클래스를 데코레이션하여 정의된다.
  ```jsx
  @Controller('/boards')
  ```
- 데코레이터는 인자를 Controller에 의해서 처리되는 “경로”로 받는다.
- 핸들러(Handler)는 컨트롤러안에서 @Get, @Post, @Delete 등과 같은 데코레이터로 장식 된 컨트롤러 클래스 내의 단순한 메서드 → 조회, 수정, 생성, 삭제 등
  ```jsx
  @Controller("/boards")
  export class BoardsController {
    @Get() // <- 핸들러 부분
    getBoards(): string {
      return "This action returns all boards";
    }
  }
  ```

## CLI로 명령어 입력 시 Controller 만드는 순서

1. cli는 먼저 해당 모듈 폴더 찾음
2. 해당 모듈 폴더 안에 .controller파일 생성
3. 해당 모듈 폴더 안에 .module파일을 찾음
4. 찾은 .module 파일 안에 controller 넣어줌

## Provider란?

- Nest의 기본 개념으로, 대부분의 기본 Nest 클래스는 서비스, 리포지토리, 팩토리, 헬퍼 등 프로바이더로 취급될 수 있다.
- 프로바이더의 주요 아이디어는 종속성으로 주입할 수 있다는 것
  - ex) Controller A에서 사용 가능하도록 서비스 객체들 (Service A, Service B, Service C)을 넣어줌 → 이때 서비스 객체들이 ‘Provider’
- 즉, 객체는 서로 다양한 관계를 만들 수 있으며 객체의 인스턴스를 “연결”하는 기능은 대부분 Nest 런타임 시스템에 위임될 수 있다.
- Provider 사용하기 위해서는 Nest에 등록해줘야 함
- 등록하기 위해서는 module 파일의 providers 항목안에 해당 모듈에서 사용하고자 하는 Provider를 넣어 주면 된다.

  ```jsx
  import { Module } from "@nestjs/common";
  import { BoardsController } from "./boards.controller";
  import { BoardService } from "./boards.service";

  @Module({
    controllers: [BoardsController],
    providers: [BoardService],
  })
  export class BoardsModule {}
  ```

## Service란?

- 서비스는 소트프웨어 개발내의 공통 개념이며, NestJS, Javascript에서만 쓰이는 개념이 아니다.
- @Injectable 데코레이터로 감싸져서 모듈에 제공되며, 이 서비스 인스턴스는 애플리케이션 전체에서 사용될 수 있다.
- 서비스는 컨트롤러에서 데이터의 유효성 체크를 하거나 데이터베이스에 아이템을 생성하는 등의 작업을 하는 부분을 처리한다.
- service 만들기
  ```jsx
  nest g service boards --no-spec
  // nest : using nestcli -> nestcli를 사용
  // g : generate -> 생성하다
  // service : 위의 generate와 이어서 서비스를 생성하겠다는 뜻
  // boards : 모듈의 이름을 'boards'로 설정 (사용자 임의)
  // --no-spec : 테스트를 위한 소스 코드 생성 x
  ```
- CLI를 이용해 생성한 Service 파일은 Injectable
- service를 controller에서 이용하기위해서는 클래스의 constructor안에서 Dependency Injection이 이뤄져야 함

  ```jsx
  import { Controller } from '@nestjs/common';
  import { BoardsService } from './boards.service';

  @Controller('boards')
  export class BoardsController {
  	constructor(private boardsService: BoardsService) {}
  }

  // 위의 코드는 원래 아래 형식이었지만 private 혹은 public을
  // construct 생성자 안에서 사용할 경우 인수인 파라미터 (여기서는 boardsService)가 암묵적으로 프로퍼티로 선언 됨.
  @Controller('boards')
  export class BoardsController {
  	boardService: BoardsService;

  	constructor(boardsService: BoardsService) {
  		this.boardsService= boardsService;
  	}
  }
  ```

- 기능을 만들기 위해서는 service에서 로직을 처리 후 controller에서 service를 불러와줌

## Model이란?

- 게시물 생성 기능을 만든다고 할 때, 게시물에 어떤 데이터가 필요한지 정의해주기 위해 게시물 데이터의 ID, 이름, 설명 등이 들어간 모델이 필요
- 모델을 정의하기 위해서는 Class or Interface 사용 (인터페이스는 변수의 타입만을 체크, 클래스는 변수의 타입 체크 + 인스턴스 생성 가능)

## Status란?

- 게시물을 예시로 게시물이 공개 게시물인지 아니면 비밀 게시물인지 나누는 것
- 그리고 위의 2가지 상태 외에는 나오지 못하게 타입스크립트의 enumeration 이용
  ```jsx
  export enum BoardStatus { // 아래 2가지 외의 값을 막기 위해 enum 사용
  	PUBLIC = 'PUBLIC',
  	PRIVATE = 'PRIVATE',
  }
  ```

## DTO (Data Transfer Object)란?

- 계층간 데이터 교환을 위한 객체
- DB에서 데이터를 얻어 service나 controller 등으로 보낼 때 사용하는 객체를 말함
- DTO는 데이터가 네트워크를 통해 전송되는 방법을 정의하는 객체
- interface or class 이용해서 정의될 수 있음 (NestJS 공식 문서에선 클래스 이용하는 것을 추천함)
- 사용 이유
  - 데이터 유효성을체크하는데 효율적
  - 더 안정적인 코드로 만들어주며 타입스크립트의 타입으로도 사용됨
- Dto 폴더 생성 후 안에 파일 만들고 controller와 service에 Dto 적용

## Pipe란?

- 파이프는 @Injectable() 데코레이터로 주석이 달린 클래스
- data transformation과 data validation을 위해 사용
  - Data Transformation은 입력 데이터를 원하는 형식으로 변환해줌 ( ex) 문자열 → 정수), 만약 숫자를 받기를 원하는데 문자열 형식으로 데이터가 오면 파이프에서 자동으로 숫자로 바꿔줌 (’7’ → 7)
  - Data validation은 입력 데이터를 평가하고 유효한 경우 변경되지 않은 상태로 전달하면 된다. 그렇지 않으면 데이터가 올바르지 않을 때 예외를 발생시킴, 만약 이름의 길이가 10자 이하여야 하는 상황에서 10장 이상의 이름 데이터가 입력될 경우 에러 발생시킴
- 컨트롤러 경로 처리기에 의해 처리되는 인수에 대해 작동
- Nest는 메소드가 호출되기 직전에 파이프를 삽입하고 파이프는 메소드로 향하는 인수를 수신하고 이에 대해 작동
- Pipe를 사용하는 방법(Binding pipes)은 3가지로 나뉜다.
  - Handler-level Pipes : 핸들러 레벨에서 @UsePipes() 데코레이터를 이용해 사용, 이 파이프는 모든 파라미터에 적용됨
  ```jsx
  @Post()
  @UsePipes(pipe)
  createBoard(   // 모든 파라미터 (title, description)에 적용
  	@Body('title') title,
  	@Body('description') description
  ) {
  }
  ```
  - Parameter-level Pipes : 파라미터 레벨의 파이프 이기 때문에 핸들러 전체가 아닌 특정한 파라미터에게만 적용이 되는 파이프
  ```jsx
  @Post()
  createBoard(   // 모든 파라미터 (title, description)에 적용
  	@Body('title', ParameterPipe) title,
  	@Body('description') description
  ) {
  }
  ```
  - Global-level Pipes : 글로벌 파이프로서 애플리케이션 레벨의 파이프, 클라이언트에서 들어오는 모든 요청에 적용됨, 가장 상단 영역인 main.ts에 넣어줌
  ```jsx
  async function bootstrap() {
    const app = await NestFactory.create(AppModule);
    app.useGlobalPipes(GlobalPipes);
    await app.listen(3000);
  }
  bootstrap();
  ```
- Nest JS에 기본적으로 사용할 수 있게 만들어 놓은 6가지 파이프가 존재 (Built-in Pipes)
  - ValidationPipe
  - ParseIntPipe
  - ParseBoolPipe
  - ParseArrayPipe
  - ParseUUIDPipe
  - DefaultValuePipe

## 유효성 체크

### 필요한 모듈

- class-validator, class-transformer
  ```jsx
  npm install class-validator class-transformer --save
  ```

### 특정 게시물을 찾을 때 없는 경우 결과 값 처리

- 아무 처리하지 않고 없는 아이디의 게시물을 가져오려 하면 결과값으로 아무 내용 없이 돌아옴
- 에러를 표출하기 위해서는 NotFoundException()이라는 예외 인스턴스 사용

  ```jsx
  getBoardById(id: string): Board {
      const found = this.boards.find((board) => board.id === id);

      // 찾으려 하는 게시물이 없을 경우 에러메시지 출력
      if (!found) {
        throw new NotFoundException(`Can't find Board with id ${id}`); // 'NotFoundException'은 NestJS에서 제공, 괄호 안에 원하는 메세지 입력
      }

      return found;
    }
  ```

## 커스텀 파이프 구현 방법

```jsx
import {
  ArgumentMetadata,
  PipeTransform,
} from '@nestjs/common';

// 커스텀 파이프 구현시 항상 'PipeTransform'을 implement 해줘야함
export class BoardStatusValidationPipe implements PipeTransform {
  // 안에는 항상 transform 메서드 필요 , ArgumentMeatdata 타입은 NestJs에서 제공
  transform(value: any, metadata: ArgumentMetadata) {    // value값은 클라이언트에서 보낸 status값 , metadata는 위의 value값에 대한 메타 데이터를 포함한 객체
    // 코드 작성

    return value;
  }
```

- 먼저 PipeTransform이란 인터페이스를 새롭게 만들 커스텀 파이프에 구현해줘야 한다. (PipeTransform 인터페이스는 모든 파이프에서 구현해줘야 하는 인터페이스이다. → NestJS 제공)
- 이와 함께 모든 파이프는 transform() 메소드를 필요로 하며 이 메소드는 NestJS가 인자(arguments)를 처리하기 위해 사용된다.
- transform() 메소드는 2개의 파라미터를 가짐
  - 첫째, 처리가 된 인자의 값(value)
  - 둘째, 인자에 대한 메타 데이터를 포함한 객체
- transform() 메소드에서 Return된 값은 Route 핸들러로 전해지며, 만약 예외 발생시 클라이언트에 바로 전해진다.

## TypeORM이란?

- TypeORM은 node.js에서 실행되고 TypeScript로 작성된 객체 관계형 매퍼 라이브러리이다.
- TypeORM은 MySQL, PostgreSQL, MariaDB, SQLite, MS SQL Server, Oracle, SAP Hana 및 WebSQL과 같은 여러 데이터 베이스를 지원한다.
- 특징 및 이점
  - 모델을 기반으로 데이터베이스 테이블 체계를 자동으로 생성
  - 데이터베이스에서 개체를 쉽게 삽입, 업데이트 및 삭제할 수 있다.
  - 테이블 간의 매핑 (일대일, 일대다 및 다대다)을 만든다.
  - 간단한 CLI 명령을 제공
  - TypeORM은 간단한코딩으로 ORM 프레임 워크를 사용하기 쉽다.
  - TypeORM은 다른 모듈과 쉽게 통합된다.
- 설치 위한 모듈

  ```jsx
  @nestjs/typeorm  // NestJs에서 TypeORM 사용하기 위해 연동시켜주는 모듈

  typeorm  // TypeORM 모듈

  pg  // Postgres 모듈 -> mariadb 사용시 mariadb로 입력

  npm install mariadb typeorm @nestjs/typeorm --save

  // 버전 설정
  npm install @nestjs/typeorm@'원하는 version'

  ```

## ORM (Object Relational Mapping)이란?

- 객체와 관계형 데이터베이스의 데이터를 자동으로 변형 및 연결하는 작업
- ORM을 이용한 개발은 객체와 데이터베이스의 변형에 유연하게 사용할 수 있다.
- TypeORM vs Pure Javascript

  ```jsx
  // TypeORM 코드
  const boards = board.find({title: 'Hello', status: 'PUBLIC'});

  // JS 코드
  db.query('SELECT * FROM boards WHERE title = "Hello" AND status = "PUBLIC", (err, result) =>
  {
  	if(err) {
  		 throw new Error('Error')
  	}
  	boards = result.rows;
  })

  ```

  - 같은 기능을 하는 코드지만 TypeORM의 코드가 훨씬 깔끔함

## TypeORM 애플리케이션 연결

1. typeORM 설정파일 생성
   - src 폴더 밑에 configs 폴더 생성 → typeorm.config.ts 파일 생성
2. typeORM 설정파일(typeorm.config.ts) 작성

   - synchronize true는 production 모드에서는 false로 설정 → 그렇지 않을 경우 데이터를 잃을 위험 있음

   ```jsx
   **import { TypeOrmModuleOptions } from '@nestjs/typeorm';

   export const typeORMConfig: TypeOrmModuleOptions = {
     type: 'mariadb',
     host: '10.0.13.194',
     port: 3306,
     username: 'root',
     password: 'mariadb',
     database: 'board_app_jungho',    // 만든 db이름 작성
     entities: [__dirname + '/../**/*.entity.{js,ts'],
     synchronize: true,
   };**
   ```

   - entities는 나중에 생성할 엔티티 하나씩 넣어 줄 수 있지만 위처럼 작성하면 모든 엔티티를 다 포함하게 됨.
     - 엔티티를 이용해 데이터베이스 테이블 생성하기 때문에 엔티티 파일이 어디에 있는지 설정해줘야함
     - 하나씩 작성하면 entities: [User, Board] User 엔티티와 Board 엔티티를 사용할 수 있게 지정
     - TypeORM을 사용할 때는 데이터베이스 테이블로 변환 되는 Class이기 때문에 클래스를 생성한 후 그 안에 컬럼들을 정의해준다.

3. 루트 Module (app.module.ts)에서 Import해줌

   ```jsx
   @Module({
     imports: [TypeOrmModule.forRoot(typeORMConfig), BoardsModule],
   })
   export class AppModule {}
   ```

## 엔티티 생성 소스 코드

```jsx
import { BaseEntity, Column, Entity, PrimaryGeneratedColumn } from "typeorm";
import { BoardStatus } from "./boards.model";

@Entity()
export class Board extends BaseEntity {
  // NestJS 실행시 자동적으로 아래 column들을 가진 table 생성
  @PrimaryGeneratedColumn()
  id: number;

  @Column()
  title: string;

  @Column()
  description: string;

  @Column()
  status: BoardStatus;
}
```

- @Entity()
  - Entity() 데코레이터 클래스는 Board 클래스가 엔티티임을 나타내는 데 사용된다.
- @PrimaryGeneratedColumn()
  - PrimaryGeneratedColumn() 데코레이터 클래스는 id 열이 해당 엔티티의 기본 키 열임을 나타는데 사용된다.
- @Column()
  - Column() 데코레이터 클래스는 해당 엔티티의 다른 열을 나타내는데 사용된다.

## Repository란?

- 리포지토리는 엔티티 개체와 함께 작동하며 엔티티 찾기, 삽입, 업데이트, 삭제 등을 합니다.
- 데이터베이스 관련 일 (INSERT, FIND, DELETE... 등) 은 서비스에서 하는 것이 아니라 레포지토리에서 수행 (Repository Pattern이라고 부름)
  - 브라우저에서 Request → Controller → Service → Repository → Service → Controller → 브라우저에 Response 전달
- Repository 생성

  1. 리포지토리 파일 생성
     - board.repository.ts
  2. 생성한 파일에 리포지토리를 위한 클래스 생성

     - 생성 시 Repository 클래스를 Extends 해줌 (Find, Insert, Delete 등 엔티티를 컨트롤 해줄 수 있음)
     - @EntityRepository() : 클래스를 사용자 정의 (custom) 저장소로 선언하는데 사용되며, 사용자 지정 저장소는 일부 특정 엔티티를 관리하거나 일반 저장소일 수 있다.

     ```jsx
     import { EntityRepository, Repository } from "typeorm";
     import { Board } from "./board.entity";

     // 엔티티를 컨트롤하기 위해 Repository extends 해줌
     @EntityRepository(Board) // 밑의 class가 Board를 컨트롤하는 리포지토리임을 선언
     export class BoardRepository extends Repository<Board> {}
     ```

  3. 생성한 Repository를 다른곳에서도 사용할 수 있게 하기 위해서 (Injectable) board.module에서 import 해줌

     ```jsx
     @Module({
       imports: [TypeOrmModule.forFeature([BoardRepository])],
       controllers: [BoardsController],
       providers: [BoardsService],
     })
     export class BoardsModule {}
     ```
