---
title: Advanced typeorm
tags:
  - nestjs
---
## 서브 쿼리 

서브 쿼리는 다른 쿼리 내부에 포함되어 있는 SELECT문을 의미한다. 자세한 내용은 [[서브쿼리]]을 참고하면 된다. nestjs에서 서브쿼리는 [[Query Builder]]를 사용하여 구현한다. 서브쿼리는 `from`, `where`, `join` 쿼리 안에서 사용할 수 있는 표현이다. 

```ts title="subQuery"
subQuery(): SelectQueryBuilder<any>;
/**
* Creates SELECT query.
* Replaces all previous selections if they exist.
*/
```

```ts title="example"
const qb = await dataSource.getRepository(Post).createQueryBuilder("post")

const posts = qb
    .where(
        "post.title IN " +
            qb
                .subQuery()
                .select("user.name")
                .from(User, "user")
                .where("user.registered = :registered")
                .getQuery(),
    )
    .setParameter("registered", true)
    .getMany()
// 위와 같은 표현이지만 아래와 같이 작성도 가능하다.
const posts = await dataSource
    .getRepository(Post)
    .createQueryBuilder("post")
    .where((qb) => {
        const subQuery = qb
            .subQuery()
            .select("user.name")
            .from(User, "user")
            .where("user.registered = :registered")
            .getQuery()
        return "post.title IN " + subQuery
    })
    .setParameter("registered", true)
    .getMany()
```

쿼리를 실행하지 않고, 쿼리만 작성한 다음 `.getQuery()`를 통해 사용하려는 구문안에 쿼리를 가져와 사용하는 방법도 있다.

>[!more]
>[typeorm](https://orkhan.gitbook.io/typeorm/docs/select-query-builder#using-subqueries) 공식문서를 기반으로 작성했다.

## findAndCount

```ts title="queryBuilder"
const query = this.userRepository.createQueryBuilder('user')
	.leftJoinAndSelect('post','post')
	.leftJoinAndSelect('post.item', 'item')
	.where({ userName })
	.addWhere({ 'post.regDt' = :regDt }, {regDt})
	.selet()
	.getRawMany()
```

위와 같은 쿼리 빌더로 작성한 쿼리가 있다고 해보자.  쿼리빌더를 사용하면 직접 SQL 쿼리를 사용하여 데이터베이스에 요청하게 된다. 따라서 복잡한 쿼리 작성에 강점을 가진다. 하지만, typeORM을 활용한다면 다음과 같이 `findAndCount`로 사용할 수 있다.

```ts title="typeORM"
const where = {
	userName: userName,
	post : {
		regDt: regDt
	}
}
const {data, total} = await this.userRepository.findAndCount({
	where,
	skip: 1,
	take: perPage,
	order: {[orderKey] : order},
	relations: {
		post : {item: true}
	}
	select: {
		...
	}
})
```

### where

where은 검색하기 위한 조건절이다. where절은 위에서 볼 수 있듯이 key와 value로 구현한다. 만약 `post`객체 안에 `regDt`를 조건으로 하고 싶다면 `where['post.regDt'] = regDt` 가 아니라 `where['post']['regDt'] = regDt`로 구현해야 한다.

### skip, take

`skip`은 선택한 객체의 offset을 설정하는 변수이다. 죽 페이지네이션을 위한 변수이다. `take`는 SQL구문에서 `limit`과 역할이 동일하다. 몇 개를 가져와 선택할 것인지 정한다. 페이지네이션을 위한 변수이므로 둘은 같이 사용된다.

### order

정렬 순서를 나타낸다. 이 역시 객체로 표현을 한다. `order: {name: "ASC", id: "DESC"}`

### relation

relation은 기준 엔티티에서 주변 관계(객체)를 불러올 때 사용한다.(JOIN과 비슷) 역시 마찬가지로 객체로써 표현을 한다. `relations: {post: {item: true}}`