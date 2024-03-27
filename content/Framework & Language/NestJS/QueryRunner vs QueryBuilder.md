---
title: QueryRunner vs QueryBuilder
tags:
  - nestjs
---
## Query Runner vs Query Builder

### Query Runner

쿼리 러너는 직접적으로 SQL 쿼리를 실행하는 도구이다. 데이터베이스 트랜잭션 관리가 가능하다. 하지만 엔티티 매퍼와 집적적으로 연결되지 않고, SQL의 직접적인 쿼리를 작성해야 하는 부분 때문에 러닝커브가 큰 단점이 있다. 복잡한 쿼리 작성에 강점을 가진다.

### Query Builder

쿼리 빌더는 객체 지향적인 방식으로 쿼리를 작성하게 하는 도구이다. 다양한 쿼리 조건을 작성할 수 있고, 엔티티 매퍼와 함께 사용하여 쿼리를 작성한다. 정렬, 그룹화 그리고 페이징 기능을 제공한다. 쉽게 작성할 수 있고, 객체지향적인 방식으로 데이터베이스 삽입이 가능하다는 장점을 갖고 있다.

## QueryRunner 구현

쿼리 러너를 통해 쿼리를 직접 작성하는 경우는, 복잡한 쿼리를 직접 작성하기 위함이 크다. 특히 migration에서 자주 쓰이는 듯 하다.

```ts title="migration"
import QueryRunner from 'typeorm'
public async up(queryRunner: QueryRunner): Promise<void> {
    await queryRunner.query(
	    `CREATE TABLE USER (
			id int NotNull AUTO_INCREMENT primarykey,
			name varchar(30) NotNull comment '유저이름',
			...
	    ) engine=InnoDB`
	)
}
```

## QueryBuilder 구현

쿼리 빌더를 사용하기 위해서는 아래 3가지의 방법이 존재한다.

### 1 DataSource

user(사용할 테이블)을 `select()`를 사용하여 명시한다.

```ts title="datasource"
const user = await dataSource
	.createQueryBuilder()
	.select("user")
	.fron(User, "user")
	.where("user.id = :id", {id : userId})
	.getOne()
```

### 2 entity manager

`select`를 사용하지 않고 `QueryBuilder()` 파라미터에 직접 엔티티와 테이블을 명시하고 사용한다.

```ts title="entity manager"
const user = await dataSource.manager
	.createQueryBuilder(User, "user")
	.where("user.id = :id", {id : userId})
	.getOne()
```

### 3 Repository

`getRepository()`의 파라미터에 엔티티를 명시하여 지정한 후, `createQueryBuilder()`에는 테이블을 명시하고 사용한다. 주로 이 방법으로 사용한다.

```ts title="repository"
const user = await dataSource
	.getRepository(User)
	.createQueryBuilder("user")
	.where("user.id = :id", {id : userId})
	.getOne()
```

