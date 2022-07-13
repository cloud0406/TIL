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

## Service에 Repository 넣어주기 (Repository Injection)

```jsx
@Injectable()
export class BoardService {
	constructor(
	@InjectRepository(BoardRepository)
	private boardRepository: BoardRepository,
	) {}
```

- @Injectable() : 이 데코레이터를 이용해 이 서비스에서 BoardRepository를 이용한다고 알려주며 이것을 boardRepository 변수에 넣어준다.

## 게시물 삭제하기 (remove vs delete) : DB연동

### remove()

- 무조건 존재하는 아이템만을 지울 수 있음. 그렇지 않으면 404 에러 발생

### delete()

- 만약 아이템이 존재하면 지우고 존재하지 않으면 아무 영향이 없음

- 이 차이로 인해 remove는 하나의 아이템을 지울 때 두번 데이터베이스를 이용 (아이템 유무 + 지우기), delete는 데이터베이스에 한번만 접근
  ```jsx
  // Service 코드

  async deleteBoard(id: number): Promise<void> {
      const result = await this.boardRepository.delete(id);

      // 만약 db에 없는 id값 전달할 경우 affected값 0으로 나옴 -> 에러메시지로 알려줌
      if (result.affected === 0) {
        throw new NotFoundException(`Can't find Board with id ${id}`);
      }
    }
  ```
  ```jsx
  // Controller 코드

  @Delete('/:id')
    deleteBoard(@Param('id', ParseIntPipe) id): Promise<void> {
      // ParseIntPipe가 id값이 숫자인지 체크하고 아니면 에러 발생
      return this.boardsService.deleteBoard(id);
    }
  ```

## 게시물 상태 업데이트 : DB 연동

```jsx
// Service 코드

async updateBoardStatus(id: number, status: BoardStatus): Promise<Board> {
    const board = await this.getBoardById(id);

    board.status = status;
    await this.boardRepository.save(board);

    return board;
  }
```

```jsx
// Controller 코드

@Patch('/:id/status')
  // 게시물 update할때 status값 바꿔주므로 '유효성 체크'를 해줘야함
  updateBoardStatus(
    @Param('id', ParseIntPipe) id: number,
    @Body('status', BoardStatusValidationPipe) status: BoardStatus,
  ) {
    return this.boardsService.updateBoardStatus(id, status);
  }
```

## 모든 게시물 가져오기

```jsx
// Service 코드

async getAllBoards(): Promise<Board[]> {
    return this.boardRepository.find();
 }
```

```jsx
// Controller 코드

@Get()
  getAllBoard(): Promise<Board[]> {
    return this.boardsService.getAllBoards(); // 연결한 보드를 통해 BoardService의 메서드인 getAllBoards 사용 가능
  }
```

## 인증 기능 구현을 위한 AuthModule 생성

### CLI를 이용한 모듈, 컨트롤러, 서비스 생성

```jsx
nest g module auth
    - auth 모듈 생성
nest g controller auth —no-spec
    - auth 컨트롤러 생성
nest g service auth —no-spec
    - auth 서비스 생성
