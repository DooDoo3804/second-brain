---
title: Advanced typeorm
tags:
  - nestjs
---
## 서브 쿼리 

서브 쿼리는 다른 쿼리 내부에 포함되어 있는 SELECT문을 의미한다. 자세한 내용은 [[서브쿼리]]을 참고하면 된다. nestjs에서 서브쿼리는 [[Query Builder]]를 사용하여 구현한다. 서브쿼리는 `from`, `where`, `join` 쿼리 안에서 사용할 수 있는 표현이다. 

```ts
subQuery(): SelectQueryBuilder<any>;
/**
* Creates SELECT query.
* Replaces all previous selections if they exist.
*/
```

```ts title="subQuery"
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

>[!info]
>[typeorm](https://orkhan.gitbook.io/typeorm/docs/select-query-builder#using-subqueries) 공식문서를 기반으로 작성했다.