```

### User를 위한 Entity 생성

- 유저에 대한인증 구현하는 것이므로 유저가 필요 → 유저 데이터를 위한 유저 Entity 생성
  1. user.entity.ts 파일 생성
  2. 파일 소스 코드 작성

### Repository 생성

- User Entity를 생성, 수정, 삭제등의 로직 처리위해 Respository 생성
  1. user.repository.ts 파일 생성
  2. 파일 소스 코드 작성
- 생성된 User Repository를 다른 곳에서도 사용하기 위해 auth module 에서 imports 안에 UserRepository를 넣어준다.

### Repository Injection

- User Repository를 auth Service 안에서 사용하기 위해 유저 리포지토리를 넣어줌

## 유저 데이터 유효성 체크 (Class-validator)

- 유효성 체크를 하기 위해서는 class-validator 모듈을 사용하면 된다.
- Dto 파일에서 Request로 들어노는 값을 정의해주고 있기 때문에 Dto 파일에 값들 하나하나에 class-validator를 이용해서 유효성 조건을 넣는다.
  ```jsx
  export class AuthCredentialsDto {
    @IsString()
    @MinLength(4)
    @MaxLength(20)
    username: string;

    @IsString()
    @MinLength(4)
    @MaxLength(20)
    // 영어랑 숫자만 가능한 유효성 체크
    @Matches(/^[a-zA-Z0-9]*$/, {
      message: "password only accepts english and number",
    })
    password: string;
  }
  ```

### ValidationPipe

- 요청이 컨트롤러에 있는 핸들러로 들어왔을 때 Dto에 있는 유효성 조건에 맞게 체크를 해주려면 ValidationPipe을 넣어줘야 한다.
  ```jsx
  @Controller('auth')
  export class AuthController {
    constructor(private authService: AuthService) {}

    @Post('/signup')
    signUp(
  		// validationpipe를 넣어줘야 유효성 검사 가능
      @Body(ValidationPipe) authCredentialsDto: AuthCredentialsDto,
    ): Promise<void> {
      return this.authService.signUp(authCredentialsDto);
    }
  }
  ```

### 유저 이름에 유니크한 값 주기

1. repository에서 findOne 메소드 사용해서 이미 같은 유저 이름 가진 아이디 있는지 체크 → 없다면 데이터 저장 (이 방법은 데이터 베이스 처리를 2번 해줘야함)
2. 데이터베이스 레벨에서 만약 같은 이름을 가진 유저가 있다면 에러 던져줌

   ```jsx
   @Entity()
   @Unique(["username"]) // @Unique로 username 필드 값을 유니크한 값으로 체크하도록 지정
   export class User extends BaseEntity {
     @PrimaryGeneratedColumn()
     id: number;

     @Column()
     username: string;

     @Column()
     password: string;
   }
   ```

   - 이를 테스트하면 500, ‘Internet server error’가 뜨는데 이유는 NestJs에서 에러가 발생하고 그걸 try catch 구문인 catch에서 잡아주지 않으면 이 에러가 Controller 레벨로 가서 그냥 500 에러를 던짐 → 따라서 반드시 try catch 구문으로 에러를 잡아줘야 함
     ```jsx
     export class UserRepository extends Repository<User> {
       async createUser(authCredentialsDto: AuthCredentialsDto): Promise<void> {
         const { username, password } = authCredentialsDto;
         const user = this.create({ username, password });

         try {
           await this.save(user);
         } catch (error) {
           // console.log('error',error)로 미리 에러 종류 확인 후 해당 에러 발생시 원하는 에러 문구 출력
           if (error.errno === 1062) {
             console.log(error.code);
             throw new ConflictException("Existing username");
           } else {
             console.log(error.code);
             throw new InternalServerErrorException();
           }
         }
       }
     }
     ```

   ## 비밀번호 암호화 하기

   - 유저를 생성할 때 비밀번호가 그대로 저장되는데 이를 암호화 해서 저장하는 것 구현
   - 구현 위해 ‘bcryptjs’라는 모듈 사용
     ```jsx
     npm install bcryptjs --save

     import * as bcrypt from 'bcryptjs';
     ```
   - 비밀번호 데이터베이스 저장 방법
     1. 원본 비밀번호 저장 (하면 안됨)
     2. 비밀번호를 암호화 키(Encryption Key)와 함께 암호화 (양방향)
        - 1234 → 암호화(알고리즘 + 암호화 키) → gUuFwNo4zkMV → 복호화 → 1234
     3. SHA256등 Hash로 암호화해서 저장 (단방향 : 복호화 불가능)
        - 1234 → 해시 → gUuFwNo4zkMV
   - bcryptjs 모듈은 솔트(salt) + 비밀번호(Plain Password)를 해시Hash로 암호화 해서 저장, 암호화할 때 원래 비밀번호에 salt를 붙인 후 해시로 암호화 함 (위의 종류들보다 훨씬 안전)
     - ex) A유저 pw: 1234 ⇒ kenfuduWssw_1234 ⇒ kfowekpfkweosodck
     - ex) B유저 pw: 1234 ⇒ okdowmnxikjd_1234 ⇒ kodkimxjjxhdndnwjdj
     ```jsx
     async createUser(authCredentialsDto: AuthCredentialsDto): Promise<void> {
         const { username, password } = authCredentialsDto;

         const salt = await bcrypt.genSalt(); // 암호화 위한 salt값 생성
         const hashedPassword = await bcrypt.hash(password, salt); // pw + salt 묶어서 암호화

         const user = this.create({ username, password: hashedPassword });
     }
     ```

   ## 로그인 기능 구현

   ```jsx
   // Service 코드

   async signIn(authCredentialsDto: AuthCredentialsDto): Promise<string> {
       const { username, password } = authCredentialsDto;
       const user = await this.userRepository.findOne({ username });

   // 해당 username의 유저가 있는지 체크 후 입력된 비밀번호와 db의 유저 비밀번호 비교
       if (user && (await bcrypt.compare(password, user.password))) {
         return 'login success';
       } else {
         throw new UnauthorizedException('login failed');
       }
     }
   ```

   ```jsx
   // Controller  코드

   @Post('/signin')
     signin(@Body(ValidationPipe) authCredentialsDto: AuthCredentialsDto) {
       return this.authService.signIn(authCredentialsDto);
     }
   ```

   ## JWT (JSON Web Token)란?

   - JWT는 당사자간에 정보를 JSON 개체로 안전하게 전송하기 위한 컴팩트하고 독립적인 방식을 정의하는 개방형 표준(RFC 7519)이다.
   - 간단하게 얘기하면 정보를 안전하게 전할 때 혹은 유저의 권한 같은 것을 체크 하기 위해 사용하는 유용한 모듈이다.
   - 구조
     - Header : 토큰에 대한 메타 데이터 포함(타입, 해싱 알고리즘 - SHA256…)
     - Payload : 유저 정보, 만료 기간, 주제 등
     - Verify Signature : 어떤 식으로든 변경되지 않았는지 확인하는데 사용되는 서명, 서명은 헤더 및 페이로드 세그먼트, 서명 알고리즘, 비밀 또는 공캐 키를 사용하여 생성
   - 사용 흐름
     - 유저 로그인 → 토큰 생성(Hasing 알고리즘) → 토큰 보관
     - 서버에서 요청에서 같이 온 headers와 payload를 가져오고 서버 안에 가지고 있는 Secret을 이용해 Signature 부분을 다시 생성, 둘이 일치하면 통과
   - 필요한 모듈
     ```jsx
     @nestjs/jwt
     	- nestjs에서 jwt를 사용하기 위해 필요한 모듈

     @nestjs/passport
     	- nestjs에서 passport를 사용하기 위해 필요한 모듈

     passport
     	- passport 모듈

     passport jwt
     	- jwt 모듈

     npm install @nestjs/jwt @nestjs/passport passport passport-jwt --save

     ```
   - 어플리케이션에 JWT 모듈 등록
     1. auth 모듈 imports에 넣기
     ```jsx
     @Module({
       imports: [
         // JWT모듈 등록
         JwtModule.register({
           secret: "Secret1234",
           signOptions: {
             expiresIn: 3600, // 3600초 : 1시간
           },
         }),
         TypeOrmModule.forFeature([UserRepository]),
       ],
       controllers: [AuthController],
       providers: [AuthService],
     })
     export class AuthModule {}
     ```
   - 어플리케이션에 Passport 모듈 등록하기
     ```jsx
     @Module({
       imports: [
         // passport 모듈 등록
         PassportModule.register({ defaultStrategy: "jwt" }),

         JwtModule.register({
           secret: "Secret1234",
           signOptions: {
             expiresIn: 3600,
           },
         }),
         TypeOrmModule.forFeature([UserRepository]),
       ],
       controllers: [AuthController],
       providers: [AuthService],
     })
     export class AuthModule {}
     ```

## 로그인 성공 시 JWT를 이용해서 토큰 생성

1.  Service의 SignIn 메소드에 생성 (이전에 Service에서 JWT를 가져와야함)

    ```jsx
    export class AuthService {
      constructor(
        @InjectRepository(UserRepository)
        private userRepository: UserRepository,
        private jwtService: JwtService,
      ) {}
    }
    ```

2.  토큰을 만들기 위해선 Secret과 Payload 필요, Payload에는 자신이 전달하고자 하는 정보 넣는다. (Role, 유저이름, 이메일 등… → 토큰으로 정보 가져갈 수 있기 때문에 민감한 정보는 넣으면 안됨)

    - 이렇게 Payload 이용해서 JWT에서 토큰 만들때 Sign() 메소드 이용

    ```jsx
    // Service 코드

    async signIn(
        authCredentialsDto: AuthCredentialsDto,
      ): Promise<{ accessToken: string }> {
        const { username, password } = authCredentialsDto;
        const user = await this.userRepository.findOne({ username });

        if (user && (await bcrypt.compare(password, user.password))) {
          // 유저 토큰 생성 (Secret + Payload) -> 토큰으로 정보 가져갈 수 있기 때문에 중요한 정보는 입력 x
          const payload = { username };
          // sign() 메소드에 payload 넣어주면 알아서 Secret과 Payload 합쳐서 access 토큰 만들어줌
          const accessToken = await this.jwtService.sign(payload);
          return { accessToken };
        } else {
          throw new UnauthorizedException('login failed');
        }
      }
    ```

    ```jsx
    // Controller 코드

    @Post('/signin')
      signin(
        @Body(ValidationPipe) authCredentialsDto: AuthCredentialsDto,
      ): Promise<{ accessToken: string }> {
        return this.authService.signIn(authCredentialsDto);
      }
    ```

## 토큰 유효성 및 DB에 있는 유저인지 체크

- Verify Signature 부분이 유효한지 먼저 확인
- 유효하다면 Payload에 있는 유저이름 정보 이용해 데이터베이스에서 해당 유저의 정보 가져옴
- 구현 순서
  1. passport-jwt 모듈을 위한 타입 정의 모듈 필요
  ```jsx
  @types/passport-jwt
  ```
  1. jwt.strategy.ts 파일 생성
  ```jsx
  import { Strategy } from 'passport-jwt'; // 기본 'passport'가 아닌 'passport-jwt'에서 strategy import해야함
  import { ExtractJwt } from 'passport-jwt';
  import { User } from './user.entity';
  import { UserRepository } from './user.repository';

  // Jwt strategy를 다른 곳에서도 사용하기 위해 Injectable 사용
  @Injectable()
  // 'passport-jwt' 기능 사용하기 위해 PassportStrategy(Strategy) 상속 받음
  export class JwtStrategy extends PassportStrategy(Strategy) {
    constructor(
      // 토큰 유효성 확인 후 유저 객체 DB에서 가져오기 위해 userRepository 주입
      @InjectRepository(UserRepository)
      private userRepository: UserRepository,
    ) {
  // super는 부모 컴포넌트 사용하기 위해서
      super({
        // 토큰 유효한지 체크용
        secretOrKey: 'Secret1234',
        // 토큰 어디서 가져오는지 (BearerToken 타입인지) 확인
        jwtFromRequest: ExtractJwt.fromAuthHeaderAsBearerToken(),
      });
    }

    async validate(payload) {
      // payload안에 유저 이름이 DB에 있는지 확인
      const { username } = payload;
      const user: User = await this.userRepository.findOne({ username });

      if (!user) {
        throw new UnauthorizedException();
      }
      return user;
    }
  }
  ```
- 위에서 만든 JwtStrategy를 AuthModule 에서 사용하기 위해 AuthModule Providers 항목에 넣어주고 다른 곳에서도 JwtStrategy와 PassportModule도 사용해줘야 하기 때문에 exports 항목에 넣어줌.
  ```jsx
  @Module({
    imports: [
      // 유저 인증위해 사용할 기본 strategy 명시
      PassportModule.register({ defaultStrategy: "jwt" }),
      // jwt 인증 부분을 담당, 주로 sign()을 위한 부분
      JwtModule.register({
        secret: "Secret1234",
        signOptions: {
          expiresIn: 3600, // 3600초 : 1시간, 이후에는 더 이상 토큰이 유효하지 않음
        },
      }),
      TypeOrmModule.forFeature([UserRepository]),
    ],
    controllers: [AuthController],
    // JwtStrategy를 이 Auth 모듈에서 사용할수 있게 등록
    providers: [AuthService, JwtStrategy],
    // JwtStrategy, passportModule을 다른 모듈에서 사용할 수 있게 등록
    exports: [JwtStrategy, PassportModule],
  })
  export class AuthModule {}
  ```
- UseGuards안에 @nestjs/passport에서 가져온 AuthGuard()를 이용하면 요청안에 유저 정보 넣을 수 있음
  ```jsx
  @Post('/test')
  // UseGuards 사용해서 요청안에 유저 객체 정보 넣음
    @UseGuards(AuthGuard()) // 토큰이 없거나 잘못됬으면 "Unauthorized" 에러 메세지를 보냄
    test(@Req() req) {
      console.log('req', req);
    }

  ```
- 위의 코드에서 유저 객체를 얻을 때 ‘req.user’가 아닌 바로 user라는 파라미터로 가져오기 위해선 ‘커스텀 데코레이터’를 이용해야 함
- NestJS에는 여러가지 미들웨어가 다른 목적을 가지며 사용됨
  - Pipes : 요청 유효성 검사 및 페이로드 변환을 위해 만들어짐, 데이터를 예상한대로 직렬화
  - Filters : 오류 처리 미들웨어, 특정 오류 처리기를 사용할 경로와 각 경로 주변의 복잡성을 관리하는 방법을 알 수 있음
  - Guards : 인증 미들웨어, 지정된 경로로 통과할 수 있는 사람과 허용되지 않는 사람을 서버에 알려줌
  - Interceptors : 인터셉터는 응답 매핑 및 캐시 관리와 함께 요청 로깅과 같은 전후 미들웨어, 각 요청 전후에 이를 실행하는 기능은 매우 강력하고 유용하다.
  - 미들웨어가 불러지는 순서
    - middleware → guard → interceptor (before) → pipe → controller → service → controller → interceptor (after) → filter (if applicate) → client

## 커스텀 데코레이션

```jsx
import { createParamDecorator, ExecutionContext } from "@nestjs/common";
import { User } from "./user.entity";

export const GetUser = createParamDecorator(
  (data, ctx: ExecutionContext): User => {
    // request 객체 안에 user정보가 들어있다는 전제하에 사용하므로 controller에서 @UseGuards 를 우선적으로 사용하고 있어야 함
    const req = ctx.switchToHttp().getRequest(); // req에 유저 객체 정보가 담김
    return req.user;
  }
);
```

- 위와 같이 user.decorator 파일 생성해서 사용하면

```jsx
@Post('/test')
  // UseGuards 사용해서 요청안에 유저 객체 정보 넣음
  @UseGuards(AuthGuard()) // 토큰이 없거나 잘못됬으면 "Unauthorized" 에러 메세지를 보냄
  test(@GetUser() user: User) {
    console.log('user', user);
  }
```

- 컨트롤러에서 위와 같이 req정보를 원하는 변수로 사용 가능하다 (UseGuards 사용하고 있어야 함)

## 인증된 유저만 게시물 보고 쓸 수 있게 만들기

### 유저에게 권한 주기

1. 인증에 관한 모듈을 board 모듈에서 쓸 수 있어야 하므로 boardModule에서 인증 모듈 imports 해야함 (이렇게 하면 AuthModule에서 export하는 어떤 거이든 board Module에서 사용 가능)

   ```jsx
   @Module({
     imports: [TypeOrmModule.forFeature([BoardRepository]), AuthModule],
     controllers: [BoardsController],
     providers: [BoardsService],
   })
   export class BoardsModule {}
   ```

2. UseGuards(AuthGuard()) 이용해서 유저가 요청을 줄 때 올바른 토큰을 가지고 요청을 주는지 확인 후 게시물에 접근 권한 줌, AuthGuard는 각각의 라우트 별로 줄 수도 있고 한번에 하나의 컨트롤러 안에 들어있는 모든 라우트에 줄 수도 있음

   ```jsx
   Controller('boards')
   @UseGuards(AuthGuard()) // 컨트롤러 레벨로 줘서 모든 핸들러가 영향 받음
   export class BoardsController {
     constructor(private boardsService: BoardsService) {}
     // 생략..
   ```

## 유저와 게시물의 관계 형성

- 게시물을 생성할 때 어떤 유저가 생성했는지 정보를 같이 넣어줘야 함
  1. 관계 형성을 위해 엔티티에 서로간의 필드를 넣어줘야 함

     1. 유저의 입장에선 OneToMany Relationship : 하나의 유저가 여러 게시판 생성 가능
     2. 게시물의 입장에선 ManyToOne Relationship : 게시글 하나를 여러명이 작성할 순 없으므로
     3. 파라미터는 총 3개 (1. Type, 2. InverseSide : board에서 유저로 접근하려면 board user로 접근해야함, 3. Option : (eager : true 일때는 user 정보를 가져올때 board도 같이 가져옴)

     ```jsx
     // board entity

     @Entity()
     export class Board extends BaseEntity {
       @PrimaryGeneratedColumn()
       id: number;

       @Column()
       title: string;

       @Column()
       description: string;

       @Column()
       status: BoardStatus;

       @ManyToOne((type) => User, (user) => user.boards, { eager: false })
       user: User;
     }
     ```

     ```jsx
     // user entity

     @Entity()
     @Unique(["username"])
     export class User extends BaseEntity {
       @PrimaryGeneratedColumn()
       id: number;

       @Column()
       username: string;

       @Column()
       password: string;

       @OneToMany((Type) => Board, (board) => board.user, { eager: true })
       boards: Board[];
     }
     ```

## 게시물 생성할 때 유저 정보 넣기

- 유저와 게시물의 관계를 엔티티를 이용해 형성해 준 후 실제로 게시물을 생성할 때 유저 정보를 게시물에 넣어줘야 함
  - 게시물 생성 요청 → 헤더안에 있는 토큰으로 유저 정보 → 유저 정보와 게시물 관계 형성하며 게시물 생성
    ```jsx
    // controller 코드
    @Post()
      @UsePipes(ValidationPipe)
      createBoard(
        @Body() CreateBoardDto: CreateBoardDto,
        @GetUser() user: User,
      ): Promise<Board> {
        return this.boardsService.createBoard(CreateBoardDto, user);
      }
    ```
    ```jsx
    // board Repositoy
    export class BoardRepository extends Repository<Board> {
      async createBoard(
        createBoardDto: CreateBoardDto,
        user: User,
      ): Promise<Board> {
        const { title, description } = createBoardDto;

        const board = this.create({
          // this는 현재 리포지토리
          title,
          description,
          status: BoardStatus.PUBLIC,
          user,
        });

        await this.save(board);
        return board;
      }
    ```
